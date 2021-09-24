---
layout: post
title:  "Powerful Visual Testing with Applitools"
date:   2021-05-09 16:46:07 -0400
tags: [applitools, quality assurance, testing, visual testing, verification, cucumber, protractor, chai]
---

Hold on to your hats because I recently had a great experience with automating visual design verification. I know... you probably can't believe it. Testing an app's appearance is a historically hard problem to automate. Screenshot comparison tools can be finicky, and doing it by hand/manually can take years off your life. Believe me, I know. It's rough. 

I am writing this article because I recently had the pleasure of using Applitools to verify the visual appearance of a web app. It was actually pretty awesome. Using Applitools greatly sped up how long it took to run our tests. Also, we were able to keep tabs our app's appearance along the way and catch visual bugs soon after they were introduced. Where before we had been using manual test protocols, switching to Applitools made it possible to automate what is often notoriously difficult to automate: visual design verification.

# The quest for "right"
If you have ever developed a website or app with a UI, you probably know what it's like to spend countless hours tweaking its appearance. This can involve adjusting the alignment of elements of the screen, the fonts, the colors, and how information is displayed. You want it to look right. For many developers and testers, it's our job to make sure it looks right.

But, how do we actually know that it is "right"? 

# Starting with ad-hoc testing
The first technique we probably all start with is navigating through an app and looking for things that feel "off". Heck, you have to do that when building an app, much less test it. As we scan back and forth across the page, we look for anything jumps out at us as wrong. Such ad-hoc testing is useful and has its place, for sure, but it can only go so far. For one, such ad-hoc testing can yield inconsistent results from day to day and person to person. One day, you might miss an alignment problem because you were focusing on a button's appearance. Someone else might not even think to check the alignment of elements. It's hit and miss. 

Now, let's not poo-poo ad-hoc testing too much, though. Ad-hoc testing's lack of structure is actually strength, because you can think outside the box with what you inspect at and try out, but it doesn't ensure full coverage of the app. That simply isn't its purpose.

# Graduating to a Manual Visual Test Protocol
So, you have decided to write a manual test protocol. Good! It will guide you through verifying that every screen in the app matches the UI design. At the end of your project you can use it to verify that the app matches the visual design. Also, your testing is now reproducible by more than one person. This will make sure that no matter who executes the test protocol, every screen will be evaluated. Can we now dust our hands off and say "our job is done?" Well, not quite.

As great as this is, there are some significant limitations with using a manual visual test protocol. For one, manual testing can be tedious and time consuming process, and susceptible to human error. Also, it relies on the design sensibilities of the person executing the test. The tester holds up the app and the design next to each other and evaluates if there are "meaningful" differences between the two. One problem with this is that one tester might be more picky or strict in his/her evaluation, while another is more lenient. Two people may have very different opinions about what looks "close enough." This causes the test results to be influenced on not just the app's adherence to the UI designs, but also the ability for the tester to correctly identify those differences. Even if you use a diff tool to help identify the differences, the outcome can still be affected by differences in opinion between different testers. 

Another downside is that it can take a long time to execute such manual testing. As we all know, if it takes a long time to do something, we probably will avoid doing that thing very often. Just think about filing your taxes. Shudder... Because of this, a team might go a long time between manual test executions, and might hold off until just before a release to run the manual protocol. The team may go a long time before noticing pesky visual design bugs that are inadvertently injected into the app. This can cause an unfortunate surprise late in the project that requires rework, hitting at just the wrong time - when the project team is trying to get the app out the door.

# Taking it to next level: Automated Visual Testing with Applitools

While developing version 1 of our web app, we initially wrote a manual visual test protocol. It was the simplest thing to do, but it turned out to take a long time to get right and to execute. Our app needed to support four different browsers, so we had to execute the entire protocol four times. It took me nearly an entire week to execute across all of our supported browsers. Ugh. For version 2, we knew that something had to change, and that's when we starting looking at Applitools.

I started by trying out Applitools' classic test runner. This enabled me to write automated tests using Protractor that would navigate my team's app to a particular page or state, and then take a snapshot of it. The snapshot was sent up to Applitools' web dashboard where I could view it and approve it. This created a "baseline" approved snapshot for that test. When I ran my test again, a new snapshot was taken and compared to the baseline. If Applitools detected any differences, then it would flag them in the web dashboard, allowing me to review the differences and accept or reject them. I could flag any issues that needed be fixed. It was awesome.

I should point out that the snapshots that Applitools takes aren't just screenshot images, they also include the page's DOM/CSS. This helps Applitools' Visual AI detect meaningful changes in the UI instead of flagging slight rendering inconsistencies. Its intelligent AI makes it possible to review the meaningful changes without drowning in a sea of tiny unimportant differences.

Using the classic runner, I wrote an NPM script to execute our visual tests on each web browser. To run the tests, I just ran each script one after the other - first for Chrome, then for Edge, then for IE, etc. This was much faster than testing manually. I could easily test all four browsers in a few hours, where as with manual tests it took a few days.

# Limitations of the Classic Runner

As great as Applitools' Classic Runner was, I discovered some limitations that required me to upgrade to Applitools Ultrafast Grid. Most of the issues centered around rough edges in their IE support. Oh IE... you relic of a different age... kept alive for reasons unknown to mortals. We are cursed with supporting you. 

When running my tests on IE, I discovered quirks that made it hard or impossible to capture full-page snapshots of our app. The problem seemed to be a combination of our app's specific design and IE's webdriver not being flexible enough. It is possible the the issues have been fixed by now, but if you plan to support IE it would be worth evaluating Applitools' IE support closely to ensure that it meets the needs of your project.

Ultimately, these issues were mostly fixed when I upgraded to Applitools Ultrafast Grid, which I will speak to next.

# Browser Quirks

One quirk about testing on multiple web browsers is that they all behave a little bit differently. For example, one of our tests involved mousing over elements in the page to display tooltips. But, we discovered that Protractor's various web drivers varied in their coordinate systems. We couldn't just say "move the mouse to (X,Y)" because the relative origin of each coordinate system was in a different location. Some browsers had (0,0) in the top-left of the page. For at least one browser, the origin was at the top-left corner of the entire OS window. We had to keep track of how much we needed to offset our coordinates for each browser to ensure that the mouse movements ended up in the same place on the page. We spent a lot of time trying to get that right.

Such browser and web driver quirks meant that we had to configure our tests a bit differently for each browser. Such tweaking took time to get right, but it was necessary so that our tests ran consistently on each browser. In retrospect, it would have saved us a lot of time if we had just used Applitools' Ultrafast Grid from the beginning, but we were taking things one step at a time and didn't have that evidence until later. 

# Knocking it out of the park with Applitools Ultrafast Grid

Upgrading to the Ultrafast Grid was a breath of fresh air. It enabled us to run our tests once on a single browser, but magically produce snapshots for multiple target browsers. Yes, it was total black magic, but it actually worked. I set aside a few days to evaluate the accuracy of the renderings and I found them to either be spot on or very close for each browser. Everything was in the range of acceptability, which I was very glad for.

Here's a quick step-by-step of how the Ultrafast Grid works:
1. Our Protractor tests launch our app, and navigate to a specific page or state.
2. We use the Applitools API to take a snapshot of the page.
3. Applitools sends the snapshot to its cloud-based Ultrafast Grid.
4. The Ultrafast Grid renders the snapshot on multiple target browsers.
5. The snapshots show up in the Applitools web dashboard, where any detected changes are highlighted.
6. We review the snapshots and changes, and either accept or reject them. We could add comments for issues found.

The Ultrafast Grid saved my team a lot of time. It was a little sad when I had to delete the code I had written to handle each individual browser separately. But, ultimately the Ultrafast Grid was a much easier system to work with and it provided accurate screenshots and change detection for each of our target browsers. Also, and certainly not least, it was able to turn around test results much quicker than running tests separately on each browser. Whereas before it would take a few hours to run the tests, now it took only 30-40 minutes with the Ultrafast grid. I could conceivably run the tests once every day if I wanted to, or hook it into our CI pipeline. It was amazing.

Now, we could test early and often, and catch visual bugs soon after they were introduced. It enabled me to have conversations like, "Hey Bob (developer), that most recent commit seems to have caused the submit button to shift downward. Was that intentional?" Fixes could be made when the code that introduced the issue was fresh in people's minds. No more waiting until the end of the project and holding your breath, hoping that no visual design issues are found. We could could build confidence along the way.

# Finding "right" and staying there

At the beginning of this article I asked how we can actually know that a web app's visual appearance is 'right'. Well, Applitools provides some great tools for that, whether you are using the Ultrafast Grid or the Classic runner. Both send their snapshots to the web dashboard. There, the snapshots can be reviewed and either approved or rejected. If you approve a snapshot, then that snapshot becomes the new baseline that all future snapshots are compared to. Once you get this process going, you can review changes as they are detected. But, you first need some sort of accepted baseline.

Here is the process:
1. Run your tests the first time. Since is the first time that Applitools has seen your app, the snapshots automatically become the approved baseline.
2. At this point, you should examine the snapshots and evaluate whether or not your app looks correct. If it doesn't then fix the issues and re-run the tests. Applitools is a great tool for detecting differences, but it doesn't magically know whether your app matches your visual designs. That's something that you have to establish at the beginning by careful examination. Once that is complete, the baselines in Applitools represent the expected appearance of your app. They are the definition of "right". This enables Applitools to detect differences going forward.
3. As your app is developed, re-execute your tets often to evaluate whether its appearance is still "right".
4. If changes are detected, you can either (A) accept them, which will save them as a new baseline, or (B) reject them, and add comments to describe what is wrong.
5. The baseline will progressively move forward as you accept expected changes, keeping a solid definition of "right" all along the way.
6. When you are about to release, you can run your tests to see that the app still matches the baseline. This will give you the confidence that the app will look correct when deployed.

The baseline and change detection features I describe above are part of Applitools Eyes, a "Visual AI Analysis" tool available to both the Ultrafast Grid and the Classic runner. So, even if you don't want to spring for the fancy Ultrafast Grid, simply using the Classic Runner will give you access to these powerful tools. They are a huge improvement over using a manual test suite.

Overall, my experience with Applitools has been a great one. It has made a big impact on my testing outlook, and I look forward to using it in the future. I feel like it is such a compelling tool that I have written up [a walk-through](https://jeremy-jarvis.github.io/applitools-demo/) of how to use Applitools with Cucumber, Protractor, and Chai to verify the visual appearance of Angular apps. That walk-though is paired with [a GitHub repo](https://github.com/jeremy-jarvis/applitools-demo) that demonstrates these tools being used together. 

I hope this article and the links above will help you knock your visual testing out of the park.