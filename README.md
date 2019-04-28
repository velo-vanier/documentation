# Velo Vanier

Welcome to Velo-Vanier. Here are some things to help you get setup and working
quickly.

The production site is located at [http://velo-vanier.s3-website-us-east-1.amazonaws.com/](http://velo-vanier.s3-website-us-east-1.amazonaws.com/)

## Getting Started
1. Get connected with our communication tools
  - [Slack](https://rhokottawa.slack.com) 
    Signup [here](https://rhok.ca/slack-sign-up/)
  - [Trello](https://trello.com/b/gocKz32m/rhok-v%C3%A9lo-vanier-bike-loans) 
    Rose anne will need your user name to add you to the project 
  - [Github](https://github.com/velo-vanier)
    Rose anne will need your user name to add you to the organization 
  - [Google Drive](https://drive.google.com/drive/folders/16bpPqPLWkoC2b_yZw0GBflFc6RV5Aewn)

2. Set up your dev environments

  Velo Vanier is split into two components. You do not need to set up both to 
  run the project. By default the front-end talks to an instance of the api 
  running on AWS

  - [vv-ui](https://github.com/velo-vanier/vv-ui): The React front end 
    - You may need to install [yarn](https://yarnpkg.com/lang/en/docs/install/)

    These instructions will setup the project and start a server. You can see the 
    site at [https://localhost:3000](https://localhost:3000).

    ```sh
    git clone https://github.com/velo-vanier/vv-ui.git
    cd vv-ui
    yarn 
    yarn start
    ``` 
  - [vv-api](https://github.com/velo-vanier/vv-api): The Laravel database API 
    - You will need to [create a dockerhub account](https://hub.docker.com/)  
      and [install docker](https://hub.docker.com/welcome).
    - Note: Running Docker on Windows is quite difficult. The easiest way requires a Pro version of Windows with compatible Intel processor.
    - You also need to install composer: 
    Ubuntu: https://www.digitalocean.com/community/tutorials/how-to-install-and-use-composer-on-ubuntu-18-04
    MacOS: `brew install composer`

    ```sh
    git clone https://github.com/velo-vanier/vv-api.git
    cd vv-api

    docker login
    docker build -f docker/php71-fpm/Dockerfile -t vv-api-fpm .
    cd docker/nginx && docker build -t vv-api-nginx . && cd ../..
    docker build -f docker/data/Dockerfile -t vv-api-data .

    composer install

    docker network create develop
    docker-compose up -d

    cp .env.example .env

    docker-compose run artisan key:generate
    docker-compose run artisan migrate
    ```
    You will be able to see a landing page at [localhost](http://localhost)
    In order to make test requests you must obtain a token. JWT_USER_PASSWORD
    can be found in the .env file.
    Note: Windows does not come with cURL

    ```
    curl -H 'Content-Type: application/json' -d '{ "password": "<JWT_USER_PASSWORD>"}' http://localhost/api/auth/login                                                                                       
    ```
    
    This token must be included as a header in all api requests.
    ```
    Authentication: Bearer <token>
    ```

## Deploying changes 
The production website is hosted on AWS using the account provided and managed 
by the Random Hacks of Kindenss staff. Contact [Brett](https://github.com/tackaberry) 
for credentials.

You will also need to install [awscli](https://docs.aws.amazon.com/cli/latest/userguide/installing.html) 

- VV-UI
The UI is built locally and deployed to an S3 bucket. To deploy changes
make sure you're in your local directory on the correct branch with the 
changes you want to deploy.

```
yarn build
aws s3 sync build s3://velo-vanier
```

- VV-API

_DISCLAIMER:_ I have not run this myself to confirm it works 

This component can be deployed with the [deploy.sh](https://github.com/velo-vanier/vv-api/blob/master/deploy.sh)
script. This script requires the following arguments:

```
PROFILE:
REPO_URL: docker hub repository
REPO_NAME: docker hub repository name
```
