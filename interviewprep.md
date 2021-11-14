---
layout: default
parent: Home
title: Tech Interview Prep 
nav_order: 7
---
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
## IDWise Tech Interview Prep Resources

Dear Candidate!

We are so excited to meet you for our IDWise Engineering Interview soon!

This file should help you understand the structure of the interview.

# Interview Structure

Our interview consists of 3 main components:

1.  Coding 30-45 minutes
2.  System Design 30-45 minutes
3.  Behavioural + Final Q&A 15-30 minutes


## What will you need on the day?
* ⏰ **Time:** Please block the time for the interview, 2.5 hours at least, as some of the parts can go over a bit.
* 🌐 **Good Internet Conenction:** to ensure that your interview is not interrupted and you get the best experience.
* 🎧 **Good Pair of Heaephones:** to allow the best communicatin.
* 💻 **Computer:** it might seem obvious 🙂, but some candidates joined from their phones with no access to a computer, which wouldn't allow for the best interview experience!

## Coding Interview

This part of the interview focuses on your coding and problem-solving skills, where you'll solve general coding questions, In this interview, there will be a high emphasis on testing your understanding of concepts like data structures, algorithms, and design.

Your technical knowledge, coding ability, and creativity will be tested.

You are not required to write a code that runs (although if you feel comfortable, give it a go!), but we are looking for your understanding of the problem, and ability to navigate and optimise the solution, the way you interact and ask questions, and when you start coding!

We also would observe the way you approach debugging your code/pseudocode, and how you detect and fix problems.

**Resources**: You can try using [LeetCode](https://leetcode.com/), which is a free tool that contains a lot of examples similar to the ones which will be asking.

**We use [codeshare.io](https://codeshare.io/) for this part of the interview.**

## System Design

In this interview, we will give you an opportunity to discuss your experience and abilities and to showcase your skills in creating complex systems.

Think of a system design interview as a brainstorming session, driven by open-ended questions, in which you'll be expected to competently discuss a complex system. Consider it an opportunity to work with your interviewer, just as two team members would, to solve a real problem related to the company's goals.

Given the complexity of system design, it's impossible to cover all the various topics and tradeoffs in 45 minutes. Moreover, unlike coding interviews, there isn't just one optimal solution, and the interviewer may focus on different aspects of the system, part of the interview is to sense that focus and ensure that you listen and flow through with the conversation.

**Resources**: A great resource is this Github repo [system-design-primer](https://github.com/donnemartin/system-design-primer) it contains loads of useful information and links to other resources and can help you for the interview, and for the real world too!

**We use [ExcaliDraw](https://excalidraw.com/) for this part of the interview.**

## Behavioural + Final Q&A

This involves asking questions about the candidate's behaviour in past situations that are similar to the ones required in the role you're trying to fill, and you will have the opportunity to ask questions about the company, team, processes, and tech of the company at the end too.

The IDWisers wishes you the best! 

For any questions, you can contact the team via the website: https://idwise.com/contact
