---
title: Introduction to events
slug: Learn/JavaScript/Building_blocks/Events
tags:
  - Article
  - Beginner
  - CodingScripting
  - Event Handler
  - Guide
  - JavaScript
  - Learn
  - events
  - l10n:priority
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/JavaScript/Building_blocks/Return_values","Learn/JavaScript/Building_blocks/Image_gallery", "Learn/JavaScript/Building_blocks")}}</div>

<p>Events are actions or occurrences that happen in the system you are programming, which the system tells you about so you can respond to them in some way if desired. For example, if the user selects a button on a webpage, you might want to respond to that action by displaying an information box. In this article, we discuss some important concepts surrounding events, and look at how they work in browsers. This won't be an exhaustive study; just what you need to know at this stage.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Basic computer literacy, a basic understanding of HTML and CSS, <a href="/en-US/docs/Learn/JavaScript/First_steps">JavaScript first steps</a>.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand the fundamental theory of events, how they work in browsers, and how events may differ in different programming environments.</td>
  </tr>
 </tbody>
</table>

<h2 id="A_series_of_fortunate_events">A series of fortunate events</h2>

<p>As mentioned above, <strong>events</strong> are actions or occurrences that happen in the system you are programming — the system produces (or "fires") a signal of some kind when an event occurs, and provides a mechanism by which an action can be automatically taken (that is, some code running) when the event occurs. For example, in an airport, when the runway is clear for take off, a signal is communicated to the pilot. As a result, the plane can safely takeoff.</p>

<p><img alt="Image displaying signal for plane to take-off" src="mdn-mozilla-events-runway.png"></p>

<p>In the case of the Web, events are fired inside the browser window, and tend to be attached to a specific item that resides in it — this might be a single element, set of elements, the HTML document loaded in the current tab, or the entire browser window. There are many different types of events that can occur. For example:</p>

<ul>
 <li>The user selects a certain element or hovers the cursor over a certain element.</li>
 <li>The user chooses a key on the keyboard.</li>
 <li>The user resizes or closes the browser window.</li>
 <li>A web page finishes loading.</li>
 <li>A form is submitted.</li>
 <li>A video is played, paused, or finishes.</li>
 <li>An error occurs.</li>
</ul>

<p>You can gather from this (and from glancing at the MDN <a href="/en-US/docs/Web/Events">Event reference</a>) that there are <strong>a lot</strong> of events that can be responded to.</p>

<p>Each available event has an <strong>event handler</strong>, which is a block of code (usually a JavaScript function that you as a programmer create) that runs when the event fires. When such a block of code is defined to run in response to an event, we say we are <strong>registering an event handler</strong>. Note: Event handlers are sometimes called <strong>event listeners</strong> — they are pretty much interchangeable for our purposes, although strictly speaking, they work together. The listener listens out for the event happening, and the handler is the code that is run in response to it happening.</p>

<div class="notecard note">
  <p><strong>Note:</strong> Web events are not part of the core JavaScript language — they are defined as part of the APIs built into the browser.</p>
</div>

<h3 id="A_simple_example">A simple example</h3>

<p>Let's look at a simple example of what we mean here. You've already seen events and event handlers used in many of the examples, but let's recap just to cement our knowledge. In the following example, we have a single {{htmlelement("button")}}, which when pressed, makes the background change to a random color:</p>

<pre class="brush: html">&lt;button&gt;Change color&lt;/button&gt;</pre>

<pre class="brush: css hidden">button { margin: 10px };</pre>

<p>The JavaScript looks like so:</p>

<pre class="brush: js">const btn = document.querySelector('button');

function random(number) {
  return Math.floor(Math.random() * (number+1));
}

btn.onclick = function() {
  const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
}</pre>

<p>In this code, we store a reference to the button inside a constant called <code>btn</code>, using the {{domxref("Document.querySelector()")}} function. We also define a function that returns a random number. The third part of the code is the event handler. The <code>btn</code> constant points to a <code><a href="/en-US/docs/Web/HTML/Element/button">&lt;button&gt;</a></code> element, and this type of object has a number of events that can fire on it, and therefore, event handlers available. We are listening for the <code><a href="/en-US/docs/Web/API/Element/click_event">click</a></code> event firing, by setting the <code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onclick">onclick</a></code> event handler property to equal an anonymous function containing code that generates a random RGB color and sets the <code><a href="/en-US/docs/Web/HTML/Element/body">&lt;body&gt;</a></code> <code><a href="/en-US/docs/Web/CSS/background-color">background-color</a></code> equal to it.</p>

<p>This code is run whenever the click event fires on the <code>&lt;button&gt;</code> element, that is, whenever a user selects it.</p>

<p>The example output is as follows:</p>

<p>{{ EmbedLiveSample('A_simple_example', '100%', 200, "", "") }}</p>

<h3 id="Its_not_just_web_pages">It's not just web pages</h3>

<p>Another thing worth mentioning at this point is that events are not unique to JavaScript — most programming languages have some kind of event model, and the way the model works often differs from JavaScript's way. In fact, the event model in JavaScript for web pages differs from the event model for JavaScript as it is used in other environments.</p>

<p>For example, <a href="/en-US/docs/Learn/Server-side/Express_Nodejs">Node.js</a> is a very popular JavaScript runtime that enables developers to use JavaScript to build network and server-side applications. The <a href="https://nodejs.org/docs/latest-v12.x/api/events.html">Node.js event model</a> relies on listeners to listen for events and emitters to emit events periodically — it doesn't sound that different, but the code is quite different, making use of functions like <code>on()</code> to register an event listener, and <code>once()</code> to register an event listener that unregisters after it has run once. The <a href="https://nodejs.org/docs/latest-v12.x/api/http.html#http_event_connect">HTTP connect event docs</a> provide a good example.</p>

<p>You can also use JavaScript to build cross-browser add-ons — browser functionality enhancements — using a technology called <a href="/en-US/docs/Mozilla/Add-ons/WebExtensions">WebExtensions</a>. The event model is similar to the web events model, but a bit different — event listeners properties are camel-cased (such as <code>onMessage</code> rather than <code>onmessage</code>), and need to be combined with the <code>addListener</code> function. See the <a href="/en-US/docs/Mozilla/Add-ons/WebExtensions/API/runtime/onMessage#examples"><code>runtime.onMessage</code></a> page for an example.</p>

<p>You don't need to understand anything about other such environments at this stage in your learning; we just wanted to make it clear that events can differ in different programming environments.</p>

<h2 id="Ways_of_using_web_events">Ways of using web events</h2>

<p>There are a number of ways to add event listener code to web pages so it runs when the associated event fires. In this section, we review the various mechanisms and discuss which ones you should use.</p>

<h3 id="Event_handler_properties">Event handler properties</h3>

<p>These are the properties that exist to contain event handler code we have seen most frequently during the course. Returning to the above example:</p>

<pre class="brush: js">const btn = document.querySelector('button');

btn.onclick = function() {
  const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
}</pre>

<p>The <code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onclick">onclick</a></code> property is the event handler property being used in this situation. It is essentially a property like any other available on the button (e.g. <code><a href="/en-US/docs/Web/API/Node/textContent">btn.textContent</a></code>, or <code><a href="/en-US/docs/Web/API/HTMLElement/style">btn.style</a></code>), but it is a special type — when you set it to be equal to some code, that code is run when the event fires on the button.</p>

<p>You could also set the handler property to be equal to a named function name (like we saw in <a href="/en-US/docs/Learn/JavaScript/Building_blocks/Build_your_own_function">Build your own function</a>). The following works just the same:</p>

<pre class="brush: js">const btn = document.querySelector('button');

function bgChange() {
  const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
}

btn.onclick = bgChange;</pre>

<p>There are many different event handler properties available. Let's experiment.</p>

<p>First, make a local copy of <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/random-color-eventhandlerproperty.html">random-color-eventhandlerproperty.html</a>, and open it in your browser. It's just a copy of the simple random color example we've played with already. Now try changing <code>btn.onclick</code> to the following different values in turn, and observing the results in the example:</p>

<ul>
 <li><code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onfocus">btn.onfocus</a></code> and <code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onblur">btn.onblur</a></code> — The color changes when the button is focused and unfocused; try pressing the tab to focus on the button and press the tab again to focus away from the button. These are often used to display information about filling in form fields when they are focused, or displaying an error message if a form field is filled with an incorrect value.</li>
 <li><code><a href="/en-US/docs/Web/API/GlobalEventHandlers/ondblclick">btn.ondblclick</a></code> — The color changes only when the button is double-clicked.</li>
 <li><code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onkeydown">window.onkeydown</a></code>, <code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onkeyup">window.onkeyup</a></code> — The color changes when a key is pressed on the keyboard. The <code>keydown</code> and <code>keyup</code> refer to just the key down and key up parts of the keystroke, respectively. Note: It doesn't work if you try to register this event handler on the button itself — we've had to register it on the <a href="/en-US/docs/Web/API/Window">window</a> object, which represents the entire browser window.</li>
 <li><code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onmouseover">btn.onmouseover</a></code> and <code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onmouseout">btn.onmouseout</a></code> — The color changes when the mouse pointer hovers over the button, or when the pointer moves off the button, respectively.</li>
</ul>

<p>Some events are general and available nearly anywhere (e.g. an <code>onclick</code> handler can be registered on nearly any element), whereas some are more specific and only useful in certain situations (e.g. it makes sense to use <a href="/en-US/docs/Web/API/GlobalEventHandlers/onplay">onplay</a> only on specific elements, such as {{htmlelement("video")}}).</p>

<h3 id="Inline_event_handlers_—_dont_use_these">Inline event handlers — don't use these</h3>

<p>You might also see a pattern like this in your code:</p>

<pre class="brush: html">&lt;button onclick="bgChange()"&gt;Press me&lt;/button&gt;
</pre>

<pre class="brush: js">function bgChange() {
  const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
}</pre>

<div class="notecard note">
  <p><strong>Note:</strong> You can find the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/random-color-eventhandlerattributes.html">full source code</a> for this example on GitHub (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/random-color-eventhandlerattributes.html">see it running live</a>).</p>
</div>

<p>The earliest method of registering event handlers found on the Web involved <strong>event handler HTML attributes</strong> (or <strong>inline event handlers</strong>) like the one shown above — the attribute value is literally the JavaScript code you want to run when the event occurs. The above example invokes a function defined inside a {{htmlelement("script")}} element on the same page, but you could also insert JavaScript directly inside the attribute, for example:</p>

<pre class="brush: html">&lt;button onclick="alert('Hello, this is my old-fashioned event handler!');"&gt;Press me&lt;/button&gt;</pre>

<p>You can find HTML attribute equivalents for many of the event handler properties; however, you shouldn't use these — they are considered bad practice. It might seem easy to use an event handler attribute if you are doing something really quick, but they quickly become unmanageable and inefficient.</p>

<p>For a start, it is not a good idea to mix up your HTML and your JavaScript, as it becomes hard to parse — keeping your JavaScript separate is best practice; if it is in a separate file you can apply it to multiple HTML documents.</p>

<p>Even in a single file, inline event handlers are not a good idea. One button is OK, but what if you had 100 buttons? You'd have to add 100 attributes to the file; it would quickly turn into a maintenance nightmare. With JavaScript, you could easily add an event handler function to all the buttons on the page no matter how many there were, using something like this:</p>

<pre class="brush: js">const buttons = document.querySelectorAll('button');

for (let i = 0; i &lt; buttons.length; i++) {
  buttons[i].onclick = bgChange;
}</pre>

<p class="brush: js">Note that another option here would be to use the <code><a href="/en-US/docs/Web/API/NodeList/forEach">forEach()</a></code> built-in method available on <code><a href="/en-US/docs/Web/API/NodeList">NodeList</a></code> objects:</p>

<pre class="brush: js">buttons.forEach(function(button) {
  button.onclick = bgChange;
});</pre>

<div class="notecard note">
  <p><strong>Note:</strong> Separating your programming logic from your content also makes your site more friendly to search engines.</p>
</div>

<h3 id="adding_and_removing_event_handlers">Adding and removing event handlers</h3>

<p>The modern mechanism for adding event handlers is the <code><a href="/en-US/docs/Web/API/EventTarget/addEventListener">addEventListener()</a></code> method. Using it, we could rewrite our random color example to look like this:</p>

<pre class="brush: js">const btn = document.querySelector('button');

function bgChange() {
  const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
}

btn.addEventListener('click', bgChange);</pre>

<div class="note">
<p><strong>Note:</strong> You can find the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/random-color-addeventlistener.html">full source code</a> for this example on GitHub (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/random-color-addeventlistener.html">see it running live</a>).</p>
</div>

<p>Inside the <code>addEventListener()</code> function, we specify two parameters: the name of the event we want to register this handler for, and the code that comprises the handler function we want to run in response to it. Note: It is perfectly appropriate to put all the code inside the <code>addEventListener()</code> function, in an anonymous function, like this:</p>

<pre class="brush: js">btn.addEventListener('click', function() {
  var rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
});</pre>

<p>This mechanism has some advantages over the older mechanisms discussed here earlier. First, there is a counterpart function, <code><a href="/en-US/docs/Web/API/EventTarget/removeEventListener">removeEventListener()</a></code>, which removes a previously added event handler. For example, this would remove the event handler set in the first code block in this section:</p>

<pre class="brush: js">btn.removeEventListener('click', bgChange);</pre>

<p>Event handlers can also be removed by passing an {{domxref("AbortSignal")}} to {{domxref("EventTarget/addEventListener()", "addEventListener()")}} and then, later, calling {{domxref("AbortController/abort()", "abort()")}} on the controller owning the <code>AbortSignal</code>. For example, to add an event handler that we can remove with an <code>AbortSignal</code>:</p>

<pre class="brush: js">const controller = new AbortController();
btn.addEventListener('click', function() {
  var rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  document.body.style.backgroundColor = rndCol;
}, { signal: controller.signal }); // pass an AbortSignal to this handler</pre>

<p>Then the event handler created by the code above can be removed like this:</p>

<pre class="brush: js">controller.abort(); // removes any/all event handlers associated with this controller</pre>

<p>For simple, small programs, cleaning up old, unused event handlers isn’t necessary — but for larger, more complex programs, it can improve efficiency. Plus, the ability to remove event handlers allows you to have the same button performing different actions in different circumstances — all you have to do is add or remove handlers.</p>

<p>The second advantage that {{domxref("EventTarget/addEventListener()", "addEventListener()")}} has over the older mechanisms discussed here earlier is that it allows you to register multiple handlers for the same listener. The following two handlers wouldn't both be applied:</p>

<pre class="brush: js">myElement.onclick = functionA;
myElement.onclick = functionB;</pre>

<p>The second line overwrites the value of <code>onclick</code> set by the first line. What would work, however, is the following:</p>

<pre class="brush: js">myElement.addEventListener('click', functionA);
myElement.addEventListener('click', functionB);</pre>

<p>Both functions would now run when the element is selected.</p>

<p>In addition, there are other powerful features and options available with this event mechanism. These are a little out of scope for this article, but if you want to read them, visit the <code><a href="/en-US/docs/Web/API/EventTarget/addEventListener">addEventListener()</a></code> and <code><a href="/en-US/docs/Web/API/EventTarget/removeEventListener">removeEventListener()</a></code> reference pages.</p>

<h3 id="What_mechanism_should_I_use">What mechanism should I use?</h3>

<p>There are only two methods you should consider for handling events:</p>

<ul>
 <li><a href="/en-US/docs/Web/Events/Event_handlers#using_onevent_properties">Event handler properties</a> have less power and options, but are easier to use. So as you begin learning, you might want to start with these.</li>
 <li><a href="/en-US/docs/Web/Events/Event_handlers#eventtarget.addeventlistener"><code>addEventListener()</code></a> is more complex, but also more powerful. So after you’ve begun learning, you should try to use it where possible.</li>
</ul>

<p>The main advantages of <code>addEventListener()</code> are that:</p>

<ul>
 <li>You can remove event-handler code if needed, using <code>removeEventListener()</code>.</li>
 <li>You can add multiple listeners of the same type to elements, if required.</li>
</ul>

<p>For example, you can call <code>addEventListener('click', function() { ... })</code> on an element multiple times, with different functions specified in the second argument. This is impossible with event handler properties because any subsequent attempts to set a property will overwrite earlier ones, e.g.:</p>

<pre class="brush: js">element.onclick = function1;
element.onclick = function2;
etc.</pre>

<p>You should never use the HTML event handler attributes — those are outdated, and using them is bad practice.</p>

<h2 id="Other_event_concepts">Other event concepts</h2>

<p>In this section, we briefly cover some advanced concepts that are relevant to events. It is not important to understand these concepts fully at this point, but they might serve to explain some code patterns you'll likely come across.</p>

<h3 id="Event_objects">Event objects</h3>

<p>Sometimes inside an event handler function, you'll see a parameter specified with a name such as <code>event</code>, <code>evt</code>, or <code>e</code>. This is called the <strong>event object</strong>, and it is automatically passed to event handlers to provide extra features and information. For example, let's rewrite our random color example again slightly:</p>

<pre class="brush: js">function bgChange(e) {
  const rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  e.target.style.backgroundColor = rndCol;
  console.log(e);
}

btn.addEventListener('click', bgChange);</pre>

<div class="notecard note">
  <p><strong>Note:</strong> You can find the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/random-color-eventobject.html">full source code</a> for this example on GitHub (also <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/random-color-eventobject.html">see it running live</a>).</p>
</div>

<p>Here you can see we are including an event object, <strong>e</strong>, in the function, and in the function setting a background color style on <code>e.target</code> — which is the button itself. The <code>target</code> property of the event object is always a reference to the element the event occurred upon. So, in this example, we are setting a random background color on the button, not the page.</p>

<div class="notecard note">
  <p><strong>Note:</strong> You can use any name you like for the event object — you just need to choose a name that you can then use to reference it inside the event handler function. <code>e</code>/<code>evt</code>/<code>event</code> are most commonly used by developers because they are short and easy to remember. It's always good to be consistent — with yourself, and with others if possible.</p>
</div>

<h4>Using event targets</h4>

<p><code>e.target</code> is incredibly useful when you want to set the same event handler on multiple elements and do something to all of them when an event occurs on them. You might, for example, have a set of 16 tiles that disappear when selected. It is useful to always be able to just set the thing to disappear as <code>e.target</code>, rather than having to select it in some more difficult way. In the following example (see <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/useful-eventtarget.html">useful-eventtarget.html</a> for the full source code; also see it <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/useful-eventtarget.html">running live</a> here), we create 16 {{htmlelement("div")}} elements using JavaScript. We then select all of them using {{domxref("document.querySelectorAll()")}}, then loop through each one, adding an <code>onclick</code> handler to each that makes it so that a random color is applied to each one when selected:</p>

<pre class="brush: js">
for (let i = 1; i &lt;= 16; i++) {
  const myDiv = document.createElement('div');
  myDiv.style.backgroundColor = "red";
  document.body.appendChild(myDiv);
}

function random(number) {
  return Math.floor(Math.random()*number);
}

function bgChange() {
  var rndCol = 'rgb(' + random(255) + ',' + random(255) + ',' + random(255) + ')';
  return rndCol;
}

const divs = document.querySelectorAll('div');

for (let i = 0; i &lt; divs.length; i++) {
  divs[i].onclick = function(e) {
    e.target.style.backgroundColor = bgChange();
  }
}</pre>

<p>The output is as follows (try clicking around on it — have fun):</p>

<pre class="brush: css hidden">
div {
  height: 100px;
  width: 25%;
  float: left;
}</pre>

<p>{{ EmbedLiveSample('Using_event_targets', '100%', 430, "", "") }}</p>

<p>Most event handlers you'll encounter have a standard set of properties and functions (methods) available on the event object; see the {{domxref("Event")}} object reference for a full list. Some more advanced handlers, however, add specialist properties containing extra data that they need to function. The <a href="/en-US/docs/Web/API/MediaStream_Recording_API">Media Recorder API</a>, for example, has a <code>dataavailable</code> event, which fires when some audio or video has been recorded and is available for doing something with (for example saving it, or playing it back). The corresponding <a href="/en-US/docs/Web/API/MediaRecorder/ondataavailable">ondataavailable</a> handler's event object has a <code>data</code> property available containing the recorded audio or video data to allow you to access it and do something with it.</p>

<h3 id="Preventing_default_behavior">Preventing default behavior</h3>

<p>Sometimes, you'll come across a situation where you want to prevent an event from doing what it does by default. The most common example is that of a web form, for example, a custom registration form. When you fill in the details and select the submit button, the natural behavior is for the data to be submitted to a specified page on the server for processing, and the browser to be redirected to a "success message" page of some kind (or the same page, if another is not specified.)</p>

<p>The trouble comes when the user has not submitted the data correctly — as a developer, you want to prevent the submission to the server and give an error message saying what's wrong and what needs to be done to put things right. Some browsers support automatic form data validation features, but since many don't, you are advised to not rely on those and implement your own validation checks. Let's look at a simple example.</p>

<p>First, a simple HTML form that requires you to enter your first and last name:</p>

<pre class="brush: html">&lt;form&gt;
  &lt;div&gt;
    &lt;label for="fname"&gt;First name: &lt;/label&gt;
    &lt;input id="fname" type="text"&gt;
  &lt;/div&gt;
  &lt;div&gt;
    &lt;label for="lname"&gt;Last name: &lt;/label&gt;
    &lt;input id="lname" type="text"&gt;
  &lt;/div&gt;
  &lt;div&gt;
     &lt;input id="submit" type="submit"&gt;
  &lt;/div&gt;
&lt;/form&gt;
&lt;p&gt;&lt;/p&gt;</pre>

<pre class="brush: css hidden">div {
  margin-bottom: 10px;
}
</pre>

<p>Now some JavaScript — here we implement a very simple check inside an <code><a href="/en-US/docs/Web/API/GlobalEventHandlers/onsubmit">onsubmit</a></code> event handler (the submit event is fired on a form when it is submitted) that tests whether the text fields are empty. If they are, we call the <code><a href="/en-US/docs/Web/API/Event/preventDefault">preventDefault()</a></code> function on the event object — which stops the form submission — and then display an error message in the paragraph below our form to tell the user what's wrong:</p>

<pre class="brush: js">const form = document.querySelector('form');
const fname = document.getElementById('fname');
const lname = document.getElementById('lname');
const para = document.querySelector('p');

form.onsubmit = function(e) {
  if (fname.value === '' || lname.value === '') {
    e.preventDefault();
    para.textContent = 'You need to fill in both names!';
  }
}</pre>

<p>Obviously, this is pretty weak form validation — it wouldn't stop the user validating the form with spaces or numbers entered into the fields, for example — but it is OK for example purposes. The output is as follows:</p>

<p>{{ EmbedLiveSample('Preventing_default_behavior', '100%', 140, "", "") }}</p>

<div class="notecard note">
  <p><strong>Note:</strong> for the full source code, see <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/preventdefault-validation.html">preventdefault-validation.html</a> (also see it <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/preventdefault-validation.html">running live</a> here.)</p>
</div>

<h3 id="Event_bubbling_and_capture">Event bubbling and capture</h3>

<p>The final subject to cover here is something that you won't come across often, but it can be a real pain if you don't understand it. Event bubbling and capture are two mechanisms that describe what happens when two handlers of the same event type are activated on one element.</p>

<h4>Video player example</h4>

<p>Let's look at an example to make this easier — open up the <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/show-video-box.html">show-video-box.html</a> example in a new tab (and the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/show-video-box.html">source code</a> in another tab.) It is also available live below:</p>

<p>{{ EmbedLiveSample('Video_player_example', '100%', 500, "", "") }}</p>

<p>This example shows and hides a {{htmlelement("div")}} with a {{htmlelement("video")}} element inside it:</p>

<pre class="brush: html">&lt;button&gt;Display video&lt;/button&gt;

&lt;div class="hidden"&gt;
  &lt;video&gt;
    &lt;source src="https://raw.githubusercontent.com/mdn/learning-area/master/javascript/building-blocks/events/rabbit320.mp4" type="video/mp4"&gt;
    &lt;source src="https://raw.githubusercontent.com/mdn/learning-area/master/javascript/building-blocks/events/rabbit320.webm" type="video/webm"&gt;
    &lt;p&gt;Your browser doesn't support HTML5 video. Here is a &lt;a href="rabbit320.mp4"&gt;link to the video&lt;/a&gt; instead.&lt;/p&gt;
  &lt;/video&gt;
&lt;/div&gt;
</pre>

<p>When the {{htmlelement("button")}} is clicked, the video is displayed, by changing the class attribute on the <code>&lt;div&gt;</code> from <code>hidden</code> to <code>showing</code> (the example's CSS contains these two classes, which position the box off the screen and on the screen, respectively):</p>

<pre class="brush: js">
  const btn = document.querySelector('button');
  const videoBox = document.querySelector('div');

  btn.onclick = function() {
    displayVideo();
  }

  function displayVideo() {
    if(videoBox.getAttribute('class') === 'hidden') {
      videoBox.setAttribute('class','showing');
    }
  }

  videoBox.addEventListener('click',function() {
    videoBox.setAttribute('class','hidden');
  });
</pre>


<pre class="brush: css hidden">
div {
  position: absolute;
  top: 50%;
  transform: translate(-50%,-50%);
  width: 480px;
  height: 380px;
  border-radius: 10px;
  background-color: #eee;
  background-image: linear-gradient(to bottom, rgba(0,0,0,0), rgba(0,0,0,0.1));
}

.hidden {
  left: -50%;
}

.showing {
  left: 50%;
}

div video {
  display: block;
  width: 400px;
  margin: 40px auto;
}
</pre>

<p>We then add a couple more <code>onclick</code> event handlers — the first one to the <code>&lt;div&gt;</code> and the second one to the <code>&lt;video&gt;</code>:</p>

<pre class="brush: js">videoBox.onclick = function() {
  videoBox.setAttribute('class', 'hidden');
};

const video = document.querySelector('video');

video.onclick = function() {
  video.play();
};</pre>

<p>Now, when the area of the <code>&lt;div&gt;</code> outside the video is selected, the box should be hidden again and when the video itself is selected, the video should start to play.</p>

<p>But there's a problem — currently, when you select the video it starts to play, but it causes the <code>&lt;div&gt;</code> to be hidden at the same time. This is because the video is inside the <code>&lt;div&gt;</code> — it is part of it — so selecting the video actually runs <em>both</em> the above event handlers.</p>

<h4 id="Bubbling_and_capturing_explained">Bubbling and capturing explained</h4>

<p>When an event is fired on an element that has parent elements (in this case, the {{htmlelement("video")}} has the {{htmlelement("div")}} as a parent), modern browsers run three different phases — the <strong>capturing</strong> phase, the <strong>target</strong> phase, and the <strong>bubbling</strong> phase.</p>

<p>In the <strong>capturing</strong> phase:</p>

<ul>
 <li>The browser checks to see if the element's outer-most ancestor ({{htmlelement("html")}}) has an <code>onclick</code> event handler registered on it for the capturing phase, and runs it if so.</li>
 <li>Then it moves on to the next element inside <code>&lt;html&gt;</code> and does the same thing, then the next one, and so on until it reaches the direct parent of the element that was actually selected.</li>
</ul>

<p>In the <strong>target</strong> phase:</p>
<ul>
 <li>The browser checks to see if the {{domxref("Event.target", "target")}} property has an event handler for the <code>click</code> event registered on it, and runs it if so.</li>
 <li>Then, if {{domxref("Event.bubbles", "bubbles")}} is <code>true</code>, it propagates the event to the direct parent of the selected element, then the next one, and so on until it reaches the <code>&lt;html&gt;</code> element. Otherwise, if {{domxref("Event.bubbles", "bubbles")}} is <code>false</code>, it doesn’t propagate the event to any ancestors of the target.</li>
</ul>

<p>In the <strong>bubbling</strong> phase, the exact opposite of the <strong>capturing</strong> phase occurs:</p>

<ul>
 <li>The browser checks to see if the direct parent of the element selected has an <code>onclick</code> event handler registered on it for the bubbling phase, and runs it if so.</li>
 <li>Then it moves on to the next immediate ancestor element and does the same thing, then the next one, and so on until it reaches the <code>&lt;html&gt;</code> element.</li>
</ul>

<p>In modern browsers, by default, all event handlers are registered for the bubbling phase. So in our current example, when you select the video, the event bubbles from the <code>&lt;video&gt;</code> element outwards to the <code>&lt;html&gt;</code> element. Along the way:</p>

<ul>
 <li>It finds the <code>video.onclick...</code> handler and runs it, so the video first starts playing.</li>
 <li>It then finds the <code>videoBox.onclick...</code> handler and runs it, so the video is hidden as well.</li>
</ul>

<div class="notecard note">
  <p><strong>Note:</strong> All JavaScript events go through the capturing and target phases. Whether an event enters the bubbling phase can be checked by the read-only {{domxref("Event.bubbles", "bubbles")}} property.</p>
</div>

<div class="notecard note">
  <p><strong>Note:</strong> Event listeners registered for the <code>&lt;html&gt;</code> element aren’t at the top of hierarchy. For example, event listeners registered for the {{domxref("Window", "window")}} and {{domxref("Document", "document")}} objects are higher in the hierarchy.</p>
</div>

<p>The following example demonstrates the behavior described above. Hover over the numbers and click on them to trigger events, and then observe the output that gets logged.</p>

{{EmbedLiveSample("Example_code_event_phases", "85ch", "400")}}

<h5>Example code: event phases</h5>

<pre class="brush: html">
&lt;div&gt;1
    &lt;div&gt;2
        &lt;div&gt;3
            &lt;div&gt;4
                &lt;div&gt;5&lt;/div&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
&lt;/div&gt;
&lt;button id="clear"&gt;clear output&lt;/button&gt;
&lt;section id="log"&gt;&lt;/section&gt;
</pre>

<pre class="brush: css">
p {
    line-height: 0;
}

div {
    display: inline-block;
    padding: 5px;

    background: #fff;
    border: 1px solid #aaa;
    cursor: pointer;
}

div:hover {
    border: 1px solid #faa;
    background: #fdd;
}
</pre>

<pre class="brush: js">
/*
 * source 1: https://dom.spec.whatwg.org/#dom-event-eventphase
 * source 2: https://stackoverflow.com/a/4616720/15266715
*/
let evtPhasestr = ["NONE: ", "CAPTURING_PHASE: ", "AT_TARGET: ", "BUBBLING_PHASE: "];
var logElement = document.getElementById('log');

function log(msg) {
    logElement.innerHTML += ('&lt;p&gt;' + msg + '&lt;/p&gt;');
}

function phase(evt) {
    log(evtPhasestr[evt.eventPhase] + this.firstChild.nodeValue.trim());
}
function gphase(evt) {
    log(evtPhasestr[evt.eventPhase] + evt.currentTarget)
}

function clearOutput() {
    logElement.innerHTML = "";
}

var divs = document.getElementsByTagName('div');
for (var i = 0; i &lt; divs.length; i++) {
    divs[i].addEventListener('click', phase, true);
    divs[i].addEventListener('click', phase, false);
}
document.addEventListener('click', gphase, true);
document.addEventListener('click', gphase, false);
window.addEventListener('click', gphase, true);
window.addEventListener('click', gphase, false);

var clearButton = document.getElementById('clear');
clearButton.addEventListener('click', clearOutput);
</pre>

<h4 id="Fixing_the_problem_with_stopPropagation">Fixing the problem with stopPropagation()</h4>

<p>This is a very annoying behavior, but there is a way to fix it! The standard <code><a href="/en-US/docs/Web/API/Event">Event</a></code> object has a function available on it called <code><a href="/en-US/docs/Web/API/Event/stopPropagation">stopPropagation()</a></code> which, when invoked on a handler's event object, makes it so that first handler is run but the event doesn't bubble any further up the chain, so no more handlers will be run.</p>

<p>We can, therefore, fix our current problem by changing the second handler function in the previous code block to this:</p>

<pre class="brush: js">video.onclick = function(e) {
  e.stopPropagation();
  video.play();
};</pre>

<p>You can try making a local copy of the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/show-video-box.html">show-video-box.html source code</a> and fixing it yourself, or looking at the fixed result in <a href="https://mdn.github.io/learning-area/javascript/building-blocks/events/show-video-box-fixed.html">show-video-box-fixed.html</a> (also see the <a href="https://github.com/mdn/learning-area/blob/master/javascript/building-blocks/events/show-video-box-fixed.html">source code</a> here).</p>

<div class="notecard note">
  <p><strong>Note:</strong> Why bother with both capturing and bubbling? Well, in the bad old days when browsers were much less cross-compatible than they are now, Netscape only used event capturing, and Internet Explorer used only event bubbling. When the W3C decided to try to standardize the behavior and reach a consensus, they ended up with this system that included both, which is the one modern browsers implemented.</p>
</div>

<div class="notecard note">
  <p><strong>Note:</strong> As mentioned above, by default all event handlers are registered in the bubbling phase, and this makes more sense most of the time. If you really want to register an event in the capturing phase instead, you can do so by registering your handler using <code><a href="/en-US/docs/Web/API/EventTarget/addEventListener">addEventListener()</a></code>, and setting the optional third property to <code>true</code>.</p>
</div>

<h4 id="Event_delegation">Event delegation</h4>

<p>Bubbling also allows us to take advantage of <strong>event delegation</strong> — this concept relies on the fact that if you want some code to run when you select any one of a large number of child elements, you can set the event listener on their parent and have events that happen on them bubble up to their parent rather than having to set the event listener on every child individually. Remember, bubbling involves checking the element the event is fired on for an event handler first, then moving up to the element's parent, etc.</p>

<p>A good example is a series of list items — if you want each one to pop up a message when selected, you can set the <code>click</code> event listener on the parent <code>&lt;ul&gt;</code>, and events will bubble from the list items to the <code>&lt;ul&gt;</code>.</p>

<p>This concept is explained further on David Walsh's blog, with multiple examples — see <a href="https://davidwalsh.name/event-delegate">How JavaScript Event Delegation Works</a>.</p>

<h2 id="Test_your_skills!">Test your skills!</h2>

<p>You've reached the end of this article, but can you remember the most important information? To verify you've retained this information before you move on — see <a href="/en-US/docs/Learn/JavaScript/Building_blocks/Test_your_skills:_Events">Test your skills: Events</a>.</p>

<h2 id="Conclusion">Conclusion</h2>

<p>You should now know all you need to know about web events at this early stage. As mentioned, events are not really part of the core JavaScript — they are defined in browser Web APIs.</p>

<p>Also, it is important to understand that the different contexts in which JavaScript is used have different event models — from Web APIs to other areas such as browser WebExtensions and Node.js (server-side JavaScript). We are not expecting you to understand all of these areas now, but it certainly helps to understand the basics of events as you forge ahead with learning web development.</p>

<p>If there is anything you didn't understand, feel free to read through the article again, or <a href="https://discourse.mozilla.org/c/mdn/learn">contact us</a> to ask for help.</p>

<h2 id="See_also">See also</h2>

<ul>
 <li><a href="https://domevents.dev/">domevents.dev</a> — a very useful interactive playground app that enables learning about the behavior of the DOM Event system through exploration.</li>
 <li><a href="/en-US/docs/Web/Events">Event reference</a></li>
 <li><a href="https://www.quirksmode.org/js/events_order.html">Event order</a> (discussion of capturing and bubbling) — an excellently detailed piece by Peter-Paul Koch.</li>
 <li><a href="https://www.quirksmode.org/js/events_access.html">Event accessing</a> (discussion of the event object) — another excellently detailed piece by Peter-Paul Koch.</li>
</ul>

<p>{{PreviousMenuNext("Learn/JavaScript/Building_blocks/Return_values","Learn/JavaScript/Building_blocks/Image_gallery", "Learn/JavaScript/Building_blocks")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/conditionals">Making decisions in your code — conditionals</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Looping_code">Looping code</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Functions">Functions — reusable blocks of code</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Build_your_own_function">Build your own function</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Return_values">Function return values</a></li>
 <li><strong>Introduction to events</strong></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Building_blocks/Image_gallery">Image gallery</a></li>
</ul>