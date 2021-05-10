---
title: "web开发者在发布你的作品前需要考虑的技术细节"
categories: [ "性能优化","原创翻译" ]
tags: [ "web","SEO","security","performance","technology","bug fixing","developers","basic" ]
draft: false
slug: "technical_details"
date: "2014-08-17 15:42:00"
url: "technical_details.html"
---

这篇文章个人觉得很受用，故仔细翻译了一下，有些地方可能翻译的不好，请见谅！

翻译后发现貌似已经有人翻译过，但是翻译的水平就有点。。。。。那篇文章中不仅忽略了知识点，并且出现了明显的语义和语法错误，有一定的误导作用，所以请务必重新阅读老高的翻译。文章中容易出错的地方老高已经注释（在文章中以【】标出）。

如需转载，请注明出处！

**web开发者在发布你的作品前需要考虑的技术细节
What technical details should a programmer of a web application consider before making the site public!**


原文地址：
[来自stackexchange.com][1]
[quora的回答][2]

## 界面和用户体验

 - 要意识到浏览器的实现标准不一，请确保你的网站在主流浏览器中的正常展现。至少要针对一个最新的[Gecko][3]引擎（[火狐][4]）的浏览器、一个基于Webkit引擎（[Safari][5]或者其他移动端）的浏览器、[Chrome][6]、你想要支持的[IE浏览器][7]（可以借助[IE应用程序兼容性VPC镜像][8]）和[Opera][9]浏览器。同时需要考虑到在不同的操作系统下[浏览器是如何渲染你的网站][10]。
 - 需要考虑来自其他浏览器的用户如何使用你的网站：智能手机、屏幕朗读器和搜索引擎，举个例子。--一些易用性信息：[网页易读性倡议][11]（WAI）和[网站508规范][12]（Section508），移动站开发：[移动互联网开发者论坛][13]（MobiForge）。
 - 构架：如果在不影响用户的情况下部署升级。有一个或者多个用来更改架构、代码或者内容更新的可用测试或运行环境，确保他们部署的可控性，以防止造成破坏。有一个自动化部署方案，用来提交更改到生产环境。最优的解决方案是结合使用一个版本控制系统（CVS, Subversion等【译者注：为啥没GIT】）或一个自动构造机制（Ant，NAnt等）。
 - 不要直接给用户展现不友好的错误信息。
 - 不要用纯文本的形式呈现用户的Email，否则他们会被垃圾邮件骚扰。
 - 给用户生成的链接加上`rel="nofollow"`属性，以避免[SEO作弊][14]。
 - [为你的站点建立合理的限制机制][15]，这一条同时属于安全细则。
 - 理解什么是[渐进增强][16]（progressive enhancement）【译者注：与之相对的是优雅降级（graceful degradation）】。
 - 如果一个请求提交成功，[请重定向至其他地方][17]，防止用户的重复提交。
 - 不要忘了考虑无障碍阅读，这对网站的优化来讲是一个好主意，而且有些情况下他是[法律强制][18]必须有的。 [无障碍网页应用][19]（WAI-ARIA）和[Web内容无障碍指南2][20]（WCAG 2）在这方面可以帮到你！
 - [不要让用户思考该如何操作][21]

## 安全

 - [OWASP开发入门][22]可能会让你花一些时间掌握，但是他涵盖了网站开发安全的方方面面。
 - 了解注入，尤其是[SQL注入][23]，并知道如何防御注入。
 - 永远不要相信用户的输入，还有请求中的所有信息（包括cookie和隐藏域）。
 - 给你的密码[加点盐][24]后在使用哈希，并针对不同行使用不同的盐以防止彩虹攻击。使用慢速【短？】散列算法，如bcrypt（经过时间考验）或者scrypt(更强、比较新)([1][25], [2][26]) ，针对如何保存密码请参考（[如何保存你的密码][27]）。[美国国家标准与技术研究院表示支持使用PBKDF2 算法来加密][28]，[联邦信息处理标准在dotnet开发][29]中也支持PBKDF2([更多信息][30])。避免直接使用MD5和SHA族算法来保存密码。
 - [不要尝试使用自创的自以为NB的认证系统][31]。因为你很容易在细节和无法测试的地方犯错误，也许被黑了以后你才会后知后觉。
 - [了解信用卡的处理规则][32]。（[详见这个问题][33]） 
 - 为登陆页面和任何需要输入敏感信息的页面使用[SSL][34]/[HTTPS][35]（如信用卡信息）。
 - 避免[跨站脚本攻击][36](XSS)。
 - 避免[跨站请求伪造][37](CSRF)。
 - 避免[点击劫持][38]。
 - 使你的系统更新到最新的补丁。
 - 确保你的数据库连接信息是安全的。
 - 时刻注意最新的攻击技术和影响你的平台的漏洞。
 - 阅读[谷歌浏览器安全手册][39]。
 - 阅读[网络产品黑客手册][40]。
 - 考虑[权限最小化原则][41]。试着让你的应用服务器以[非ROOT权限][42]运行（以[tomcat为例][43]）。

## 性能

 - 必要时使用缓存，理解并使用[HTTP缓存技术][44]和[HTML5的Manifest技术][45]。
 - 图片优化 - 不要使用一个20KB大小的的图片最为重复背景。
 - 学习如何使用[gzip压缩内容][46]。
 - 合并/链接多个样式表或多个脚本文件以减少浏览器的请求数，并且使用gzip压缩文件中重复的内容。
 - 看一看[雅虎高性能站点][47]，有很多不错的点子，包括提高前端性能和他们的[YSlow][48]工具（需求使用Firefox、Safira、Chrome、或Opera浏览器）。同时，[Google page speed][49]（[使用浏览器插件][50]）也是一个不错的性能调校工具，他同时也会优化你的图片。
 - 针对小并且相关的图片使用[CSS image sprite技术][51]。
 - 访问量大的站点需要考虑将不用内容[分至不同的域名][52]下。
 - 静态内容（如图片、css、js脚本还有不需要cookie的普通内容）应该被分配到一个[不使用cookie][53]的域名下，因为一个域名下的所有cookie和子域下的cookie将会被包含在所有对应的域名下。一个好的主意是使用CND加速，但是考虑到CND可能会挂，到时候本地的拷贝也会提供服务。
 - 最小化一个浏览器需要渲染一个页面所需要的请求数。
 - 利用工具[Google Closure Compiler][54]最小化你的js文件，还有其他的[最小化工具][55]。
 - 确保有一个`favicon.ico`在服务器的根目录，就算html里没有提到他，[浏览器也会自动请求他][56]。如果你没有一个`favicon.ico`，那么会导致很多的404错误，并浪费带宽。

## SEO

 - 使用搜索引擎友好的URL，如使用`www.phpgao.com/technical_details.html`，而不用`www.phpgao.com/index.php?p=XXX`。
 - 将你的#变为#!，然后再服务端使用$_REQUEST["_escaped_fragment_"]接收google机器人发来的请求，换句话说，google会把`./#!page=1`变为`?_escaped_fragments_=page=1`。同时，针对使用火狐beta4或者Chrome浏览器的用户，使用`history.pushState({"foo":"bar"}, "About", "./?page=1");`，是一个不错的选择。这样的话即使浏览器的地址栏有变化，但是页面不会重新加载。这样就允许你使用`?`代替`#`以保留动态内容，同时告诉服务器当你通过邮件发送的链接到底是什么页面，同时 ajax不需要额外的请求。【这一段如果看不懂：请参考[URL的井号][57]】
 - 不要给你的链接添加诸如**[点我][58]**的说明。这样做是在浪费SEO优化的机会，同时让读者不容易理解。
 - 需要提供一个[XML网站地图][59]，最好使用默认的地址`/sitemap.xml`。
 - 当你有多个URL指向相同的页面，请使用[`<link rel="canonical" ...][60] />`指出你需要强调的页面，这个问题可以在[Google Webmaster Tools][61]被找到。
 - 使用[谷歌站长工具][62]和[Bing站长工具][63]。
 - 一开始就是用[Google分析][64]（或者一个开源分析工具，例如[Piwik][65]）。
 - 了解[robots.txt][66]和搜索引擎爬虫的工作机制。
 - 在(www.phpgao.com)或(phpgao.com)之间选择一个，然后使用301重定向将域名重定向到主域名，以防止分权。【博主选用了带www的域名www.phpgao.com，所以访问不带www的域名phpgao.com会被301重定向】
 - 要知道有一些不守规矩的爬虫。
 - 如果你有一些非文本的内容，可以借助Google's sitemap extensions for video 等工具。

## 技术

 - 理解[HTTP][67]，诸如GET,POST,sessions，cookies还有“无状态”的含义。
 - 使用符合[W3C][68]规则的[XHTML][69]/[HTML][70] + [CSS][71]页面并确保他们[通过验证][72]。此行的目的是避免出现浏览器的怪异模式，同时作为一个奖励，它会更加兼容非传统的浏览器，如屏幕朗读器和手机设备。
 - 理解js在浏览器中如何工作【dom原型】。
 - 理解js、样式表和其他资源如何被载入并解析为一个页面并考虑他们对渲染性能的影响。除非你在分析应用或者使用html5-shims做测试，否则把你的[脚本放在html的最后][73]是一个不错的选择。
 - 理解js的沙盒的工作机制，尤其是你在使用iframes的时候。
 - 意识到js是可以被禁用的，而Ajax也仅仅是一个扩展功能，而不是基准功能。尽管大部分用户多ajax置之不顾，但是要记住[无脚本][74]已经越来越流行，移动设备也可能不会像你期待的那样运行，并且Google在索引你的站点时几乎不会运行你的JS脚本。
 - 了解[301和302重定向的区][75]别（这也是一个SEO论题）
 - 尽可能的掌握你的产品部署平台。
 - 考虑使用[样式表重置][76]或者[`normalize.css`][77].
 - 考虑js框架（[jQuery][78]、[MooTools][79]、[Prototype][80]、[Dojo][81]或[YUI 3][82]），他们会屏蔽很多不同浏览器使用JS操作DOM的差异。【简化操作，屏蔽差异】
 - 同时考虑到渲染性能和js框架，建议使用诸如[Google Libraries API][83]的公共服务库载入框架，好处是浏览器在已经缓存的情况下不需要在从网站下载。【如果每个网站都是用公共库，命中率将大大提高】
 - 不要重复造轮子。再做任何事情之前先google之，看看有没有合适的组建或者已经实现的例子。有99%的几率你需要的功能已经被实现并开源。
 - 另一个方面，在你最终决定需求之前，不要一开始就上很多个库。尤其是针对客户端，最重要的是轻量、快速和灵活。

## Bug修复

 - 你必须知道这个事实，纵观整个软件周期，你将花费20%的时间写代码，剩下80%的时间都在维护他，所以好好码吧！
 - 建立一个良好的错误报告解决方案。
 - 有一个客户反馈系统。
 - 将应用的工作原理写成文档，以便作为将来支持和运维的参考。
 - 有计划行的备份你的站点！（同时确保他们是可恢复的备份）[Ed Lucas的回答][84]中包含了一些建议。建立一个恢复机制，而不是备份机制。
 - 使用版本控制系统保存你的文件，如[Subversion][85]、 [Mercurial][86] 和 [Git][87].
 - 不要忘了做验收检测。Selenium可以帮你的忙。
 - 确保你有足够的空间保存[log4j][88], [log4net][89] 或[log4r][90]生产的日志。如果你的生产环境出错，你可以用日志找到问题的根源。
 - 记录日志时请确保你同时记录了可处理和不可处理的错误。汇报/分析日志，他将会告诉你系统哪里出了问题。



  [1]: http://programmers.stackexchange.com/questions/46716/what-technical-details-should-a-programmer-of-a-web-application-consider-before
  [2]: http://www.quora.com/Web-Development/What-are-5-essential-skills-any-Web-Developer-should-have
  [3]: http://en.wikipedia.org/wiki/Gecko_%28layout_engine%29
  [4]: http://firefox.com/
  [5]: http://www.apple.com/safari/
  [6]: http://www.google.com/chrome
  [7]: http://en.wikipedia.org/wiki/Internet_Explorer
  [8]: http://www.microsoft.com/Downloads/details.aspx?FamilyID=21eabb90-958f-4b64-b5f1-73d0a413c8ef&displaylang=en
  [9]: http://www.opera.com/
  [10]: http://www.browsershots.org/
  [11]: http://www.w3.org/WAI/
  [12]: http://www.section508.gov/
  [13]: http://mobiforge.com/
  [14]: http://en.wikipedia.org/wiki/Nofollow
  [15]: http://www.codinghorror.com/blog/archives/001228.html
  [16]: http://en.wikipedia.org/wiki/Progressive_enhancement
  [17]: http://en.wikipedia.org/wiki/Post/Redirect/Get
  [18]: http://www.section508.gov/
  [19]: http://www.w3.org/WAI/intro/aria
  [20]: http://www.w3.org/TR/WCAG20/
  [21]: http://www.sensible.com/dmmt.html
  [22]: http://www.owasp.org/index.php/Category:OWASP_Guide_Project
  [23]: http://en.wikipedia.org/wiki/SQL_injection
  [24]: http://security.stackexchange.com/q/21263/396
  [25]: http://www.tarsnap.com/scrypt.html
  [26]: http://it.slashdot.org/comments.pl?sid=1987632&cid=35149842
  [27]: http://codahale.com/how-to-safely-store-a-password/
  [28]: http://security.stackexchange.com/q/7689/396
  [29]: http://security.stackexchange.com/a/2136/396
  [30]: http://security.stackexchange.com/questions/211/how-to-securely-hash-passwords
  [31]: http://stackoverflow.com/questions/1581610/how-can-i-store-my-users-passwords-safely/1581919#1581919
  [32]: https://www.pcisecuritystandards.org/
  [33]: http://stackoverflow.com/questions/51094/payment-processors-what-do-i-need-to-know-if-i-want-to-accept-credit-cards-on-m
  [34]: http://www.ruanyifeng.com/blog/2011/03/url_hash.html
  [35]: http://en.wikipedia.org/wiki/Https
  [36]: http://en.wikipedia.org/wiki/Cross-site_scripting
  [37]: http://en.wikipedia.org/wiki/Cross-site_request_forgery
  [38]: http://en.wikipedia.org/wiki/Clickjacking
  [39]: http://code.google.com/p/browsersec/wiki/Main
  [40]: http://amzn.com/0470170778
  [41]: https://en.wikipedia.org/wiki/Principle_of_least_privilege
  [42]: http://security.stackexchange.com/questions/47576/do-simple-linux-servers-really-need-a-non-root-user-for-security-reasons
  [43]: http://tomcat.apache.org/tomcat-8.0-doc/security-howto.html#Non-Tomcat_settings
  [44]: http://www.mnot.net/cache_docs/
  [45]: http://www.w3.org/TR/2011/WD-html5-20110525/offline.html
  [46]: http://developer.yahoo.com/performance/rules.html#gzip
  [47]: http://developer.yahoo.com/performance/
  [48]: http://developer.yahoo.com/yslow/
  [49]: https://developers.google.com/speed/docs/best-practices/rules_intro
  [50]: https://developers.google.com/speed/pagespeed/insights_extensions
  [51]: http://alistapart.com/articles/sprites
  [52]: http://developer.yahoo.com/performance/rules.html#split
  [53]: http://blog.stackoverflow.com/2009/08/a-few-speed-improvements/
  [54]: http://developers.google.com/closure/compiler/
  [55]: http://developer.yahoo.com/yui/compressor/
  [56]: http://mathiasbynens.be/notes/rel-shortcut-icon
  [57]: http://en.wikipedia.org/wiki/Https
  [58]: http://ux.stackexchange.com/questions/12100/why-shouldnt-we-use-the-word-here-in-a-textlink
  [59]: http://www.sitemaps.org/
  [60]: http://googlewebmastercentral.blogspot.com/2009/02/specify-your-canonical.html
  [61]: http://www.google.com/webmasters/
  [62]: http://www.google.com/webmasters/
  [63]: http://www.bing.com/toolbox/webmaster
  [64]: http://www.google.com/analytics/
  [65]: http://piwik.org/
  [66]: http://en.wikipedia.org/wiki/Robots_exclusion_standard
  [67]: http://www.ietf.org/rfc/rfc2616.txt
  [68]: http://www.w3.org/TR/
  [69]: http://www.w3.org/TR/xhtml1/
  [70]: http://www.w3.org/TR/REC-html40/
  [71]: http://www.w3.org/TR/CSS2/
  [72]: http://validator.w3.org/
  [73]: http://developer.yahoo.com/blogs/ydn/posts/2007/07/high_performanc_5/
  [74]: http://noscript.net/
  [75]: http://www.bigoakinc.com/blog/when-to-use-a-301-vs-302-redirect/
  [76]: http://stackoverflow.com/questions/167531/is-it-ok-to-use-a-css-reset-stylesheet
  [77]: http://necolas.github.com/normalize.css/
  [78]: http://jquery.com/
  [79]: http://mootools.net/
  [80]: http://www.prototypejs.org/
  [81]: http://dojotoolkit.org/
  [82]: http://developer.yahoo.com/yui/3/
  [83]: http://developers.google.com/speed/libraries/devguide
  [84]: http://stackoverflow.com/questions/72394/what-should-a-developer-know-before-building-a-public-web-site#73970
  [85]: http://subversion.apache.org/
  [86]: http://mercurial.selenic.com/
  [87]: http://git-scm.org/
  [88]: http://logging.apache.org/log4j/
  [89]: http://logging.apache.org/log4net/
  [90]: http://log4r.rubyforge.org/