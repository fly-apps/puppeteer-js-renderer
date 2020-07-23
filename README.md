# js-renderer

This is an online puppeteer service to render pages with javascript (js). Mainly useful for web scraping (not using splash).

At times while scraping web pages you will come across websites or web pages that only render on a browser that renders the loaded javascript. If you curl it or use something like [Scrapy](https://scrapy.org/), you just end up with not useful HTML.

This project aims to solve that issue with Puppeteer. With Scrapy you can use [Splash](https://github.com/scrapy-plugins/scrapy-splash) but it is Scrapy specific and not easy to configure.

## Uses

This project uses Puppeteer to render the page as a full browser and Express to open Puppeter as an API.

## Run locally

If you have node install you can do:

```
npm install
npm start
```

If you want to run with docker, execute the following:

```
npm install
docker-compose up
```

The hit `http://localhost:8080/api/render?url=https://instagram.com`

## How to use it

If you want to use it for scraping, use the following URL on Fly.io:

https://js-renderer.fly.dev/api/render?url=https://instagram.com

### Styles broken

Styles and images will look broken but the HTML tags will be there. Happy Web Scraping!

## How to deploy it on fly.io

Fly is a platform for applications that need to run globally. It runs your code close to users and scales compute in cities where your app is busiest. Write your code, package it into a Docker image, deploy it to Fly's platform and let that do all the work to keep your app snappy.

Fly.io has great [documentation](https://fly.io/docs/) to get started. You can find a quick speed run how how to get your app running closer to your users with this [guide](https://fly.io/docs/speedrun/). Please follow the following steps to deploy it on fly.io

1. [Install](https://fly.io/docs/getting-started/installing-flyctl/) the flyctl CLI command
1. Register on fly with `flyctl auth signup` , if you already have a fly account login with `flyctl auth login`
1. Clone this repo with `git clone git@github.com:geshan/js-renderer-fly.git`
1. Then run `cd js-renderer-fly`
1. After that execute `flyctl init`
1. Then type in a name like `js-renderer-fly`
1. Then select and org, generally it will be your firstname-lastname
1. After that, select `Dockerfile` as the builder
1. It should create a fly.toml file in the project root (I have not committed it, it is in .gitignore). Below is a screenshot of `flyctl init` output I got:

![Flyctl init output for js-renderer](imgs/01fly-init.png?raw=true)

1. Now run `flyctl deploy` to deploy the app -- this will take some time it will build the container, push it and deploy it. Below is a screenshot after `flyctl deploy` ended

![Flyctl deploy output for js-renderer](imgs/02fly-deploy.png?raw=true)

1. Then you can try `flyctl info` - it will give the details of the app including host name. In addition to it, some more details will be added to your `fly.toml` file like the internal port of the container, service's concurrency and timeouts.

1. Following that, you can try `flyctl open` and your app will open on the browser. For me it was opening `https://js-renderer-fly.fly.dev` 
