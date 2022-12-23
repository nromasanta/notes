# HTML Basics

```
<!-- BASIC STARTING PAGE -->

<!DOCTYPE html>
<html lang = "en">
<head>
<title>Page Title</title>
</head>
<body>

<h1>My First Heading</h1>
<p title = "I am a tooltip">This is a paragraph</p>
<p>My first paragraph.</p>
```
- **\<!DOCTYPE HTML>**
    - Defines doc as an HTML5 doc
- **\<html>**
    - root of an HTML page
- **\<head>**
    - Meta info about HTML page
- **\<title>**
    - Title for page (title bar or page tab)
- **\<body>**
    - Container for all visible content
- **\<html lang = "en">
    - Helps search engines and browsers know lang
- **\<p title = "I am a tooltip">This is a paragraph</p>**
    - Tooltips display info when element is moused over
---
---
### **Links**
- \<a>
    - \<a href = "www.link.com"> this is a link \<a>

the "targe" element defines how to open the link
- _self = open in same window/tab
- _blank = document in new window/tab
- _parent = open in parent frame
- _top = full body of window
---
---
### **Images**
- \<img src =  "img.jpg" alt = "image desc" width = "100" height = "100">
    - src specifies path
    - alt specifies alternate text if image cannot be displayed, good for screen readers

put the image tag inside an \<a> tag to use an image as a link

to use a button as a code, use javascript
```
<button onclick = "document.location='default.asp'"> HTML Tutorial </button>
```
reccomend using a title attribute so ppl know what they're clicking on hovering something

---
---
## **Formatting Text and Paragraphs**
Note: These are tags!
- **\<hr>**
    - Horizontal line
- **\<br>**
    - Line Break
- **\<pre>**
    - preformatted text
- **\<b>** or **\<strong>**
    - bold
- **\<i>** or **\<em>**
    - italics, em is used for screenreaders
- **\<small>**
    - small text
- **\<mark>**
    - highlights
- **\<del>**
    - strikethrough
- **\<ins>**
    - underlines, signifies inserted text
- **\<sub>**
    - subscript
- **\<sup>**
    - superscript
- **\<abbr title = "full name"> abbreviation \</abbr>**
    - abbreviates, good for screenreaders

---
### **Quotations**
- **\<blockquote>**
    - optional: add a ``cite = "www.link.com`` element
- **\<q>**
    - short quotes, adds quotation marks
- **\<cite>**
    - title of work





---
--- 
### **Styles**
inline is done like this:
```
<tagname style="property:value;">
```
