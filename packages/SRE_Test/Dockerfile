#Base Build image 
FROM node:alpine as BUILD_IMAGE
ENV NEXT_PUBLIC_APP_VERSION=v1
WORKDIR /app
COPY package*.json /app/

RUN npm install --production 

COPY . .
# build && remove dev dependencies
RUN npm run build && npm prune --production


#Slimmed down version of image for production 
FROM node:16-alpine
ARG NEXT_PUBLIC_APP_VERSION
ENV NEXT_PUBLIC_APP_VERSION=$NEXT_PUBLIC_APP_VERSION

WORKDIR /app

# copy from build image
COPY --from=BUILD_IMAGE /app/package.json ./package.json 
COPY --from=BUILD_IMAGE /app/node_modules ./node_modules
COPY --from=BUILD_IMAGE /app/.next ./.next
COPY --from=BUILD_IMAGE /app/public ./public
COPY --from=BUILD_IMAGE /app/pages ./pages
COPY --from=BUILD_IMAGE /app/styles ./styles

ENV PORT 3000
EXPOSE 3000

# Running the app
CMD "npm" "run" "dev"
