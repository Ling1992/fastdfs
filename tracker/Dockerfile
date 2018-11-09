FROM centos:7

ENV FASTDFS_PATH=/home/Documents \
    FASTDFS_BASE_PATH=/home/yuqing/fastdfs

RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
 && curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
 && yum clean all \
 && yum makecache \
 && yum -y update

RUN yum install -y gcc gcc-c++ \
 && yum install -y make autoconf wget


RUN mkdir -p ${FASTDFS_PATH} \
&& mkdir -p ${FASTDFS_BASE_PATH}

WORKDIR ${FASTDFS_PATH}
RUN wget https://github.com/happyfish100/libfastcommon/archive/V1.0.39.tar.gz \
 && tar zxfv V1.0.39.tar.gz \
 && cd libfastcommon-1.0.39 \
 && ./make.sh \
 && ./make.sh install

WORKDIR ${FASTDFS_PATH}
RUN wget https://github.com/happyfish100/fastdfs/archive/V5.11.tar.gz \
 && tar zxfv V5.11.tar.gz \
 && cd fastdfs-5.11 \
 && ./make.sh \
 && ./make.sh install \
 && cp /etc/fdfs/tracker.conf.sample /etc/fdfs/tracker.conf \
 && cp init.d/fdfs_trackerd /home/ \
 && cp init.d/fdfs_trackerd /home/start.sh 

# nginx 
RUN yum install -y pcre pcre-devel \
 && yum install -y openssl openssl-devel

WORKDIR ${FASTDFS_PATH}
RUN wget http://nginx.org/download/nginx-1.14.1.tar.gz \
 && tar zxfv nginx-1.14.1.tar.gz \
 && cd nginx-1.14.1 \
 && ./configure \
 && make \
 && make install

# php
RUN yum install -y epel-release \
 && rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm \
 && yum install -y php72w-fpm php72w-opcache php72w-gd php72w-mysql php72w-mbstring php72w-cli php72w-devel

#php-fastdfs
WORKDIR ${FASTDFS_PATH}
RUN yum install -y re2c \ 
 && cd fastdfs-5.11/php_client \
 && phpize \
 && ./configure \
 && make \
 && make install \
 && cp fastdfs_client.ini /etc/php.d/ \
 && cp /etc/fdfs/client.conf.sample /etc/fdfs/client.conf

RUN echo -e '#!/bin/sh \n' > /home/start.sh \
 && echo -e '/usr/local/nginx/sbin/nginx  && \n' >> /home/start.sh \
 && echo -e 'php-fpm && \n' >> /home/start.sh \
 && echo -e '/home/fdfs_trackerd restart  && \nfThread=$(ps -ef | grep dfs  | wc -l) \nwhile [ $fThread -le 1 ] \ndo \n      echo $fThread \n      fThread=$(ps -ef | grep fdfs | wc -l) \ndone \ntail -f ${FASTDFS_BASE_PATH}/logs/trackerd.log \n' >> /home/start.sh

RUN yum clean all \
 && rm -rf /var/cache/yum \
 && rm -rf ${FASTDFS_PATH}/*

EXPOSE 22122 8080 80

ENTRYPOINT ["/home/start.sh"]