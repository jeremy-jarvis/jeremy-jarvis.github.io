---
layout: post
title:  "Powerful Visual Testing with Applitools"
date:   2021-05-09 16:46:07 -0400
tags: [applitools, quality assurance, testing, visual testing, verification, cucumber, protractor, chai]
---

Hold on to your hats because I recently had a great experience with automated visual testing.

I know... you probably can't believe it. Testing an app's visual  appearance is a historically hard problem to automate. Screenshot comparison tools can be finicky, and doing it manually can take years off your life. Believe me, I know. It's rough. 

I am writing this article because I recently had the pleasure of using Applitools to verify the visual appearance of a web app. It was actually pretty awesome. Applitools made it possible for us to automate our visual testing, something that is often notoriously difficult to automate. It greatly reduced how long it took to run our tests. This allowed us to keep tabs our app's appearance along the way and catch visual bugs soon after they were introduced.

# The Quest for "Right"
If you have ever developed a website or app with a UI, you probably know what it's like to spend countless hours tweaking its appearance. This can involve adjusting the alignment of elements of the screen, the fonts, the colors, and how information is displayed. You want it to look right. For many developers and testers, it's our job to make sure it looks right.

But, how do we actually know that it is "right"? 

# Starting with Ad-hoc Testing
The first technique we probably all start with is ad-hoc testing. We navigate through our apps and look for things that feel "off". It's natural to do this, either as a part of developing an app, or as a basic way to "test" that it looks right. Such ad-hoc testing is useful and definitely has its place, but it can only go so far. For one thing, such ad-hoc testing can yield inconsistent results from day to day or person to person. One day, you might be so focused on a button's incorrect appearance that you miss an obvious alignment problem. Someone else might not even think to check either of those things and is focused on something else. It can be hit and miss. 

Now, let's not poo-poo ad-hoc testing too much. Its lack of structure is actually a strength, because you can think outside the box with what you examine and try out, but it doesn't ensure full coverage of the app. That simply isn't its purpose.

# Graduating to a Manual Test Protocol
So, you have decided to write a manual visual test protocol. Good. That is a step in the right direction! It will guide you through verifying that every screen in your app matches the UI design. Also, your testing is now reproducible by more than one person. This will make sure that no matter who executes the test protocol, every screen will be evaluated. Can we now dust off our hands and say "our job is done"? Well, not quite.

As great as this is, there are some significant limitations with using manual protocols. For one, manual testing can be a tedious, time consuming, and susceptible to human error. Also, it relies on the design sensibilities of the person executing the test. He or she has to compare the app to the design and evaluate if there are "meaningful" differences. One tester might be more picky or strict in his or her evaluation, while a different tester is more lenient. Two people may have very different opinions about what looks "close enough." This can cause the test results to be influenced not only by the app's adherence to the UI designs, but also the ability for the tester to correctly identify those differences. Even if you use a diff tool to help identify the differences, the outcome can still be affected by differences in opinion between different testers. 

Another downside is that it can take a long time to execute such manual testing. It can be very, very tedious. As we all know, if it takes a long time to do something, we probably will avoid doing that thing very often. Just think about filing your taxes. Shudder... A team would likely de-prioritize such testing in favor of more urgent work. Because of this, a team might go a long time between manual test executions, even waiting until just before release to execute the testing. I've totally been there.

Infrequent testing or "end of the project only" testing delays a team's ability to find pesky visual design bugs that are inadvertently injected into the app. This can cause an unfortunate surprise late in the project that requires rework, hitting at just the wrong time - when the project team is trying to get the app out the door.

# Automated Testing With Applitools

While developing version 1 of our web app, we initially wrote a manual visual test protocol. It was the simplest thing to do, but it turned out to take a long time to get right and to execute. Our app needed to support four different browsers, so we had to execute the entire protocol four times. It took me nearly an entire week to execute across all of our supported browsers. Ugh. For version 2, we knew that something had to change, and that's when we starting looking at Applitools.

I got started with Applitools by poking around with Applitools' classic test runner. It enabled me to write automated tests using Protractor that would navigate our app to a particular page or state, and then take a snapshot of it. The snapshot was sent up to Applitools' web dashboard where I could view it and approve it. This created a "baseline" approved snapshot for that test. When I ran my test again, a new snapshot was taken and compared to the baseline. If Applitools Visual AI detected any differences, then it would flag them in the web dashboard, allowing me to review the differences and accept or reject them. I could flag any issues that needed be fixed. It was awesome.

I should point out that the snapshots that Applitools captures aren't just screenshot images, they also include the page's DOM and CSS. This helps Applitools' Visual AI detect meaningful changes in the UI instead of flagging slight rendering inconsistencies. Its intelligent AI makes it possible to review the meaningful changes without drowning in a sea of tiny unimportant differences.

Using the classic runner, I wrote NPM scripts to execute our visual tests on each web browser. To run the tests, I just ran each script one after the other - first for Chrome, then for Edge, then for IE, etc. This was much faster than testing manually. I could easily test all four browsers in a few hours, where as with manual testing it took a few days.

# Limitations of the Classic Runner

As great as Applitools' classic runner is, I discovered some limitations that required me to upgrade to Applitools Ultrafast Grid. Most of the issues centered around rough edges in their IE support.

> Oh IE... you relic of a different age... kept alive for reasons unknown to mortals. We are cursed with supporting you. 

When running my tests on IE, I discovered quirks that made it hard or impossible to capture full-page snapshots of our app. The problem seemed to be a caused by a combination of our app's implementation and inflexibilities in the IE webdriver. It is possible the the issues have been fixed by now, but be warned: If you plan to support IE, then it would be worth evaluating Applitools' IE support to ensure that it meets your needs.

Ultimately, these issues were mostly fixed when I upgraded to Applitools Ultrafast Grid.

# Browser Quirks

One challenge of testing on multiple browsers is that they all behave a little bit differently. For example, one of our tests involved mousing over elements in the page to display tooltips. But, we discovered that the webdrivers varied in their coordinate systems. We couldn't just say "move the mouse to (X,Y)" because the relative origin of each coordinate system was in a different location. Some browsers had (0,0) in the top-left of the page. For at least one browser, the origin was inexplicably at the top-left corner of the entire OS window. To work around this, we had to adjust our coordinates with different offsets for each browser. This better ensured that the mouse movements ended up in the same place on the page in each browser. We spent a lot of time investigating and fiddling with offsets to get that right.

Such browser and web driver quirks meant that we had to configure our tests a bit differently for each browser. Such tweaking took time to get right, but it was necessary so that our tests ran consistently. In retrospect, it would have saved us a lot of time if we had just used Applitools' Ultrafast Grid from the beginning, but we were taking things one step at a time and hadn't yet drawn that conclusion. 

# Taking it to the Next Level With Applitools Ultrafast Grid

Upgrading to the Ultrafast Grid was like a breath of fresh air combined with a kick of adrenaline. With it we were able to run our tests once on a single browser, but magically produce snapshots for multiple target browsers.

Yes, you read that right. Run once, and produce output for as many browsers as we wanted. It was total black magic, but it actually worked. I set aside a few days to evaluate the accuracy of the renderings and I found them be spot on or at least very close, depending on the browser. Everything was in the range of acceptability, which I was very glad for.

Here's a quick step-by-step of how the Ultrafast Grid works:
1. Our Protractor tests launch our app, and navigate to a specific page or state.
2. We use the Applitools API to take a snapshot of the page.
3. Applitools sends the snapshot to its cloud-based Ultrafast Grid.
4. The Ultrafast Grid renders the snapshot on multiple target browsers.
5. The snapshots show up in the Applitools web dashboard, where any detected changes are highlighted.
6. We review the snapshots and changes, and either accept or reject them. We add comments for issues found.

The Ultrafast Grid saved my team a lot of time. It was a little sad when I had to delete the code I had written for handling each individual browser separately. But, ultimately the Ultrafast Grid was a much easier system to work with and it provided accurate screenshots and change detection for each of our target browsers. Also, and certainly not least, it was able to turn around test results much quicker than running tests separately on each browser. Whereas before it would take a few hours to run the tests, now it took only 30-40 minutes with the Ultrafast grid. I could conceivably run the tests once every day if I wanted to, or hook it into our CI pipeline. It was amazing.

Now, we could test early and often, and catch visual bugs soon after they were introduced. It enabled me to have conversations with teammates like:
> "Hey, that most recent commit seems to have caused the submit button to shift downward. Was that intentional?" 

Fixes could be made while the related code was fresh in people's minds. No more waiting until the end of the project to test and holding our breaths. We could build confidence along the way.

# Finding "Right" and Staying There

At the beginning of this article I asked how we can actually know that a web app's visual appearance is 'right'. Well, Applitools provides some great tools for that, whether you are using the Ultrafast Grid or the Classic runner.

Both send their snapshots to the web dashboard. There, the snapshots can be reviewed and either approved or rejected. If you approve a snapshot, then it becomes the new baseline that all future snapshots are compared to. Once you get this process going you can review changes as they are detected, but the first step is to establish an approved baseline snapshot to compare against.

Here is the process:
1. Run your tests the first time. Since is the first time that Applitools has seen your app, the snapshots automatically become the approved baseline.
2. At this point, you should examine the snapshots and evaluate whether or not your app looks correct. If it doesn't, then fix the issues and re-run the tests. Applitools is a great tool for detecting differences, but it cannot magically know whether or not your app matches the visual designs. That's something that you have to establish at the beginning by careful examination. Once you have established those baselines, they are treated as the expected appearance of your app. They are your definition of "right". This enables Applitools to detect differences going forward.
3. As your app is developed, re-execute your tests often to evaluate whether its appearance is still "right".
4. If changes are detected, you can either (A) accept them, which will save them as a new baseline, or (B) reject them, and add comments to describe what is wrong.
5. The baseline will progressively move forward as you accept expected changes, keeping a solid definition of "right" all along the way.
6. When you are about to release, you can run your tests to see that the app still matches the baseline. This will give you confidence that the app will look correct when deployed.

# Applitools Eyes

The baseline and change detection features I describe above are part of Applitools Eyes, a "Visual AI Analysis" tool available to both the Ultrafast Grid and the Classic runner. So, even if you don't want to spring for the fancy Ultrafast Grid, simply using the classic runner will give you access to these powerful tools. They are a huge improvement over using a manual test suite.

# Further Reading

Overall, my experience with Applitools has been a great one. It has made a big impact on my testing outlook, and I look forward to using it in the future.

I feel like it is such a compelling tool that I have written up [a walk-through](https://jeremy-jarvis.github.io/applitools-demo/) of how to use Applitools with Cucumber, Protractor, and Chai to verify the visual appearance of Angular apps. That walk-though is paired with [a GitHub repo](https://github.com/jeremy-jarvis/applitools-demo) that demonstrates these tools being used together. 

I hope this article and the resources above will help you accelerate your visual testing suite. Happy testing!