FROM ubuntu:16.04

# install prerequisites
#
COPY ./sources.list.tuna /etc/apt/sources.list

RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y build-essential ca-certificates curl libgd-dev \
        libgeoip-dev libncurses5-dev libperl-dev libreadline-dev \
        libxslt1-dev make perl unzip zlib1g-dev \
        libpcre3-dev libssl-dev wget

# install openresty
# 

# import our GPG key:
RUN wget -qO - https://openresty.org/package/pubkey.gpg | apt-key add -

# for installing the add-apt-repository command
# (you can remove this package and its dependencies later):
RUN apt-get -y install software-properties-common

# add the our official APT repository:
RUN add-apt-repository -y "deb http://openresty.org/package/ubuntu $(lsb_release -sc) main"

RUN apt-get update
RUN apt-get install -y openresty


# install luarocks
#
ARG RESTY_LUAROCKS_VERSION="2.4.3"

RUN cd /tmp \
    && curl -fSL http://luarocks.org/releases/luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz -o luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && tar xzf luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && cd luarocks-${RESTY_LUAROCKS_VERSION} \
    && ./configure \
        --prefix=/usr/local/openresty/luajit \
        --with-lua=/usr/local/openresty/luajit \
        --lua-suffix=jit \
        --with-lua-include=/usr/local/openresty/luajit/include/luajit-2.1 \
    && make build \
    && make install

# cleanup
#
RUN \
    cd /tmp \
    && rm -rf luarocks-${RESTY_LUAROCKS_VERSION} luarocks-${RESTY_LUAROCKS_VERSION}.tar.gz \
    && DEBIAN_FRONTEND=noninteractive apt-get autoremove -y

ENTRYPOINT ["/usr/local/openresty/nginx/sbin/nginx", "-g", "daemon off;"]
