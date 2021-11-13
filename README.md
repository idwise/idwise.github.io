---
layout: default
title: Home
nav_order: 1
has_children: true
---


## Welcome to IDWise Developers Site!

* [IDWise Android SDK](https://developers.idwise.com/idwise-android-sdk-documentation/)
* [IDWise iOS SDK](https://developers.idwise.com/idwise-ios-sdk-documentation/)
* [IDWise Web SDK](https://developers.idwise.com/idwise-web-sdk/)
* [IDWise Document Processing API](http://developers.idwise.com/document-processing-api/)
* [IDWise Journey API] - contact us to get access.
* [Tech Interview Preapration & Resources](http://developers.idwise.com/interview-prep/)


<button class="btn js-toggle-dark-mode">Dark Mode</button>

<script>
const toggleDarkMode = document.querySelector('.js-toggle-dark-mode');

jtd.addEvent(toggleDarkMode, 'click', function(){
  if (jtd.getTheme() === 'dark') {
    jtd.setTheme('light');
    toggleDarkMode.textContent = 'Dark Mode';
  } else {
    jtd.setTheme('dark');
    toggleDarkMode.textContent = 'Light Mode';
  }
});
</script>
