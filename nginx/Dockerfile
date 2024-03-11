FROM ubuntu:20.04

RUN apt update

RUN apt install -y curl \
    vim \
    wget \
    build-essential \
    libpcre3 \
    libpcre3-dev \
    zlib1g \
    zlib1g-dev \
    libssl-dev

WORKDIR /app

RUN wget https://nginx.org/download/nginx-1.24.0.tar.gz && \
    tar -zxvf nginx-1.24.0.tar.gz

WORKDIR /app/nginx-1.24.0

RUN ./configure \
    --sbin-path=/usr/bin/nginx \ 
    --conf-path=/etc/nginx/nginx.conf \
    --error-log-path=/var/log/nginx/error.log \
    --http-log-path=/var/log/nginx/access.log \
    --pid-path=/var/run/nginx.pid \
    --with-http_ssl_module \
    --with-http_auth_request_module

RUN make

RUN make install

COPY nginx.conf /app 

RUN mv /app/nginx.conf /etc/nginx/nginx.conf

CMD ["nginx", "-g", "daemon off;"]