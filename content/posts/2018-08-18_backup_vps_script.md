---
title: "VPS网站备份脚本"
categories: [ "服务器技术" ]
tags: [  ]
draft: true
slug: "backup_vps_script"
date: "2018-08-18 08:02:00"
url: "backup_vps_script.html"
---

```bash
#! /bin/bash
#===============================================================================================
#   System Required:  CentOS6.x (32bit/64bit)
#   Description: Server backup and restore script
#   Author: Jalena <jalena@bcsytv.com>
#   Intro:  https://jalena.bcsytv.com/archives/1358
#===============================================================================================

[[ $EUID -ne 0 ]] && echo 'Error: This script must be run as root!' && exit 1

# global variables
current_date=`date +%Y%m%d`
current_time=`date +%Y-%m-%d_%H%M%S`
YESTERDAY=`date +%Y%m%d -d -1days`
# Initialize the database of account information
function initialization(){
	if [[ ! -e '/root/.my.cnf' ]]; then
		echo -e -n "\e[34mPlease enter the MySQL host (Default : \e[33m127.0.0.1\e[34m):\e[0m"
		read MYSQL_HOST
		[[ -z "$MYSQL_HOST" ]] && MYSQL_HOST="127.0.0.1"
		echo -e -n "\e[34mPlease enter the MySQL port (Default : \e[33m3306\e[34m):\e[0m"
		read MYSQL_PORT
		[[ -z "$MYSQL_PORT" ]] && MYSQL_PORT="3306"
		echo -e -n "\e[34mPlease enter the MySQL user (Default : \e[33mroot\e[34m):\e[0m"
		read MYSQL_USER
		[[ -z "$MYSQL_USER" ]] && MYSQL_USER="root"
		echo -e -n "\e[34mPlease enter the MySQL password:\e[0m"
		read MYSQL_PASS
cat > /root/.my.cnf<<EOF
[client]
host=$MYSQL_HOST
port=$MYSQL_PORT
user=$MYSQL_USER
password="$MYSQL_PASS"
[mysqldump]
host=$MYSQL_HOST
port=$MYSQL_PORT
user=$MYSQL_USER
password="$MYSQL_PASS"
EOF
		echo -e "------------------------------------------------------"
		echo -e "MySQL Host = $MYSQL_HOST"
		echo -e "MySQL Port = $MYSQL_PORT"
		echo -e "MySQL User = $MYSQL_USER"
		echo -e "MySQL Pass = $MYSQL_PASS"
		echo -e "------------------------------------------------------"

	fi

	if [[ ! -e '/root/.backup.option' ]]; then
		echo -e -n "\e[34mPlease enter backup path:(Default : \e[33m/tmp/backup\e[34m):\e[0m"
		read BACKUP_DIR
			[[ -z "$BACKUP_DIR" ]] && BACKUP_DIR="/tmp/backup"
		echo -e -n "\e[34mPlease enter the path to the site (Default : \e[33m/home/ftp/phpergao\e[34m):\e[0m"
		read WEB_PATH
			[[ -z "$WEB_PATH" ]] && WEB_PATH="/home/ftp/phpergao"
		echo -e -n "\e[34mPlease enter nginx configuration path (Default : \e[33m/etc/nginx\e[34m):\e[0m"
		read NGINX_PATH
			[[ -z "$NGINX_PATH" ]] && NGINX_PATH="/etc/nginx"
		echo -e -n "\e[34mPlease enter nginx log path (Default : \e[33m/var/log/nginx\e[34m):\e[0m"
		read NGINX_LOG_PATH
			[[ -z "$NGINX_LOG_PATH" ]] && NGINX_LOG_PATH="/var/log/nginx"
cat > /root/.backup.option<<EOF
BACKUP_DIR="$BACKUP_DIR"
WEB_PATH="$WEB_PATH"
NGINX_PATH="$NGINX_PATH"
NGINX_LOG_PATH="$NGINX_LOG_PATH"
EOF
		echo -e "------------------------------------------------------"
		echo -e "Backup directory = \e[33m$BACKUP_DIR\e[0m"
		echo -e "web directory = \e[33m$WEB_PATH\e[0m"
		echo -e "nginx directory = \e[33m$NGINX_PATH\e[0m"
		echo -e "nginx log directory = \e[33m$NGINX_LOG_PATH\e[0m"
		echo -e "------------------------------------------------------"

	fi
}

function initialization_check(){
	if [[ -s '/root/.backup.option' ]]; then
		source /root/.backup.option
	elif [[ -s '/root/.my.cnf' ]]; then
		source /root/.my.cnf
	else
		initialization
	fi

	if [[ ! -d $BACKUP_DIR ]]; then
		mkdir -p ${BACKUP_DIR}
	fi
}

# Backup all database tables
function backup_database(){
	for db in $(mysql -B -N -e 'SHOW DATABASES' |sed -e '/_schema/d' -e '/mysql/d' -e '/sys/d')
		do
			mysqldump ${db} | gzip -9 > ${BACKUP_DIR}/${db}.sql.gz
			echo -e "\t\e[1;32m--- Backup data table \e[1;31m${db} \e[1;32msuccess! ---\e[0m"
	done

	# Pack all database tables
	tar zcPf ${BACKUP_DIR}/sql_${current_date}.tar.gz ${BACKUP_DIR}/*.sql.gz --remove-files
}

# Packing site data
function packing_data(){
	for web in $(ls -1 ${WEB_PATH} |sed -e '/phpMy/d')
	do
		if [[ -d ${WEB_PATH}/${web} ]]; then
			tar zcPf ${BACKUP_DIR}/${web}_${current_date}.tar.gz ${WEB_PATH}/${web}
			echo -e "\t\e[1;32m--- package \e[1;31m${web} \e[1;32msuccess! ---\e[0m"
		fi
	done
}

# package the nginx configuration file && ssl
function configuration(){
	tar zcPf ${BACKUP_DIR}/nginx_${current_date}.tar.gz $NGINX_PATH
	echo -e "\t\e[1;32m--- package \e[1;31mnginx_${current_date}.tar.gz \e[1;32msuccess! ---\e[0m"
	# find / -name nginx.conf |grep -v root | xargs tar zrPf ${BACKUP_DIR}/nginx_${current_date}.tar.gz
	# echo -e "\t\e[1;32m--- Additional file successfully ---\e[0m"
}

# package the nginx configuration file
function accesslog(){
	NGINX_PID=`cat /var/run/nginx.pid`
	# rename
	mv $NGINX_LOG_PATH/error.log $NGINX_LOG_PATH/$YESTERDAY-error.log
	mv $NGINX_LOG_PATH/access.log $NGINX_LOG_PATH/$YESTERDAY-access.log
	if [[ -s $NGINX_LOG_PATH/phpgao.access.log ]]; then
		mv $NGINX_LOG_PATH/phpgao.access.log $NGINX_LOG_PATH/$YESTERDAY-phpgao.access.log
	fi
	tar zcPf ${BACKUP_DIR}/accesslog_${current_date}.tar.gz $NGINX_LOG_PATH
	rm -rf $NGINX_LOG_PATH/*.log
	kill -USR1 $NGINX_PID
	echo -e "\t\e[1;32m--- package \e[1;31maccesslog_${current_date}.tar.gz \e[1;32msuccess! ---\e[0m"
	# find / -name nginx.conf |grep -v root | xargs tar zrPf ${BACKUP_DIR}/nginx_${current_date}.tar.gz
	# echo -e "\t\e[1;32m--- Additional file successfully ---\e[0m"
}

# Upload data
function upload_file(){
	rclone move ${BACKUP_DIR} b:${current_date}/${current_time}
	echo -e "\t\e[1;32m--- rclone move file successfully ---\e[0m"
	# for file in $(ls -1 ${BACKUP_DIR})
	# 	do
	# 		#scp ${file} root@23.239.196.3:/root/backup/${file}
	# done
}

# tar myself
function myself(){
	SCRIPT=$(readlink -f "$0")
	tar zcPf ${BACKUP_DIR}/script_${current_date}.tar.gz ${SCRIPT}
	echo -e "\t\e[1;32m--- backup script file successfully ---\e[0m"
	# for file in $(ls -1 ${BACKUP_DIR})
	# 	do
	# 		#scp ${file} root@23.239.196.3:/root/backup/${file}
	# done
}

# Restore all data
function restore_all(){
	level=`tar -ztPf sql*.tar.gz | grep -o '/' | wc -l`
	num=level-1
	tar zxf sql*.tar.gz --strip-components ${num}
	for db in $(find ${BACKUP_DIR}/*.sql.gz | sed 's/.sql.gz//g')
		do
			mysqladmin create ${db}
			gunzip -f < ${BACKUP_DIR}/${db}.sql.gz | mysql ${db}
	done

	for web in $(ls -1 ${BACKUP_DIR}/*.tar.gz| grep -v mysql |grep -v nginx)
		do
			tar zxPf ${BACKUP_DIR}/${web}
	done

	for nginx in $(ls -1 ${BACKUP_DIR}/nginx*)
		do
			tar zxPf ${BACKUP_DIR}/${nginx}
	done
}

# Initialization settings
function initial_setup(){
	initialization
}

function backup_db(){
	initialization_check
	backup_database
	upload_file
}

# Full backup
function backup_all(){
	initialization_check
	HOUR=`date +%H`
	if [ $HOUR -eq 00 ]
	then
		packing_data
		accesslog
	fi
	myself
	backup_database
	configuration
	upload_file
}

function upload(){
	initialization_check
	upload_file
}

echo -e "\t\e[1;32m--- current time: ${current_time} ---\e[0m"

# Initialization step
action=$1
# [  -z $1 ] && action=backup
case "$action" in
init)
	initial_setup
	;;
backup)
	backup_all
	;;
db)
	backup_db
	;;
up)
	upload
	;;
Restore)
	restore_all
	;;
*)
	echo -e "\n\t\e[1;32mUsage: \e[1;33m./backup.sh init|backup|db|Restore\n\e[0m"
	;;
esac

```