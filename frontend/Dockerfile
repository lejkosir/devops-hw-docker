# build stage
FROM node:lts AS build

WORKDIR /app

RUN apt-get update && \
    apt-get install -y openssl git unzip curl && \
    rm -rf /var/lib/apt/lists/*

RUN git clone -b relative https://github.com/MatejBokal/devops-spletna .

RUN mkdir -p /srv/frontend && \
    cp -r code/taprav-fri/frontend/* /srv/frontend && \
    chown -R node:node /srv/frontend

WORKDIR /srv/frontend

RUN npm install

RUN sed -i "s|destination: 'http://localhost:80/taprav-fri/api/:path\\*'|destination: 'http://backend:80/taprav-fri/api/:path*'|" next.config.ts

# http namesto https
RUN sed -i "s|require('https')|require('http')|" /srv/frontend/server.js && \
    sed -i "/const httpsOptions = {/,/};/d" /srv/frontend/server.js && \
    sed -i "s|createServer(httpsOptions,|createServer(|" /srv/frontend/server.js && \
    sed -i "s|HTTPS server running on https|HTTP server running on http|" /srv/frontend/server.js

# samopodpisano SPREMENI!!!
# RUN mkdir -p /etc/ssl/localcerts && \
#     openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
#     -keyout /etc/ssl/localcerts/nextjs.key \
#     -out /etc/ssl/localcerts/nextjs.crt \
#     -subj "/CN=localhost" && \
#     chown node:node /etc/ssl/localcerts/nextjs.key && \
#     chmod 600 /etc/ssl/localcerts/nextjs.key

RUN npm run build


# runtime stage (distroless)
FROM gcr.io/distroless/nodejs20

WORKDIR /srv/frontend

# copy everything needed from build stage
COPY --from=build /srv/frontend /srv/frontend

# ne root (distroless already runs as non-root)
# USER node

# forward
EXPOSE 3000

CMD ["server.js"]
