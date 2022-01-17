<p align="center">
  <h1 align="center">
  Mihai Gherghina SRE Take Home Test
  </h1>
</p>

- [Requirements](#requirements)
- [Logic and end-results](#logic-and-end-results)
- [Getting Started](#getting-started)
- [Folder Structure & Explanation:](#folder-structure--explanation)
- [How to build non-production image:](#how-to-build-non-production-image)
- [How to build production image:](#how-to-build-production-image)
<br/>

## Requirements 

- Docker

## Logic and end-results

Since the pdf instructed me to optimize the production image in various ways, I would like to explain my logic behind what I did and why I did it.
    
* Install node dependencies for production:
  * This step was fairly easy, as using the "--production" flag when installing dependencies and also pruning at the end (just in case) 
          made me confident that the image was as light as possible in its entirety.
        
* Using an appropriate lightweight base image from node, My methods for creating the production container were as follows:

     * Using multi-stage Docker builds (This way, our final image won’t have any unnecessary content except the required artifact).
     
     * Removing development dependencies (as discussed above), and choosing a lightweight base image.  

     * Using a .dockerignore file and ignoring local modules and debug logs from being copied onto Docker image.  (node_modules and npm-debug.log)
    
     * Minimizing the amount of layers to install packages in the Dockerfile to avoid increasing the size of the image.
         * This last step also was essential in optimizing the run steps so that only the layers that change frequently (those being the RUN steps) are towards the bottom while the steps that changed rarely were more towards the top of the dockerfile. I do believe that I could have made the production image even smaller by using a distroless image(i.e gcr.io/distroless), but I was not sure if I was allowed and I decided to stay with the node:alpine images.  

*** End Results were as follows ***
    
     non_production_image_nextjs build time: 22s
     non_production_image_nextjs size: 799MB
     -------------------------------------------
     production_image build time: 8s
     production_image size: 335MB

```bash
REPOSITORY                    TAG       IMAGE ID       CREATED          SIZE
non_production_image_nextjs   latest    1cf772ef1410   2 minutes ago    779MB
production_image              latest    18632066a6be   25 minutes ago   335MB
```


## Getting Started

Export this variable in your local environment, or any other version you would like 

```bash
export NEXT_PUBLIC_APP_VERSION=v1.0.0
```


## Folder Structure & Explanation:
```bash
SRE <-- Root folder 
├── README.md
├── docker-compose.yml <-- docker-compose file for non-production image
└── packages
    └── SRE_Test <-- Folder for most of the production level files & deps
        ├── Dockerfile <-- Production Dockerfile
        ├── Dockerfile.dev <-- Non-production Dockerfile
        ├── next.config.js
        ├── node_modules
        ├── package-lock.json
        ├── package.json
        ├── pages
        ├── public
        └── styles
```
<br/>

## How to build non-production image:
### Important that you are in the Root folder for this to work... That being /SRE ###

```bash
docker-compose -p [nameOfProject] up
```
Example:
```bash
docker-compose -p non_production_image up
```

## How to build production image:
### Important that you are in the SRE_Test folder when running these commands... That being /SRE/packages/SRE_Test/ ###

#### First build the image for production
```bash
docker build --build-arg NEXT_PUBLIC_APP_VERSION=$NEXT_PUBLIC_APP_VERSION -f Dockerfile -t [nameOfImage] .
```

Example:
```bash
docker build --build-arg NEXT_PUBLIC_APP_VERSION=$NEXT_PUBLIC_APP_VERSION -f Dockerfile -t production_image .
```

#### Then run the image. Since the instructions didn't state specific ports for production image, I assumed that it would be up to the user to set them.
```bash
docker run -p [portValue]:[portValue] [nameOfImage] 
```

Example:
```bash
docker run -p 3000:3000 production_image 
```
