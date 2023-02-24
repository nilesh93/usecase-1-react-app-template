# Stage 1 - Build
FROM node:16-alpine as build-stage

WORKDIR /usr/src/app

COPY package.json  ./

RUN yarn install --silent

COPY . ./

RUN yarn build

# Stage 2 - Nginx reverse proxy
FROM nginx:1.23.3-alpine

LABEL app @project-x/admin-panel

## Copy nginx config
COPY ./nginx/default.conf /etc/nginx/conf.d/

## Remove default nginx website
RUN rm -rf /usr/share/nginx/html/*

COPY --from=build-stage /usr/src/app/build /usr/share/nginx/html

EXPOSE 80

RUN addgroup -g 10014 newgroup && \
    adduser -D -u 10014 -G newgroup newuser

RUN chown -R nginx:nginx /app && \
    chmod -R 755 /app && \
    chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    chown -R nginx:nginx /etc/nginx/conf.d && \
    touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid

USER 10014

CMD ["nginx", "-g", "daemon off;"]