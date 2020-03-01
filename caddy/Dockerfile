ARG CADDY_VERSION=no-stats
FROM abiosoft/caddy:${CADDY_VERSION}

ARG CADDY_DOMAIN
ENV CADDY_DOMAIN ${CADDY_DOMAIN}

ARG V2RAY_PATH
ENV V2RAY_PATH ${V2RAY_PATH}

RUN mkdir /etc/caddy
COPY Caddyfile /etc/caddy/Caddyfile
RUN sed -i -e "s#yourdomain#$CADDY_DOMAIN#g" \
    -e "s#v2ray_path#$V2RAY_PATH#g" \
    /etc/caddy/Caddyfile

# RUN cat /etc/caddy/Caddyfile

CMD ["--conf", "/etc/caddy/Caddyfile", "--log", "stdout", "--agree=true"]

EXPOSE 80 443 2015

