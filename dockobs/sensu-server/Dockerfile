FROM centos:6
MAINTAINER Sebastien GLON <glon.sebastien@free.fr>

ADD sensu.repo /etc/yum.repos.d/sensu.repo
ADD supervisord.conf /etc/supervisord.conf
ADD config.json /etc/sensu/config.json
ADD check_disk.json /etc/sensu/conf.d/check_disk.json
ADD client.json /etc/sensu/conf.d/client.json

RUN yum update -y && yum install -y wget \
 && wget http://packages.erlang-solutions.com/erlang-solutions-1.0-1.noarch.rpm \
 && rpm -Uvh erlang-solutions-1.0-1.noarch.rpm \
 && redhat_release=`cat /etc/redhat-release | awk {'print int($3)'}` \
 && yum install -y erlang logrotate \
 && rpm --import http://www.rabbitmq.com/rabbitmq-signing-key-public.asc \
 && rpm -Uvh http://www.rabbitmq.com/releases/rabbitmq-server/v3.5.6/rabbitmq-server-3.5.6-1.noarch.rpm \
 && rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/i386/epel-release-6-8.noarch.rpm \
 && yum install -y redis sensu supervisor uchiwa

RUN /etc/init.d/rabbitmq-server start && rabbitmqctl add_vhost /sensu \
 && rabbitmqctl add_user sensu secret \
 && rabbitmqctl set_permissions -p /sensu sensu ".*" ".*" ".*" && /etc/init.d/rabbitmq-server stop

ADD uchiwa.json /etc/sensu/uchiwa.json
ADD rabbitmq.config /etc/rabbitmq/rabbitmq.config

ENV RABBITMQ_LOGS=- RABBITMQ_SASL_LOGS=-

VOLUME /etc/sensu/conf.d
VOLUME /var/lib/rabbitmq
VOLUME /data

EXPOSE 3000 4567 5672 15672

CMD ["/usr/bin/supervisord"]



