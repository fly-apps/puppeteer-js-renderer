# js-renderer

JavaScript is the bane of a web scraper's life. Scraping is all about extracting data from a web page and JavaScript is there adding content, hiding blocks, and moving the DOM around. Just reading the HTML from the server is just not enough. What you ideally want is a way to run all that JavaScript on the page so you can see what's left after that. Then you can get down to some serious scraping.

There are tools to do this out there but most have their own complications or restrictions that stop them from being used out on the edge. Js-renderer-fly has none of those problems and with [Fly](https://fly.io), you can deploy apps close to your users too. At its core, js-renderer-fly is a puppeteer-based service. [Puppeteer](https://pptr.dev/) is a package that renders pages using a headless Chrome instance, executing the JavaScript within the page.

## Quick Start

A typical YouTube page will add views count on it only when the javascript on that page executes. These are the types of use cases where js-renderer-fly comes in very handy. Js-renderer-fly will execute the javascript on the YouTube page and after that pull out the views and the title of the video can be scraped.

Lets get started

### Install js-renderer-fly

To install js-renderer-fly locally, please execute the following commands:

```
git clone git@github.com:geshan/js-renderer-fly.git
cd js-renderer-fly
npm install
```

### Your first scraper

To quickly try js-renderer-fly after installing it, run the command below:

```
node yt-views.js
```

This will show something like below:

```
Pulling views from YouTube, please wait...
Luis Fonsi - Despacito ft. Daddy Yankee has 6,881,463,846 views
```

If you want to try any other video just pass the YouTube video URL as a param to the script like below:

```
node yt-views https://www.youtube.com/watch?v=XqZsoesa55w
```

It should give you an output like below:

```
Pulling views from YouTube, please wait...
Baby Shark Dance | Sing and Dance! | @Baby Shark Official | PINKFONG Songs for Children has 6,077,338,169 views
```

### What just happened here?

To show the YouTube video views count, a small scraper written with [Axrio](https://www.npmjs.com/package/@geshan/axrio) npm package was executed. Axrio combines the popular [Axios](https://www.npmjs.com/package/axios) library and [Cheerio](https://www.npmjs.com/package/cheerio) to create a mini scraper. Axios is used to make requests and Cheerio acts like DOM navigator parsing the markup and giving us an API for traversing/manipulating the resulting data structure. You can kickstart a small scraper with Axrio too.

The [yt-views.js](./yt-views) is a basic scraper which performs a GET request for the given YouTube URL with js-renderer-fly. Using js-renderer-fly will result in getting the final DOM after the page's javascript executes. Then it parses the title and views count out of the rendered page's markup and prints it on the console.q

It used the js-renderer-fly service URL that is already deployed and running on Fly.io. Have a look at "[how to use it as a service](#how-to-use-it-as-a-service)" part for more information. To start your own js-renderer-fly on fly.io jump to the "[how to deploy on fly.io](#how-to-deploy-it-on-flyio)" section.

## Run locally

If you have node installed on your machine, you already cloned this repository and ran `npm install`. Then run `npm start` to get this service running locally.

Next step is to navigate to `http://localhost:8080/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk` on your browser to view the javascript rendered output.

### Run with docker

If you want to run with docker, execute the following after you clone the repository:

```
cd js-renderer-fly
docker-compose up
```

Then hit `http://localhost:8080/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk` on your browser to see the output.

## How to use it as a service

If you want to use it for scraping, use the following URL on Fly.io:

```
https://js-renderer-fly.fly.dev/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk
```

The YouTube URL is an example above, you can use any URL that needs javascript to be executed to be functional.

### Styles broken

Styles and images will look broken but the HTML tags will be there. Happy Web Scraping!

## How to deploy it on fly.io

[Fly.io](https://fly.io) is a platform for applications that need to run globally. It runs your code close to users and scales compute in cities where your app is busiest. Write your code, package it into a Docker image, deploy it to Fly's platform, and let that do all the work to keep your app snappy.

Fly.io has great [documentation](https://fly.io/docs/) to get started. You can find a quick speed run how to get your app running closer to your users with this [guide](https://fly.io/docs/speedrun/). Please follow the following steps to deploy it on fly.io

### Prerequisites

1. [Install](https://fly.io/docs/getting-started/installing-flyctl/) the flyctl CLI command
1. Register on fly with `flyctl auth signup`, if you already have a fly account log in with `flyctl auth login`

### Steps

1. Clone this repo with `git clone git@github.com:geshan/js-renderer-fly.git` if you are logged in the SSH support enabled otherwise try `https://github.com/geshan/js-renderer-fly.git`
1. Then run `cd js-renderer-fly`
1. After that execute `flyctl init --dockerfile` hit return for an app name to be generated (unless there's a name you really want), I ran it with js-renderer-fly as the app name for this example.
1. Subsequently, you can select an organization. Usually this will be your first name-last name on the prompt
1. It should create a fly.toml file in the project root (I have not committed it, it is in .gitignore).
1. Now run `flyctl deploy` to deploy the app -- this will take some time it will build the container, push it, and deploy it. It will build the docker container, push it to the fly docker container registry and deploy it giving out information about the number of instances and their health.
1. Then you can try `flyctl info` it will give the details of the app including hostname.
1. Following that, you can try `flyctl open` and your app will open on the browser. For me, it was opening `https://js-renderer-fly.fly.dev` with a `{mesage: "alive"}` output
1. Add `/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk` on the address bar of your browser after your js-renderer-fly URL. Mine looked like `https://js-renderer-fly.fly.dev/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk` this will render the final DOM of that YouTube page after the javascript is executed.
1. Enjoy!

Again, the YouTube video URL is an example and it can be replaced with other web pages which need javascript to work.

## More fly commands

You can suspend your service with `flyctl suspend` it will pause your service until you resume it. If you try `flyctl status` after suspending it will not show any instances running. Suspending the service means all resources allocated to run the service will be deallocated, resulting in the application running nowhere. To get the instances running again execute `flyctl resume`.

### Fly default resources

I wanted to see what resources were allocated to the App on Fly by default. The scale commands allowed me to find it out pretty easily like below:

1. `flyctl scale show` - showed me VM Size: micro-2x
1. `flyctl scale vm` - showed me micro-2x is a 0.25 CPU cores with 512 MB of memory.

If you want to increase CPU/memory or run more instances in a particular region please refer to the official fly docs on [scaling](https://fly.io/docs/scaling/).

### Flying the app on 3 continents

Our service is running in one data center. For me, it's iad (Ashburn, Virginia) but yours will likely be different based on where you are working from. We can add instances around the world the speed up responses, let's get rolling:

1. To see the regions available run `flyctl platform regions`, I could see regions all over the world from Oregon to Sydney.
1. Let's add an instance to Australia in Sydney, to do this run `flyctl regions add syd`, yes it is that easy.
1. Now check `flyctl status` and you will see an instance running on Sydney
1. Let’s add one more in Europe at Amsterdam with `flyctl regions add ams`, great so we are mostly covered with the app running in 3 continents.
1. Of course, you can run `flyctl status` again to see your app shining on 3 continents.
