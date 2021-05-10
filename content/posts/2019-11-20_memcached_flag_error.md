---
title: "一个flag引发的惨案(memcached)"
categories: [ "代码人生" ]
tags: [ "memcache","flag","danga" ]
draft: false
slug: "memcached_flag_error"
date: "2019-11-20 05:42:00"
url: "memcached_flag_error.html"
---

## 起因


老高最近在重构一个服务，流程中有一步需要将数据格式化后放入memcached中，改数据之后会被另一个服务消费。老高使用的Python的`pymemcache`包，调用add方法后没有错误。然后通知另一个JAVA服务来读数据，JAVA使用的包名为`com.danga.MemCached`。该服务接受到消费任务后就来找之前放在memecached中的数据，好的！问题来了。

JAVA用什么方法都读不到Python放进去的数据，但是JAVA自己放自己取是能读到的。
PYTHON同理，能够读到自己设置的数据，但是读取JAVA程序设置的值有乱码。
通过命令行设置的值JAVA读取有问题，Python没问题。

程序猿的直觉告诉我，肯定是库出了问题！

<!--more-->


## 排查

通过之前将的测试结果，八成已经可以确定是库的问题了，但是应该从什么地方着手呢？当然是从源码了！

先把`pymemcache`库的add方法代码贴出来，其中flags引起了老高的注意，虽然是可选参数，但是如果不设置这个值的话，默认为0，而且0值的意义也没有解释，在get方法中也没有具体看出flag的特殊用意！

```python
def add(self, key, value, expire=0, noreply=None, flags=None):
    """
    The memcached "add" command.

    Args:
        key: str, see class docs for details.
        value: str, see class docs for details.
        expire: optional int, number of seconds until the item is expired
                from the cache, or zero for no expiry (the default).
        noreply: optional bool, True to not wait for the reply (defaults to
                self.default_noreply).
        flags: optional int, arbitrary bit field used for server-specific
                flags

    Returns:
        If noreply is True, the return value is always True. Otherwise the
        return value is True if the value was stored, and False if it was
        not (because the key already existed).
    """
    if noreply is None:
        noreply = self.default_noreply
    return self._store_cmd(b'add', {key: value}, expire, noreply,
                            flags=flags)[key]
```

再看看`com.danga.MemCached`中获取key逻辑的部分代码


```java
String cmd = "get " + key + "\r\n";
log.debug("++++ memcache get command: " + cmd);
sock.write(cmd.getBytes());
sock.flush();
Map<String, Object> hm = new HashMap();
this.loadItems(sock, hm, asString);
log.debug("++++ memcache: got back " + hm.size() + " results");
sock.close();
return hm.get(key);

# this.loadItems(sock, hm, asString); 方法

if ((flag & 2) != 0) {
  ....
}

if ((flag & 8) == 0) {
  ....
}
```

果然，java程序在获取key对应的值的时候是通过判断flag的值进行不同的解码工作！搞清楚这个，那么问题就迎刃而解了！不过真的是这样吗？

## 解决

虽然老高找到了原因，但是如何解决这个问题呢？具体还是要看代码！

```java
if ((flag & 2) != 0) {
    try {
        GZIPInputStream gzi = new GZIPInputStream(new ByteArrayInputStream(buf));
        ByteArrayOutputStream bos = new ByteArrayOutputStream(buf.length);
        byte[] tmp = new byte[2048];

        int count;
        while((count = gzi.read(tmp)) != -1) {
            bos.write(tmp, 0, count);
        }

        buf = bos.toByteArray();
        gzi.close();
    } catch (IOException var17) {
        if (this.errorHandler != null) {
            this.errorHandler.handleErrorOnGet(this, var17, key);
        }

        log.error("++++ IOException thrown while trying to uncompress input stream for key: " + key);
        log.error(var17.getMessage(), var17);
        throw new NestedIOException("++++ IOException thrown while trying to uncompress input stream for key: " + key, var17);
    }
}

Object o;
if ((flag & 8) == 0) {
    if (!this.primitiveAsString && !asString) {
        try {
            o = NativeHandler.decode(buf);
        } catch (Exception var15) {
            if (this.errorHandler != null) {
                this.errorHandler.handleErrorOnGet(this, var15, key);
            }

            log.error("++++ Exception thrown while trying to deserialize for key: " + key, var15);
            throw new NestedIOException(var15);
        }
    } else {
        log.info("++++ retrieving object and stuffing into a string.");
        o = new String(buf, this.defaultEncoding);
    }
} else {
    ContextObjectInputStream ois = new ContextObjectInputStream(new ByteArrayInputStream(buf), this.classLoader);

    try {
        o = ois.readObject();
        log.info("++++ deserializing " + o.getClass());
    } catch (ClassNotFoundException var16) {
        if (this.errorHandler != null) {
            this.errorHandler.handleErrorOnGet(this, var16, key);
        }

        log.error("++++ ClassNotFoundException thrown while trying to deserialize for key: " + key, var16);
        throw new NestedIOException("+++ failed while trying to deserialize for key: " + key, var16);
    }
}
```

第一步，如果flag与2!=0，说明原来的数据被压缩，需要先解压。这一步我们需要确保Python放进去的数据是没有经过压缩的。

第二步的流程比较复杂，用过分析，默认流程会调用`o = NativeHandler.decode(buf);`这一句。对应的方法为`decode`，其中b[0]==6就是作为字符串解析。这样的话我们只需要Python在放入数据的时候使用相同的方式即可！

```java
public static Object decode(byte[] b) throws Exception {
    if (b.length < 1) {
        return null;
    } else if (b[0] == 2) {
        return decodeBoolean(b);
    } else if (b[0] == 3) {
        return decodeInteger(b);
    } else if (b[0] == 6) {
        return decodeString(b);
    } else if (b[0] == 5) {
        return decodeCharacter(b);
    } else if (b[0] == 1) {
        return decodeByte(b);
    } else if (b[0] == 7) {
        return decodeStringBuffer(b);
    } else if (b[0] == 9) {
        return decodeShort(b);
    } else if (b[0] == 4) {
        return decodeLong(b);
    } else if (b[0] == 10) {
        return decodeDouble(b);
    } else if (b[0] == 8) {
        return decodeFloat(b);
    } else {
        return b[0] == 11 ? decodeDate(b) : null;
    }
}
```


于是，下面的代码应运而生，完美解决问题，保持了数据兼容性！

```python
b"".join([bytes([6]), str.encode(data)])
```

