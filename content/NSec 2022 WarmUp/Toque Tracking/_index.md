+++
title = "Toque Tracking"
description = ""
weight = 4
+++

Challenge statement:
```
The Toqué Restaurant Team is beside themselves... someone appears to have highjacked the restaurant's Google account along with their website analytics.

Google has asked them to provide their Google Tag Manager number along with an affidavit to recover the account, but they have no idea what that is, or how to find it. Do you think your team can leap to their aid? Quebec's gastronomy may just depend on it!

Warning⚠️ : This is a drill with a live, operational environment. Only passive reconnaissance is allowed. No active scanning or brute-forcing shall be used. All legal consequences of breaking this rule are the responsibility of those conducting it.

Determine the Restaurant's Google Tag Manager number and provide it in your flag submission for this challenge.

Enter only the part of the google tag manager number that follows the "GTM-".
```

Searching for `Toqué Restaurant` on Google, we can find the website of the restaurant: https://www.restaurant-toque.com/en.

Looking at the page source, we can find the two following snippet containing the letters `GTM`:

```javascript
[...]
  // gtm code snippet
  (function (w, d, s, l, i) {
  w[l] = w[l] || []; w[l].push({
    'gtm.start':
      new Date().getTime(), event: 'gtm.js'
  }); var f = d.getElementsByTagName(s)[0],
    j = d.createElement(s), dl = l != 'dataLayer' ? '&l=' + l : ''; j.async = true; j.src =
      'https://www.googletagmanager.com/gtm.js?id=' + i + dl; f.parentNode.insertBefore(j, f);
  })(window, document, 'script', 'dataLayer', 'GTM-KVM6BMM');
</script>
<!-- End Google Tag Manager -->
<title>A leader of Quebec gastronomy - Restaurant Toqué</title>
<script>dl = [];
(function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
'//www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
})(window,document,'script','dl','GTM-5J9R53Z');
</script>
[...]
```

```javascript
[...]
<body data-site="toque" data-handle="toque_homepage"><noscript><iframe src="//www.googletagmanager.com/ns.html?id=GTM-5J9R53Z"
height="0" width="0" style="display:none;visibility:hidden"></iframe></noscript>
<noscript><img height="1" width="1" style="display:none"
src="//www.facebook.com/tr?id=2641458616125750&ev=PageView&noscript=1" /></noscript>

  <!-- Google Tag Manager (noscript) -->
<noscript>
    <iframe src="https://www.googletagmanager.com/ns.html?id=GTM-KVM6BMM" height="0" width="0" style="display:none;visibility:hidden"></iframe>
</noscript>
[...]
```

In both of these snippets, we can find two tags. The first one was GTM-KVM6BMM and the second GTM-5J9R53Z. Without a second to lose, we can try the first, and KVM6BMM was the flag.