FROM alpine:3.8
RUN apk add --update nginx git && rm -rf /var/cache/apk/*
RUN mkdir -p /tmp/nginx/client-body

COPY nginx/nginx.conf /etc/nginx/nginx.conf
COPY nginx/default.conf /etc/nginx/conf.d/default.conf

RUN git clone https://github.com/hbceylan/wannacrypt_balance.git www 

CMD ["nginx", "-g", "daemon off;"]