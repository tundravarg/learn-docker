FROM node:12-alpine

# This directory will be in system root
WORKDIR /image-root

# Copy files from current dir to WORKDIR in image
COPY . .

# Run the command
# Each command creates new layer and doesn't run again during building if isn't changed
RUN cd app && yarn install --production

# Run command on docker start (current dir is WORKDIR)
CMD ["node", "./app/src/index.js"]
