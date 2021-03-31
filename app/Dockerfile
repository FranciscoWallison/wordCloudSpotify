FROM node:14
RUN apt update && apt install -y mysql-server postgresql postgresql-contrib expect
RUN service postgresql restart
WORKDIR /usr/src/app
RUN yarn
RUN npm install -g nodemon 
RUN yarn global add @vue/cli
RUN yarn global upgrade --latest @vue/cli
RUN yarn global add @vue/cli @vue/cli-service-global

EXPOSE 3333

ENTRYPOINT [ "bash" ]