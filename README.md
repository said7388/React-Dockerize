# Getting Started Dockerize React App

To build a Docker image, we want to copy all the source files inside the container, build the project (also inside the container) and then serve the `build` folder.

## Building a Docker image

In the project directory, you can run:

### Ignoring Files

Let's start by ignoring the files that we never want to copy to the docker image. For this, we'll create a `.dockerignore` file in the root of the project:

```
// .dockerignore
 node_modules
 build
```

### Docker File

Next, let's define our Docker container by creating a `Dockerfile` in the root of the project:

```
# ==== CONFIGURE =====
# Use a Node latest version base image
FROM node:lts-alpine
# Set the working directory to /app inside the container
WORKDIR /app
# Copy app files
COPY . .
# ==== BUILD =====
# Install dependencies (npm ci makes sure the exact versions in the lockfile gets installed)
RUN npm ci
# Build the app
RUN npm run build
# ==== RUN =======
# Set the env to "production"
ENV NODE_ENV production
# Expose the port on which the app will be running (3000 is the default that `serve` uses)
EXPOSE 3000
# Start the app
CMD [ "npx", "serve", "build" ]
```

### Build and run the application

Ok, let's build the Docker image and run it, to make sure everything works.

```
# Build the Docker image for the current folder
# and tag it with `project-name`
docker build . -t project-name

# Check the image was created
docker images | grep project-name

# Run the image in detached mode
# and map port 3000 inside the container with 3000 on current host
docker run -p 3000:3000 -d project-name
```

Now open the app at http://localhost:3000 - you should see Output 😀
