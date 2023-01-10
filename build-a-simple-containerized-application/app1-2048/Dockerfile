FROM nginx:latest

LABEL maintainer="adrian@cantrill.io" 

COPY 2048 /usr/share/nginx/html

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
