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
 && cp /etc/fdfs/storage.conf.sample /etc/fdfs/storage.conf \
 && cp init.d/fdfs_storaged /home/ \
 && cp init.d/fdfs_storaged /home/start.sh \
 && echo -e '#!/bin/sh \n/home/fdfs_storaged restart  && \nfThread=$(ps -ef | grep fdfs_storage  | wc -l) \nwhile [ $fThread -le 1 ] \ndo \n      echo $fThread \n      fThread=$(ps -ef | grep fdfs_storage | wc -l) \ndone \ntail -f ${FASTDFS_BASE_PATH}/logs/storaged.log \n' > /home/start.sh

RUN rm -rf /var/cache/yum  \
 && rm -rf ${FASTDFS_PATH}/*

EXPOSE 23000 8888

ENTRYPOINT ["/home/start.sh"]

