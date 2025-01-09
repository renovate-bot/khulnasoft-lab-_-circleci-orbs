FROM node@sha256:0579ebfe2cd30c3843cd2539b9398a36c9291c4748ef7ccb7dbc9fb3e731d540

RUN mkdir -p /home/node/ && apt-get update && apt-get -y install curl
COPY ./app/ /home/node/app/

# DEV NOTE: remember to re-enable healthcheck and remove debugging port 22 before final push!

# HEALTHCHECK CMD curl --fail http://localhost:8081/ || exit 1
EXPOSE 8081 22

CMD node /home/node/app/server.js
