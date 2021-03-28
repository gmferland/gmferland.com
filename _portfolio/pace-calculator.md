---
title: Running pace calculator
website: https://racepace.space
header:
  teaser: /assets/images/pace-calculator-teaser.png
excerpt: |
  The easiest tool for runners to calculate splits and predict paces
  for workouts and races.
---
As a runner I am obsessed with numbers, sometimes to a fault. When planning my own workouts, I like knowing how fast I should be going at each step of the way. And when watching others run, especially over longer distances, it often makes more sense to talk about a performance in terms of the pace per mile or some other common denominator.

But I have no idea off the top of my head how quick to run a lap of the track at my 10k race pace or how fast a sub-3 hour marathon really is. So I created the Race Pace calculator to make running arithmetic quick and easy.

{% include figure image_path="/assets/images/pace-calculator-main.png" alt="The Race Pace Calculator allows runners to see intermediate lap splits given a race distance and goal time." caption="The Race Pace Calculator allows runners to see intermediate lap splits given a race distance and goal time." %}

This project first started as a Chrome extension that I hacked together as I was first getting into web development. This setting worked perfectly for being able to comprehend race results I saw online, especially for certain road races like Falmouth (7 miles) or Manchester (4.748 miles). Then, a few years later, my roommate Cole and I repackaged the pace calculator into a web app. For the best experience, open [Race Pace]({{ page.website }}) in your mobile web browser and add it to your home screen, where it looks and feels just like a native app.

## Technology

Race Pace is built to be sleek and fast both for the end user and for me, the developer. I chose [Preact](https://preactjs.com) rather than React in order to be able to write UI components without shipping a bloated SPA bundle. The Preact CLI conveniently builds the javascript code into static prerendered html pages in order to speed up page load in the browser. Meeting the [Progressive Web App](https://developer.mozilla.org/en-US/docs/Web/Progressive_web_apps) standard was rather easy, as there are no API calls to make and all calculations are performed in client side javascript.

{% include figure image_path="/assets/images/pace-calculator-mobile.jpeg" alt="On mobile devices, input is streamlined for ease of use." caption="On mobile devices, input is streamlined for ease of use." %}

For user input, I chose [Formik](https://formik.org) to create accessible forms without a lot of boilerplate code. One of the fun challenges of writing the user interface was building a masked input to correctly format the time, freeing up the user to simply type numbers.

Another little win was the discovery of the `<datalist>` html element to give typeahead suggestions on the distance input. On an iPhone, Safari will automatically pipe the `<datalist>` elements to the suggestion boxes atop the keyboard. Race Pace is not yet the most popular pace calculator on the internet, but I'm confident it's the most user friendly!
