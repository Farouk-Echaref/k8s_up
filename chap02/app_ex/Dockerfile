
# start from a node.js 10 image (base image)
FROM node:10

# specify the directory inside the image in which all commands will run (working directory)
WORKDIR /usr/src/app

# copy package files and install dependencies
COPY package*.json ./
RUN npm install

# copy all the app files into the image
COPY . . 

# default command to run when starting the container (diff between CMD and ENTRYPOINT)
CMD ["npm", "install"]