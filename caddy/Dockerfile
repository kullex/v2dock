ARG CADDY_VERSION=no-stats
FROM abiosoft/caddy:${CADDY_VERSION}

ARG CADDY_DOMAIN
ENV CADDY_DOMAIN ${CADDY_DOMAIN}

COPY Caddyfile /tmp/Caddyfile
RUN mkdir /etc/caddy && sed "s#yourdomain#$CADDY_DOMAIN#g" /tmp/Caddyfile > /etc/caddy/Caddyfile

CMD ["--conf", "/etc/caddy/Caddyfile", "--log", "stdout", "--agree=true"]

EXPOSE 80 443 2015

