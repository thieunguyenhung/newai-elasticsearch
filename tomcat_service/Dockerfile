FROM tomcat:8.0-jre8

RUN rm -rf /usr/local/tomcat/webapps/examples && \
rm -rf /usr/local/tomcat/webapps/docs && \
rm -rf /usr/local/tomcat/webapps/ROOT && \
rm -rf /usr/local/tomcat/webapps/host-manager && \
rm -rf /usr/local/tomcat/webapps/manager

ENV TERM xterm
RUN apt-get update && apt-get install -y git
RUN apt-get update && apt-get install -y nano
WORKDIR /home
RUN mkdir workspace
WORKDIR /home/workspace