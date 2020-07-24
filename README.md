# js-renderer

JavaScript is the bane of a web scraper's life. Scraping is all about extracting data from a web page and JavaScript is there adding content, hiding blocks and moving the DOM around. Just reading the HTML from the server is just not enough. What you ideally want is a way to run all that JavaScript on the page so you can see what's left after that. Then you can get down to some serious scraping.

There are tools to do this out there but most have their own complications or restrictions that stop them from being used out on the edge. Js-renderer-fly has none of those problems and with [Fly](https://fly.io), you can deploy apps close to your users too. At its core, js-renderer-fly is a puppeteer-based service. [Puppeteer](https://pptr.dev/) is a package which renders pages using a headless Chrome instance, executing the JavaScript within the page.

## Quick Try

A typical youtube page will add views count on it only when the javascript on that page executes. These are the types of use cases where js-renderer-fly comes in very handy. Js-renderer-fly will execute the javascript in the youtube page and after that pull out the views and title of the video value can be scraped.
As an example, you can run the following command to see the views of "Despacito" apparently the most viewed youtube video:

```
node yt-views.js
```

If you want to try any other video just pass the youtube video URL as a param to the script like below:

```
node yt-views https://www.youtube.com/watch?v=XqZsoesa55w
```

It should give you an output like below:

```
Pulling views from youtube, please wait...
Baby Shark Dance | Sing and Dance! | @Baby Shark Official | PINKFONG Songs for Children  has 6,077,338,169 views
```

It is the second most popular video on youtube.

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

The hit `http://localhost:8080/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk`

## How to use it

If you want to use it for scraping, use the following URL on Fly.io:

https://js-renderer-fly.fly.dev/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk

### Styles broken

Styles and images will look broken but the HTML tags will be there. Happy Web Scraping!

## How to deploy it on fly.io

[Fly.io](https://fly.io) is a platform for applications that need to run globally. It runs your code close to users and scales compute in cities where your app is busiest. Write your code, package it into a Docker image, deploy it to Fly's platform and let that do all the work to keep your app snappy.

Fly.io has great [documentation](https://fly.io/docs/) to get started. You can find a quick speed run how how to get your app running closer to your users with this [guide](https://fly.io/docs/speedrun/). Please follow the following steps to deploy it on fly.io

### Prerequisites

1. [Install](https://fly.io/docs/getting-started/installing-flyctl/) the flyctl CLI command
1. Register on fly with `flyctl auth signup` , if you already have a fly account login with `flyctl auth login`

### Steps

1. Clone this repo with `git clone git@github.com:geshan/js-renderer-fly.git` if you are logged in the SSH support enabled else try `https://github.com/geshan/js-renderer-fly.git`
1. Then run `cd js-renderer-fly`
1. After that execute `flyctl init --dockerfile` hit return for a app name to be generated (unless there's a name you really want), I tried with: `js-renderer-fly`
1. Then select and org, generally it will be your firstname-lastname
1. It should create a fly.toml file in the project root (I have not committed it, it is in .gitignore).
1. Now run `flyctl deploy` to deploy the app -- this will take some time it will build the container, push it and deploy it. It will build the docker container, push it to the fly docker container registery and deploy it giving out information about the number of instances and their health.
1. Then you can try `flyctl info` it will give the details of the app including host name. 
1. Following that, you can try `flyctl open` and your app will open on the browser. For me it was opening `https://js-renderer-fly.fly.dev`
1. To try your specific URL suffix it with `/api/render?url=<your-url>` like `/api/render?url=https://www.youtube.com/watch?v=kJQP7kiw5Fk` as Youtube pages will not render the final DOM with a regular curl.
1. Enjoy!

### Fly default resources

So I wanted to check how much resources were allocated to this app on fly by default. It was easy to know with the following commands

1. `flyctl scale show` - showed me VM Size: micro-2x
1. `flyctl scale vm` - showed me micro-2x is a 0.25 CPU cores with 512 MB of memory.

If you want to increase CPU/memory or run more instances in a particular region please refer to the official fly docs on [scaling](https://fly.io/docs/scaling/).

### More fly commands

You can suspend your service with `flyctl suspend` it will pause your service until you resume it. If you try `flyctl status` after suspend it will not show any isntances running. To get the instances back execute `flyctl resume`.

#### Fly on 3 continents

Now your service is running well in one data center for me it was iad which is `Ashburn, Virginia (US)`. Now let's add some more:

1. To see the regions availble run `flyctl platform regions` , I could see regions all over the world from Oregon to Sydney.
1. Let's add an instance to Australia in Sydney, to do this run `flyctl regions add syd` yes it is that easy.
1. Now check `flyctl status` and you will see an instance running on Sydney
1. Lets add one more in Europe at Amsterdam with `flyctl regions add ams`, great so we are mostly covered with the app running in 3 continents.
1. Of course you can run `flyctl status` again to see your app shining in 3 continents.
