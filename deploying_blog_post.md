# Lessons Learned Deploying Code used by over 15 million Websites

Step 1. Realize you have a problem. 
Step 2. Solve it
Step 3. Profit

Over the past year, the engineers on our Publisher team have been constantly iterating on our release plan so that we can deploy quickly and safely. One of the biggest and most fun problems we face is our scale. Deploying code that is used by over 15 million websites means we have to be extra careful, so that we don't break all our custom websites, for example. We have learned a lot along the way and I was hoping to share some of our lessons learned and processes we currently implement to ensure quality.

## The Problems and Solutions

When I first started, our deployment process was as follows:

 1. Release every two weeks (not including hot fixes)
 2. build master and push to UAT
 3. get approval from QA
 4. Tag master with current versioned release
 5. Deploy to all our production servers
 6. Purge our CDN
 7. Hope for the best

### 1. Two-week release cycles

At AddThis, most of our engineering teams follow a two-week sprint and release cycle, which we have found to be suboptimal at our scale. Two weeks releases pose a dangerous risk of introducing too many changes, which makes debugging problems harder. 

We decided to drop two-week release cycles and moved towards releasing whenever our engineers thought was the right time. This means we are now releasing maybe 2 or 3 bug fixes at a time, if that. Following this release plan, we have noticed a tremendous drop in support tickets and with that an overall decrease in wasted developer time tracking down problems.

### 2-7. Deploy to everyone or no-one

We quickly came to realize that this deployment pattern was not working as well as we wanted. Our QA team is awesome and would find a lot of things in UAT; however, it's impossible to catch everything, especially when we have to work on a lot of legacy browsers across so many websites. One of our engineers had the awesome idea to replace UAT with 1% deploys (aka edge). What this means is that we deploy our new code to 1% of our users as a better sample space to find bugs before we release to 100%.

With this in mind here is our current plan:

 1. Release whenever
 2. Build master and push to servers as edge
 3. Purge CDN
 4. Monitor real-time graphs for 10-15 minutes (talked about next)
 5. Tag branch & change symlinks so edge is now live to 100% traffic
 6. Monitor real-time graphs for 10-15 minutes (if not longer as the graphs are always next to us on monitors)
 7. Grab a beer because you can be sure IE8 didn't break.
 
 This new deployment process has been its weight in gold. 
### 3. No real-time insights into our deployments
