# codedoc-google-analytics
A plugin for websites built with [CODEDOC](https://codedoc.cc/) that sends tracking data to Google Analytics.

## Getting started

Set your Google Analytics tracking ID in the [CODEDOC configuration file](https://codedoc.cc/docs/config/overview).
```tsx
plugins: [
  ...,
  googleAnalytics("UA-123456789-1"),
  ...
],
```

## Verify

There are two ways to verify that it is working.
1. Go to [Google Analytics](https://analytics.google.com/analytics/web) -> Realtime -> Overview, and find data for your own page views in real time.
2. Use [Google's Tag Assistant extension for Google Chrome](https://chrome.google.com/webstore/detail/tag-assistant-by-google/kejbdjndbnbjgmefkgdddjlbokphdefk).  For information about how to use this, see my [Documentation of Correctness](https://github.com/bender2k14/codedoc-google-analytics/blob/master/Documentation_of_Correctness.md).