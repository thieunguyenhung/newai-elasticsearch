FROM tomcat:8.0-jre8

ENV TERM xterm
RUN apt-get update && apt-get install -y git
RUN apt-get update && apt-get install -y nano
WORKDIR /home
RUN mkdir workspace
RUN git clone https://github.com/thieunguyenhung/newai-elasticsearch.git
RUN cp /home/workspace/newai-elasticsearch/tomcat_service/newai-spelling-api.war /usr/local/tomcat/webapps/