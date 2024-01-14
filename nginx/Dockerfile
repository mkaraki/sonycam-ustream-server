ARG NGINX_VERSION="1.24.0"
ARG DEBIAN_VERSION="bullseye"

FROM debian:${DEBIAN_VERSION} as build

ARG NGINX_VERSION
ARG DEBIAN_VERSION

RUN echo "deb-src http://deb.debian.org/debian ${DEBIAN_VERSION} main InRelease" >> /etc/apt/sources.list
RUN echo "deb-src http://deb.debian.org/debian-security ${DEBIAN_VERSION}-security main InRelease" >> /etc/apt/sources.list
RUN apt-get update && apt-get build-dep -y nginx libnginx-mod-rtmp && apt-get install -y wget tar git

RUN mkdir /build
WORKDIR /build

RUN wget https://nginx.org/download/nginx-$NGINX_VERSION.tar.gz -O nginx-$NGINX_VERSION.tar.gz && tar xvf nginx-$NGINX_VERSION.tar.gz

ARG RTMP_MODULE_GIT_DIR="/build/nginx-rtmp-module"
ARG RTMP_MODULE_BASE_HASH="23e1873aa62acb58b7881eed2a501f5bf35b82e9"

RUN git clone --depth 1 https://github.com/arut/nginx-rtmp-module.git ${RTMP_MODULE_GIT_DIR} && \
    git -C ${RTMP_MODULE_GIT_DIR} reset --hard ${RTMP_MODULE_BASE_HASH}

RUN git config --global user.name "invalid" && git config --global user.email "invalid@example.invalid"

RUN git -C ${RTMP_MODULE_GIT_DIR} remote add transelement-nginx-rtmp-module https://github.com/transelement/nginx-rtmp-module.git && \
    git -C ${RTMP_MODULE_GIT_DIR} fetch transelement-nginx-rtmp-module && \
    git -C ${RTMP_MODULE_GIT_DIR} cherry-pick 162163903da077553b378186cf7dba025d2e5c04


WORKDIR /build/nginx-${NGINX_VERSION}

RUN ./configure --with-compat --add-dynamic-module=${RTMP_MODULE_GIT_DIR}
RUN make modules

FROM nginx:${NGINX_VERSION}-${DEBIAN_VERSION}

ARG NGINX_VERSION
ARG DEBIAN_VERSION

COPY --from=build /build/nginx-${NGINX_VERSION}/objs/ngx_rtmp_module.so /etc/nginx/modules/

RUN mkdir /etc/nginx/modules-enabled/
RUN echo "load_module modules/ngx_rtmp_module.so;" >> /etc/nginx/modules-enabled/ngx_rtmp_module.conf

COPY nginx.conf /etc/nginx/nginx.conf
COPY rtmp_application_settings.conf /etc/nginx/rtmp_application_settings.conf

EXPOSE 80/tcp
EXPOSE 1935/tcp

ENTRYPOINT ["nginx"]
STOPSIGNAL SIGQUIT
CMD []