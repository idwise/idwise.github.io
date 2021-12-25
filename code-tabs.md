---
layout: default
parent: Home
title: Test Code Tabs
nav_order: 200
---


<style lang="css">
/* Style the tab */
.tab {
  overflow: hidden;
  border: 1px solid #ccc;
  background-color: #f1f1f1;
}

/* Style the buttons that are used to open the tab content */
.tab button {
  background-color: inherit;
  float: left;
  border: none;
  outline: none;
  cursor: pointer;
  padding: 14px 16px;
  transition: 0.3s;
}

/* Change background color of buttons on hover */
.tab button:hover {
  background-color: #ddd;
}

/* Create an active/current tablink class */
.tab button.active {
  background-color: #ccc;
}

/* Style the tab content */
.tabcontent {
  display: none;
  padding: 6px 12px;
  border: 1px solid #ccc;
  border-top: none;
}

</style>

<!-- Tab links -->
<div class="tab">
  <button class="tablinks" onclick="openTab(event, 'Python')">Python</button>
  <button class="tablinks" onclick="openTab(event, 'Java')">Java</button>
  <button class="tablinks" onclick="openTab(event, 'CSharp')">C#</button>
</div>

<!-- Tab content -->
<div id="Python" class="tabcontent">
  ``` python
  print("Hello world")
  ```
</div>

<div id="Java" class="tabcontent">
  ``` java
  Print("Hello world")
  ```
</div>

<div id="CSharp" class="tabcontent">
  ``` java
  Console.Print("Hello world")
  ```
</div>

<script>
function openTab(evt, tabName) {
  var i, tabcontent, tablinks;

  tabcontent = document.getElementsByClassName("tabcontent");
  for (i = 0; i < tabcontent.length; i++) {
    tabcontent[i].style.display = "none";
  }

  tablinks = document.getElementsByClassName("tablinks");
  for (i = 0; i < tablinks.length; i++) {
    tablinks[i].className = tablinks[i].className.replace(" active", "");
  }

  document.getElementById(tabName).style.display = "block";
  evt.currentTarget.className += " active";
}
  alert("test")
</script>
