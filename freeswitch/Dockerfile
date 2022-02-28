FROM debian:buster

# COPY ./modules.conf /modules.conf
#ADD /Users/openstack/src/ThirdParty/FSWITCH/vraovic/freeswitch /usr/src/
ENV APP freeswitch
ENV USER freeswitch
ENV HOME /usr/local/$APP
ENV PATH=/usr/sbin:$PATH

RUN apt-get update && \
apt-get -y install gnupg2 wget lsb-release net-tools && \
wget -O - https://files.freeswitch.org/repo/deb/debian-release/fsstretch-archive-keyring.asc | apt-key add - && \
echo "deb http://files.freeswitch.org/repo/deb/debian-release/ `lsb_release -sc` main" | tee /etc/apt/sources.list.d/freeswitch.list && \
echo "deb-src http://files.freeswitch.org/repo/deb/debian-release/ `lsb_release -sc` main" | tee -a /etc/apt/sources.list.d/freeswitch.list && \
apt-get update && \
apt-get -y build-dep freeswitch && \
\
cd /usr/src/ && \
git clone https://github.com/vraovic/freeswitch.git -bv1.10 freeswitch && \
\
cd freeswitch && \
git config pull.rebase true && \
#Here you can build your Freeswitch on specific commit, it is allways good practice to do so
#git checkout f9990221e6094886066ec2bf9685648135bd405a && \
# cp /modules.conf /usr/src/freeswitch/modules.conf && \
\
./bootstrap.sh -j && \
./configure && \
make && \
make install && \
# create user 'freeswitch'
# add it to group 'freeswitch'
# change owner and group of the freeswitch installation
cd /usr/local && \
groupadd freeswitch && \
adduser --quiet --system --home /usr/local/freeswitch --gecos "FreeSWITCH open source softswitch" --ingroup freeswitch freeswitch --disabled-password && \
chown -R freeswitch:freeswitch /usr/local/freeswitch/ && \
chmod -R ug=rwX,o= /usr/local/freeswitch/ && \
chmod -R u=rwx,g=rx /usr/local/freeswitch/bin/* 

#CMD ["/usr/local/freeswitch/bin/freeswitch", "-nonat"]
# ENV APP freeswitch
# ENV USER freeswitch
# ENV HOME /usr/local/$APP
# ENV PATH=/usr/sbin:$PATH
#ENV FREESWITCH http://localhost:3000

WORKDIR $HOME

#RUN chown -R $USER:$USER ~ /usr/local/freeswitch

SHELL ["/bin/bash", "-lc"]

HEALTHCHECK --interval=15s --timeout=5s \
  CMD fs_cli -x status | grep -q ^UP || exit 1

