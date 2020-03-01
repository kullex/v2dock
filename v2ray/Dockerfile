FROM v2ray/official

ARG V2RAY_FRONTEND_UUID
ENV FRONTEND_UUID ${V2RAY_FRONTEND_UUID}

ARG V2RAY_BACKEND_UUID
ENV BACKEND_UUID ${V2RAY_BACKEND_UUID}

ARG V2RAY_PORT
ENV V2RAY_PORT ${V2RAY_PORT}

ARG V2RAY_PATH
ENV V2RAY_PATH ${V2RAY_PATH}

COPY config.json /etc/v2ray/config.json
RUN sed -i -e "s#frontend_uuid#$FRONTEND_UUID#g" \
    -e "s#backend_uuid#$BACKEND_UUID#g" \
    -e "s#v2ray_port#$V2RAY_PORT#g" \
    -e "s#v2ray_path#$V2RAY_PATH#g" \
    /etc/v2ray/config.json

# RUN cat /etc/v2ray/config.json

EXPOSE 8188 ${V2RAY_PORT}
