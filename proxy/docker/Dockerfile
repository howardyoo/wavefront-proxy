FROM ubuntu:18.04

# This script may automatically configure wavefront without prompting, based on
# these variables:
#  WAVEFRONT_URL           (required)
#  WAVEFRONT_TOKEN         (required)
#  JAVA_HEAP_USAGE         (default is 4G)
#  WAVEFRONT_HOSTNAME      (default is the docker containers hostname)
#  WAVEFRONT_PROXY_ARGS    (default is none)
#  JAVA_ARGS               (default is none)

# Dumb-init
RUN apt-get -y update
RUN apt-get install -y apt-utils
RUN apt-get install -y curl
RUN apt-get install -y sudo
RUN apt-get install -y gnupg2
RUN apt-get install -y debian-archive-keyring
RUN apt-get install -y apt-transport-https
RUN apt-get install -y openjdk-11-jdk

# Download wavefront proxy (latest release). Merely extract the debian, don't want to try running startup scripts.
RUN echo "deb https://packagecloud.io/wavefront/proxy/ubuntu/ bionic main" > /etc/apt/sources.list.d/wavefront_proxy.list
RUN echo "deb-src https://packagecloud.io/wavefront/proxy/ubuntu/ bionic main" >> /etc/apt/sources.list.d/wavefront_proxy.list
RUN curl -L "https://packagecloud.io/wavefront/proxy/gpgkey" | apt-key add -
RUN apt-get -y update

RUN apt-get -d install wavefront-proxy
RUN dpkg -x $(ls /var/cache/apt/archives/wavefront-proxy* | tail -n1) /

# Clean up APT when done.
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Configure agent
RUN cp /etc/wavefront/wavefront-proxy/log4j2-stdout.xml.default /etc/wavefront/wavefront-proxy/log4j2.xml

# Run the agent
EXPOSE 3878
EXPOSE 2878
EXPOSE 4242

ADD run.sh run.sh
CMD ["/bin/bash", "/run.sh"]
