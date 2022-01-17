<br/>

# Requirements 

- Docker

<br/>

### Getting Started

Export this variable in your local environment, or any other version you would like 

```bash
export NEXT_PUBLIC_APP_VERSION=v1.0.0
```
<br/>

### Folder Structure & Explanation:

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

### How to build non-production image:
** [Important that you are in the Root folder for this to work... That being /SRE] **

```bash
docker-compose up
```

<br/>

### How to build production image:
!**[Important that you are in the SRE_Test folder when running these commands .. i.e /SRE/packages/SRE_Test/]

** First build the image for production
```bash
docker build --build-arg NEXT_PUBLIC_APP_VERSION=$NEXT_PUBLIC_APP_VERSION -f Dockerfile -t [nameOfImage] .
```

** Then run the image. Since the instructions didn't state specific ports for production image, I assumed that it 
** would be up to the user to set them.  To change ports just change the values after -p , i.e "docker run -p [numValue]:[numValue] [nameOfImage]"
```bash
docker run -p 3000:3000 [nameOfImage] 
```
<br/>
