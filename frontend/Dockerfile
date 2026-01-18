# build stage - copies pre-built artifacts
FROM node:lts AS build

WORKDIR /srv/frontend

# Copy pre-built frontend (built by CI/CD, copied to frontend/ directory)
# The entire frontend directory with .next build folder is copied
COPY . /srv/frontend

# No npm install or npm run build needed - already done in CI/CD
# No sed commands needed - already done in CI/CD


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
