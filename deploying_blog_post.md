# Lessons Learned Deploying Code used by over 15 million Websites

Step 1. Realize you have a problem.

Step 2. Solve it

Step 3. Profit

Over the past year, the engineers on our Publisher team have been constantly iterating our release plan so that we can deploy quickly and safely. One of the biggest and most fun problem we have is our scale. Deploying code that is used by over 15 million websites means we have to be extra careful. We have learned a lot along the way and I was hoping to share some of our lessons learned and processes we currently implement to ensure quality.

## The Problems and Solutions

When I first started, our deployment process was as follows:

 1. Release every two weeks (not including hotfixes)
 2. Build master and push to UAT (pre-production)
 3. Get thumbs up from QA
 4. Tag master with current versioned release
 5. Deploy to all our production servers
 6. Purge CDN
 7. Hope for the best

### Problem 1. Two-week release cycles

At AddThis, most of our engineering teams follow a two-week sprint and release cycle, which we have found to be suboptimal on our team at our scale. Two-week releases introduce too many changes which correlates to a high risk of something going wrong. Also, the more changes you introduce at once the harder debugging problems is.  

We decided to drop two-week release cycles and moved towards releasing whenever our engineers want. This means we are now releasing maybe 2 or 3 bug fixes at a time, if that. Following this release plan, we have noticed a tremendous drop in support tickets and with that an overall decrease in wasted developer time tracking down problems. We are also more confident in our releases because surface area of possible bugs has been reduced drastically over two-week releases.

### Problems 2-7. Deploy to everyone or no-one

We quickly came to realize that this deployment pattern was not working as well as we wanted. Our QA team is awesome and would find a lot of things in UAT; however, it's impossible to catch everything, especially when our code has to work on a lot of legacy browsers across so many websites. One of our engineers had the awesome idea to replace UAT with 1% deploys (aka edge). What this means is that we deploy our new code to 1% of our users as a better sample space to find bugs before we release to 100%.

With this in mind here is our current plan:

 1. Release whenever
 2. Build master and push to servers as new edge code
 3. Purge CDN
 4. Monitor real-time graphs for 10-15 minutes (talked about next)
 5. Tag branch & change symlinks so edge is now live to 100% traffic
 6. Monitor real-time graphs for 10-15 minutes (if not longer as the graphs are always next to us on monitors)
 7. Grab a beer because you can be sure IE8 didn't break.
 
Moving to edge releases has been one of the best moves we have done as far as ensuring quality is concerned. Our sample size is large enough that we can monitor different browsers on our various endpoints to feel safe about our releases. 

### 3. No real-time insights into our deployments

This has been the fun project we have been interating on and making better over the last couple months. We had a problem where the quickest we could get insights on releases was about an hour, which was not acceptible. Internally, we use kafka to log requests to our various endpoints and we have services downstream who consume and do various things with the data. We had the idea to add a downstream service that consumes 100% of our edge traffic and 1% of non-edge traffic, which provides us with a large enough sample size to analyize our releases. 

As you can see by the graph below, we can watch in near real-time the adoption rate of our new release version based on the data we log from our endpoints. The data is updated every 30 seconds; however, we can get down to sub 5 second updates if we wanted.

![Release Version Change](https://fluxthis.io/release_version.png)

Another graph we monitor heavily is the amount of invalid versus valid requests we receive on our major endpoints. We measure invalid and valid based on important values that upstream stakeholders care about. This ensures that we didn't accidently release some code missing key values that essentially render the data useless. 

![inavlid vs valid requests](https://fluxthis.io/invalid-validd.png)

These are just a few of the graphs that we have in our dashboard that we use during every release. 


## How we do it

This whole process all starts with our external facing service logging requests from our endpoints to [kafka](https://kafka.apache.org/). From there we have any number of downstream consumers who listen to these topics and do cool stuff with the data. In our example, we ingest a subset of the data and save it to [InfluxDB](https://influxdb.com), so that we can run queries on why things are bad and also populate our internal [grafana](http://grafana.org/) dasbhoards. 

Want to know more? Leave some comments! Also, we are [hiring](http://www.addthis.com/careers?jvi=oeja0fwr,job) :D!
