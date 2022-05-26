---
creation date : <% tp.file.creation_date() %> 
modification date : <% tp.file.last_modified_date("dddd Do MMMM YYYY HH:mm:ss") %> 
tags : <% tp.file.tags %>
---

<% tp.date.now("YYYY-MM-DD", 1) %> 

# <% tp.file.title %> 

Problem
===

Source
---

Problem Analytics
---

### The original problem




Your task is to write a program, which for a given sequence prints True if it is full of colors, otherwise it prints False.

### Analytics
> To solve the problem, we first have to understand the problem.

Let's define the notation as below:


and the rules can we rephrase as this,




Angle of Attack
---



Algorithm
---
````

````

Hierarchy Decomposition
---

Further Readings
---
###### tags: ``