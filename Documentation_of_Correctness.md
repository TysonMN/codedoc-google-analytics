# Documentation of Correctness

Here are my notes that help convince me that this plugin is correctly implemented.

## Latest Google Analytics library

Google Analytics has been around for some time now, and there have been several different ways to send analytics to Google.

- [This documentation under `developers.google.com/analystics`](https://developers.google.com/analytics/devguides/collection/gajs/asyncMigrationExamples) says that using `ga.js` or `urchin.js` is no longer preferred.  Instead, `analytics.js` should be used.
> `ga.js` is a legacy library. If you are starting a new implementation, we recommend you use the latest version of this library, `analytics.js`.
> ...
> If your Google Analytics tracking snippet contains the `urchin.js` markup or the traditional `ga.js` syntax, use this guide to migrate your tracking to the improved Asynchronous snippet.

- [This documentation also under `developers.google.com/analystics`](https://developers.google.com/analytics/devguides/collection/upgrade#web) says that using `analytics.js` is no longer preferred.  Instead, `gtag.js` should be used.
> Web deployments should use `gtag.js` to send data to Google Analytics. If you're currently using `analytics.js`, [upgrade `analytics.js`](https://developers.google.com/analytics/devguides/collection/upgrade/analyticsjs) walks you through the process of updating.

Therefore, the preferred approach these days is to use `gtag.js`.

Before I read those two pages, I was very confused by all the sources that I found online saying how to properly configure a website to send `pageview` events to Google Analytics.  They seemed so contradictory, which was very confusing.  I now believe that they were contradictory because there were for different libraries that could communicate with Google Analytics (`urchin.js` vs `ga.js` vs `analytics.js` vs `gtag.js`).

## Default `gtag.js` code snippet

When I recently created my Google Analytics tracking ID, Google told me to use [this code snippet](https://developers.google.com/analytics/devguides/collection/gtagjs#install_the_global_site_tag).
```HTML
<script async src="https://www.googletagmanager.com/gtag/js?id=UA-XXXX-1"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'UA-XXXX-1');
</script>
```

## Single page application

Each CODEDOC website is really just a single page that simulates the navigation of internal links with custom transitions and a change to the URL. [This documentation](https://developers.google.com/analytics/devguides/collection/gtagjs/pages#manual_pageviews) says that the default code snippet does not work in this case.
> For most Google Analytics implementations, the default snippet does not need to be modified. However, in cases where you want to manually control how pageviews are sent (e.g. single page applications or infinite scrolling), you should do the following:
> 
> 1. [Disable pageview measurement](https://developers.google.com/analytics/devguides/collection/gtagjs/pages#disable_pageview_measurement)
> 2. [Send the `page_view` event when appropriate](https://developers.google.com/analytics/devguides/collection/gtagjs/pages#page_view_event)

I tried that, but I couldn't get it to work.

Nonetheless, it is true those that the default `gtag.js` code snippet doesn't generate a page view when navigating from one internal page to another on my CODEDOC site.  Instead of the approach suggested by Google, I am listening to the `"navigation"` event on the `window` object.  When this event fires, I execute the relevant part of the default code snippet.  This seems to work.

## Verifying

There are two reasons that I think I have this correctly configured.  Both are [suggested by Google in their documentation](https://support.google.com/analytics/answer/1008080#verify:~:text=Verify%20that%20your%20global%20site%20tag%20is%20working,-To).

### 1. Realtime overview on Google Analytics

Go to [Google Analytics](https://analytics.google.com/analytics/web) -> Realtime -> Overview, and find data for your own page views in real time.  See the [Real-Time documentation](https://support.google.com/analytics/answer/1638635) for more information.

### 2. Google's Tag Assistant extension for Google Chrome

[Tag Assistant](https://chrome.google.com/webstore/detail/tag-assistant-by-google/kejbdjndbnbjgmefkgdddjlbokphdefk) is an extension created by Google for Google Chrome that displays the data sent to Google Analytics.  Here is a [quick video](https://www.youtube.com/watch?v=EkNsaBDT8Cc) that shows someone quickly solving a problem with help from Tag Assistant.  Like in that video, I verified that navigating via several internal links is reported to Google as a single session.

I encountered multiple sources that said Tag Assistant contained bugs that caused some false negative warnings for single page websites (like those made by CODEDOC).  Here is [one of those sources](https://www.analyticsmania.com/post/multiple-installations-of-google-tag-manager-detected/).  Tag Assistant gives me some negative feedback, which is probably the same false negatives experienced by others.

I analyzed the network traffic as described in the previous link.  When the browser loads a page, I see that
1. the page loads `gtag.js` (TODO: add "because of [this line]" _with link to line_),
2. `gtag.js` loads `analytics.js`, and
3. `analytics.js` sends a `pageview` event to Google.

Then when navigating via an internal link, another `pageview` event is sent to Google.  All of this behavior seems correct to me.