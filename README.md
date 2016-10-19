# Dockerfile based on alpine-node image + libsass + yarn + pm2
(and dependencies: gcc, g++, make, node-gyp, python, git)

```dockerfile
FROM mhart/alpine-node:6.7.0

# Install required dependencies (Alpine Linux packages)
RUN \
  apk add --no-cache \
  g++ \
  gcc \
  make \
  git \
  python \
  && git clone https://github.com/sass/sassc \
  && cd sassc \
  && git clone https://github.com/sass/libsass \
  && SASS_LIBSASS_PATH=/sassc/libsass make

# install
RUN mv sassc/bin/sassc /usr/bin/sass

# cleanup
RUN rm -rf /var/cache/apk/* && rm -rf /sassc

# Install (global) NPM packages/dependencies
RUN npm install --ignore-optional -g \
  node-gyp \
  pm2 \
  yarn

WORKDIR /src

ADD . .

RUN yarn --ignore-optional

RUN npm run build

# Expose SERVER ports
EXPOSE 8080

# Specify default CMD
CMD ["npm", "run", "start:prod"]
```
