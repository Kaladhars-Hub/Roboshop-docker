FROM node:20.19.5-alpine3.21 AS build
# alpine = tiny Linux (5MB vs 200MB!)
# AS build = name this stage "build"

WORKDIR /opt/server
# set working directory

COPY package.json .
COPY *.js .
# copy app files into image

RUN npm install
# install dependencies — happens in build stage only

FROM node:20.19.5-alpine3.21
# fresh clean image for production

RUN addgroup -S roboshop && adduser -S roboshop -G roboshop
# create non-root user and group — security! ✅
# remember USER instruction we practiced!

chown -R roboshop:roboshop /opt/server
# give roboshop user ownership of /opt/server

EXPOSE 8080
# catalogue listens on port 8080

LABEL com.project="roboshop" \
      component="catalogue" \
      created_by="sivakumar"
# metadata — remember LABEL instruction!

ENV MONGO="true" \
    MONGO_URL="mongodb://mongodb:27017/catalogue"
# mongodb = container name on roboshop network
# Docker resolves name to IP automatically!

COPY --from=build --chown=roboshop:roboshop /opt/server /opt/server
# copy built files FROM stage 1 INTO stage 2
# --chown gives ownership to roboshop user

USER roboshop
# run as non-root user — security! ✅

CMD ["server.js"]
ENTRYPOINT ["node"]
# together runs: node server.js ✅