# STAGE I Build App
FROM node:14.5-alpine3.12 as build

# Set working directory
WORKDIR /usr/src/app

# Install app dependencies
COPY ./react/package*.json ./
RUN npm ci

# Copy app files
COPY ./react . 

# Build the app
RUN npm run build

# STAGE II Run App
FROM nginx:1.19.1-alpine

# Copy files from build
COPY --from=build /usr/src/app/public/* /usr/share/nginx/html/

# Run service
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
