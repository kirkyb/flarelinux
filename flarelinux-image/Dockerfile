FROM asclinux/linuxforphp:8.2

MAINTAINER Andrew Caya <andrewscaya@yahoo.ca>

USER root

WORKDIR /root

# bind-utils setup
RUN lfphp-get bind-utils
RUN cd && rm libuv-v1.38.1-lfphp.tar.gz krb5-1.17-lfphp.tar.gz bind-9.16.5-lfphp.tar.gz

# Node 10.24.1 setup
RUN lfphp-get --force node.js-10
RUN npm install --global yarn
RUN cd && rm which-2.21-lfphp.tar.gz node-v10.24.1-linux-x64.tar.xz

# Golang setup
RUN lfphp-get golang
RUN cd && rm go1.15.5.linux-amd64.tar.gz

# jq setup
RUN lfphp-get jq

# libtirpc and lsof setup
RUN lfphp-get lsof
RUN cd && rm libtirpc-1.3.1.tar.bz2 lsof_4.91.tar.gz

# Flare Network setup
RUN groupadd flare && useradd -g flare flareuser && cp -rf /etc/skel /home/flareuser && chown -R flareuser:flare /home/flareuser && echo "flareuser:flarepass" | chpasswd
RUN sed -i 's/GOPATH=\/root\/go/GOPATH=\/home\/go/' /etc/bashrc
RUN mv /root/go /home
RUN echo 'export XRP_APIs="https://xrpl.flare.network:443,https://xrpl-1.flare.network:443,https://xrplcluster.com"' >> /etc/bashrc
RUN cd /home && git clone https://gitlab.com/flarenetwork/flare
# Checking out last successful build of Coreth (Go module) - CURRENT HEAD is 43335956
RUN export GOGC=90 && cd /home/flare && git checkout d00ed147e4bca7b2e3054d98c534f0a47bcd606f && source /etc/bashrc && sed -i 's/read/#read/' local.sh && ./local.sh && sed -i 's/#read/read/' local.sh && cd client && yarn && chgrp -R flare /home/go && chmod -R 775 /home/go && chown -R flareuser:flare /home/flare

COPY ./flare /usr/bin

# Change root password
RUN echo "root:toor" | chpasswd

WORKDIR /home/flare

USER flareuser

CMD ["/usr/bin/flare"]
