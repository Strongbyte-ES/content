---
title: 'Cooperative asynchronous JavaScript: Timeouts and intervals'
slug: Learn/JavaScript/Asynchronous/Timeouts_and_intervals
tags:
  - Animation
  - Beginner
  - CodingScripting
  - Guide
  - Intervals
  - JavaScript
  - Loops
  - asynchronous
  - requestAnimationFrame
  - setInterval
  - setTimeout
  - timeouts
---
<div>{{LearnSidebar}}</div>

<div>{{PreviousMenuNext("Learn/JavaScript/Asynchronous/Introducing", "Learn/JavaScript/Asynchronous/Promises", "Learn/JavaScript/Asynchronous")}}</div>

<p>This tutorial looks at the traditional methods JavaScript has available for running code asynchronously after a set time period has elapsed, or at a regular interval (e.g. a set number of times per second), discusses what they are useful for, and considers their inherent issues.</p>

<table>
 <tbody>
  <tr>
   <th scope="row">Prerequisites:</th>
   <td>Basic computer literacy, a reasonable understanding of JavaScript fundamentals.</td>
  </tr>
  <tr>
   <th scope="row">Objective:</th>
   <td>To understand asynchronous loops and intervals and what they are useful for.</td>
  </tr>
 </tbody>
</table>

<h2 id="Introduction">Introduction</h2>

<p>For a long time, the web platform has offered JavaScript programmers a number of functions that allow them to asynchronously execute code after a certain time interval has elapsed, and to repeatedly execute a block of code asynchronously until you tell it to stop.</p>

<p>These functions are:</p>

<dl>
 <dt><code><a href="/en-US/docs/Web/API/setTimeout">setTimeout()</a></code></dt>
 <dd>Execute a specified block of code once after a specified time has elapsed.</dd>
 <dt><code><a href="/en-US/docs/Web/API/setInterval">setInterval()</a></code></dt>
 <dd>Execute a specified block of code repeatedly with a fixed time delay between each call.</dd>
 <dt><code><a href="/en-US/docs/Web/API/window/requestAnimationFrame">requestAnimationFrame()</a></code></dt>
 <dd>The modern version of <code>setInterval()</code>. Executes a specified block of code before the browser next repaints the display, allowing an animation to be run at a suitable framerate regardless of the environment it is being run in.</dd>
</dl>

<p>The asynchronous code set up by these functions runs on the main thread (after their specified timer has elapsed).</p>

<p>It's important to know that you can (and often will) run other code before a <code>setTimeout()</code> call executes, or between iterations of <code>setInterval()</code>. Depending on how processor-intensive these operations are, they can delay your async code even further, as any async code will execute only <em>after</em> the main thread is available. (In other words, when the stack is empty.)  You will learn more on this matter as you progress through this article.</p>

<p>In any case, these functions are used for running constant animations and other background processing on a web site or application. In the following sections we will show you how they can be used.</p>

<h2 id="setTimeout">setTimeout()</h2>

<p>As we said before, <code><a href="/en-US/docs/Web/API/setTimeout">setTimeout()</a></code> executes a particular block of code once after a specified time has elapsed. It takes the following parameters:</p>

<ul>
 <li>A function to run, or a reference to a function defined elsewhere.</li>
 <li>A number representing the time interval in milliseconds (1000 milliseconds equals 1 second) to wait before executing the code. If you specify a value of <code>0</code> (or omit the value), the function will run as soon as possible. (See the note below on why it runs "as soon as possible" and not "immediately".) More on why you might want to do this later.</li>
 <li>Zero or more values that represent any parameters you want to pass to the function when it is run.</li>
</ul>

<div class="notecard note">
<p><strong>Note:</strong> The specified amount of time (or the delay) is <strong>not</strong> the <em>guaranteed time</em> to execution, but rather the <em>minimum time</em> to execution. The callbacks you pass to these functions cannot run until the stack on the main thread is empty.</p>

<p>As a consequence, code like <code>setTimeout(fn, 0)</code> will execute as soon as the stack is empty, <strong>not</strong> immediately. If you execute code like <code>setTimeout(fn, 0)</code> but then immediately after run a loop that counts from 1 to 10 billion, your callback will be executed after a few seconds.</p>
</div>

<p>In the following example, the browser will wait two seconds before executing the anonymous function, then will display the alert message (<a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/simple-settimeout.html">see it running live</a>, and <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/simple-settimeout.html">see the source code</a>):</p>

<pre class="brush: js">let myGreeting = setTimeout(() => {
  alert('Hello, Mr. Universe!');
}, 2000);</pre>

<p>The functions you specify don't have to be anonymous. You can give your function a name, and even define it somewhere else and pass a function reference to the <code>setTimeout()</code>. The following two versions of the code snippet are equivalent to the first one:</p>

<pre class="brush: js">// With a named function
let myGreeting = setTimeout(function sayHi() {
  alert('Hello, Mr. Universe!');
}, 2000);

// With a function defined separately
function sayHi() {
  alert('Hello Mr. Universe!');
}

let myGreeting = setTimeout(sayHi, 2000);</pre>

<p>That can be useful if you have a function that needs to be called both from a timeout and in response to an event, for example. But it can also just help keep your code tidy, especially if the timeout callback is more than a few lines of code.</p>

<p><code>setTimeout()</code> returns an identifier value that can be used to refer to the timeout later, such as when you want to stop it. See {{anch("Clearing timeouts")}} (below) to learn how to do that.</p>

<h3 id="Passing_parameters_to_a_setTimeout_function">Passing parameters to a setTimeout() function</h3>

<p>Any parameters that you want to pass to the function being run inside the <code>setTimeout()</code> must be passed to it as additional parameters at the end of the list.</p>

<p>For example, you could refactor the previous function so that it will say hi to whatever person's name is passed to it:</p>

<pre class="brush: js">function sayHi(who) {
  alert(`Hello ${who}!`);
}</pre>

<p>Now, you can pass the name of the person into the <code>setTimeout()</code> call as a third parameter:</p>

<pre class="brush: js">let myGreeting = setTimeout(sayHi, 2000, 'Mr. Universe');</pre>

<h3 id="Clearing_timeouts">Clearing timeouts</h3>

<p>Finally, if a timeout has been created, you can cancel it before the specified time has elapsed by calling <code><a href="/en-US/docs/Web/API/clearTimeout">clearTimeout()</a></code>, passing it the identifier of the <code>setTimeout()</code> call as a parameter. So to cancel our above timeout, you'd do this:</p>

<pre class="brush: js">clearTimeout(myGreeting);</pre>

<div class="notecard note">
<p><strong>Note:</strong> See <code><a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/greeter-app.html">greeter-app.html</a></code> for a slightly more involved demo that allows you to set the name of the person to say hello to in a form, and cancel the greeting using a separate button (<a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/greeter-app.html">see the source code also</a>).</p>
</div>

<h2 id="setInterval">setInterval()</h2>

<p><code>setTimeout()</code> works perfectly when you need to run code once after a set period of time. But what happens when you need to run the code over and over again—for example, in the case of an animation?</p>

<p>This is where <code><a href="/en-US/docs/Web/API/setInterval">setInterval()</a></code> comes in. This works in a very similar way to <code>setTimeout()</code>, except that the function you pass as the first parameter is executed repeatedly at no less than the number of milliseconds given by the second parameter apart, rather than once. You can also pass any parameters required by the function being executed as subsequent parameters of the <code>setInterval()</code> call.</p>

<p>Let's look at an example. The following function creates a new <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date">Date()</a></code> object, extracts a time string out of it using <code><a href="/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date/toLocaleTimeString">toLocaleTimeString()</a></code>, and then displays it in the UI. It then runs the function once per second using <code>setInterval()</code>, creating the effect of a digital clock that updates once per second (<a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/setinterval-clock.html">see this live</a>, and also <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/setinterval-clock.html">see the source</a>):</p>

<pre class="brush: js">function displayTime() {
   let date = new Date();
   let time = date.toLocaleTimeString();
   document.getElementById('demo').textContent = time;
}

const createClock = setInterval(displayTime, 1000);</pre>

<p>Just like <code>setTimeout()</code>, <code>setInterval()</code> returns an identifying value you can use later when you need to clear the interval.</p>

<h3 id="Clearing_intervals">Clearing intervals</h3>

<p><code>setInterval()</code> keeps running a task forever, unless you do something about it. You'll probably want a way to stop such tasks, otherwise you may end up getting errors when the browser can't complete any further versions of the task, or if the animation being handled by the task has finished. You can do this the same way you stop timeouts — by passing the identifier returned by the <code>setInterval()</code> call to the <code><a href="/en-US/docs/Web/API/clearInterval">clearInterval()</a></code> function:</p>

<pre class="brush: js">const myInterval = setInterval(myFunction, 2000);

clearInterval(myInterval);</pre>

<h4 id="Active_learning_Creating_your_own_stopwatch!">Active learning: Creating your own stopwatch!</h4>

<p>With this all said, we've got a challenge for you. Take a copy of our <code><a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/setinterval-clock.html">setInterval-clock.html</a></code> example, and modify it to create your own simple stopwatch.</p>

<p>You need to display a time as before, but in this example, you need:</p>

<ul>
 <li>A "Start" button to start the stopwatch running.</li>
 <li>A "Stop" button to pause/stop it.</li>
 <li>A "Reset" button to reset the time back to <code>0</code>.</li>
 <li>The time display to show the number of seconds elapsed, rather than the actual time.</li>
</ul>

<p>Here's a few hints for you:</p>

<ul>
 <li>You can structure and style the button markup however you like; just make sure you use semantic HTML, with hooks to allow you to grab the button references using JavaScript.</li>
 <li>You probably want to create a variable that starts at <code>0</code>, then increments by one every second using a constant loop.</li>
 <li>It is easier to create this example without using a <code>Date()</code> object, like we've done in our version, but less accurate — you can't guarantee that the callback will fire after exactly <code>1000</code>ms. A more accurate way would be to run <code>startTime = Date.now()</code> to get a timestamp of exactly when the user clicked the start button, and then do <code>Date.now() - startTime</code> to get the number of milliseconds after the start button was clicked.</li>
 <li>You also want to calculate the number of hours, minutes, and seconds as separate values, and then show them together in a string after each loop iteration. From the second counter, you can work out each of these.</li>
 <li>How would you calculate them? Have a think about it:
  <ul>
   <li>The number of seconds in an hour is <code>3600</code>.</li>
   <li>The number of minutes will be the amount of seconds left over when all of the hours have been removed, divided by <code>60</code>.</li>
   <li>The number of seconds will be the amount of seconds left over when all of the minutes have been removed.</li>
  </ul>
 </li>
 <li>You'll want to include a leading zero on your display values if the amount is less than <code>10</code>, so it looks more like a traditional clock/watch.</li>
 <li>To pause the stopwatch, you'll want to clear the interval. To reset it, you'll want to set the counter back to <code>0</code>, clear the interval, and then immediately update the display.</li>
 <li>You probably ought to disable the start button after pressing it once, and enable it again after you've stopped/reset it. Otherwise multiple presses of the start button will apply multiple <code>setInterval()</code>s to the clock, leading to wrong behavior.</li>
</ul>

<div class="notecard note">
<p><strong>Note:</strong> If you get stuck, you can <a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/setinterval-stopwatch.html">find our version here</a> (see the <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/setinterval-stopwatch.html">source code</a> also).</p>
</div>

<h2 id="Things_to_keep_in_mind_about_setTimeout_and_setInterval">Things to keep in mind about setTimeout() and setInterval()</h2>

<p>There are a few things to keep in mind when working with <code>setTimeout()</code> and <code>setInterval()</code>. Let's review these now.</p>

<h3 id="Recursive_timeouts">Recursive timeouts</h3>

<p>There is another way to use <code>setTimeout()</code>: you can call it recursively to run the same code repeatedly, instead of using <code>setInterval()</code>.</p>

<p>The below example uses a recursive <code>setTimeout()</code> to run the passed function every <code>100</code> milliseconds:</p>

<pre class="brush: js">let i = 1;

setTimeout(function run() {
  console.log(i);
  i++;
  setTimeout(run, 100);
}, 100);</pre>

<p>Compare the above example to the following one — this uses <code>setInterval()</code> to accomplish the same effect:</p>

<pre class="brush: js">let i = 1;

setInterval(function run() {
  console.log(i);
  i++;
}, 100);</pre>

<h4 id="How_do_recursive_setTimeout_and_setInterval_differ">How do recursive <code>setTimeout()</code> and <code>setInterval()</code> differ?</h4>

<p>The difference between the two versions of the above code is a subtle one.</p>

<ul>
 <li>Recursive <code>setTimeout()</code> guarantees the given delay between the code execution completion and the next call. The delay for the next execution will start counting only after the code has finished running, therefore <em>excluding</em> the time taken to run the code. In this example, the <code>100</code> milliseconds will be the delay between the <code>run</code> code finishing, and the next <code>run</code> call.</li>
 <li>The example using <code>setInterval()</code> does things somewhat differently. The interval you chose <em>includes</em> the time taken to execute the code you want to run in. Let's say that the code takes <code>40</code> milliseconds to run — the interval then ends up being only <code>60</code> milliseconds.</li>
 <li>When using <code>setTimeout()</code> recursively, each iteration can calculate a different delay before running the next iteration. In other words, the value of the second parameter can specify a different time in milliseconds to wait before running the code again.</li>
</ul>

<p>When your code has the potential to take longer to run than the time interval you’ve assigned, it’s better to use recursive <code>setTimeout()</code> — this will keep the time interval constant between executions regardless of how long the code takes to execute, and you won't get errors.</p>

<h3 id="Immediate_timeouts">Immediate timeouts</h3>

<p>Using <code>0</code> as the value for <code>setTimeout()</code> schedules the execution of the specified callback function as soon as possible but only after the main code thread has been run.</p>

<p>For instance, the code below (<a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/zero-settimeout.html">see it live</a>) outputs an alert containing <code>"Hello"</code>, then an alert containing <code>"World"</code> as soon as you click OK on the first alert.</p>

<pre class="brush: js">setTimeout(function() {
  alert('World');
}, 0);

alert('Hello');</pre>

<p>This can be useful in cases where you want to set a block of code to run as soon as all of the main thread has finished running — put it on the async event loop, so it will run straight afterwards.</p>

<h3 id="Clearing_with_clearTimeout_or_clearInterval">Clearing with clearTimeout() or clearInterval()</h3>

<p><code>clearTimeout()</code> and <code>clearInterval()</code> both use the same list of entries to clear from. Interestingly enough, this means that you can use either method to clear a <code>setTimeout()</code> or <code>setInterval()</code>.</p>

<p>For consistency, you should use <code>clearTimeout()</code> to clear <code>setTimeout()</code> entries and <code>clearInterval()</code> to clear <code>setInterval()</code> entries. This will help to avoid confusion.</p>

<h2 id="requestAnimationFrame">requestAnimationFrame()</h2>

<p><code><a href="/en-US/docs/Web/API/window/requestAnimationFrame">requestAnimationFrame()</a></code> is a specialized enqueueing function created for running animations efficiently in the browser. It runs a specified block of code before the browser next repaints the display, allowing the execution to be paired with the device's display frame rate.</p>

<p>It was created in response to perceived problems with previous async functions like <code>setInterval()</code>, which for example doesn't run at a frame rate optimized for the device, dropping frames in some cases. They also lacked some optimizations suited for animations, like stopping the execution if the tab isn't active or the animation is scrolled off the page, among other things.</p>

<p>(<a href="http://creativejs.com/resources/requestanimationframe/index.html">Read more about this on CreativeJS</a>.)</p>

<div class="notecard note">
<p><strong>Note:</strong> You can find examples of using <code>requestAnimationFrame()</code> elsewhere in the course — see for example <a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Drawing_graphics">Drawing graphics</a>, and <a href="/en-US/docs/Learn/JavaScript/Objects/Object_building_practice">Object building practice</a>.</p>
</div>

<p>The method takes as an argument a callback to be invoked before the repaint. This is the general pattern you'll see it used in:</p>

<pre class="brush: js">function draw() {
   // Drawing code goes here
   requestAnimationFrame(draw);
}

draw();</pre>

<p>The idea is to define a function in which your animation is updated (e.g. your sprites are moved, score is updated, data is refreshed, or whatever). Then, you call it to start the process off. At the end of the function block you call <code>requestAnimationFrame()</code> with the function reference passed as the parameter, and this instructs the browser to call the function again on the next display repaint. This is then run continuously, as the code is calling <code>requestAnimationFrame()</code> recursively.</p>

<div class="notecard note">
<p><strong>Note:</strong> If you want to perform some kind of simple constant DOM animation, <a href="/en-US/docs/Web/CSS/CSS_Animations">CSS Animations</a> are probably faster. They are calculated directly by the browser's internal code, rather than JavaScript.</p>

<p>If, however, you are doing something more complex and involving objects that are not directly accessible inside the DOM (such as <a href="/en-US/docs/Web/API/Canvas_API">2D Canvas API</a> or <a href="/en-US/docs/Web/API/WebGL_API">WebGL</a> objects), <code>requestAnimationFrame()</code> is the better option in most cases.</p>
</div>

<h3 id="How_fast_does_your_animation_run">How fast does your animation run?</h3>

<p>The smoothness of your animation is directly dependent on your animation's frame rate and it is measured in frames per second (fps). The higher this number is, the smoother your animation will look, to a point.</p>

<p>Since most screens have a refresh rate of 60Hz, the fastest frame rate you can aim for is 60 frames per second (FPS) when working with web browsers. However, more frames means more processing, which can often cause stuttering and skipping — also known as <em>dropping frames</em>, or <em>jank</em>.</p>

<p>If you have a monitor with a 60Hz refresh rate and you want to achieve 60 FPS you have about 16.7 milliseconds (<code>1000 / 60</code>) to execute your animation code to render each frame. This is a reminder that you'll need to be mindful of the amount of code that you try to run during each pass through the animation loop.</p>

<p><code>requestAnimationFrame()</code> always tries to get as close to this magic 60 FPS value as possible. Sometimes, it isn't possible — if you have a really complex animation and you are running it on a slow computer, your frame rate will be less. In all cases, <code>requestAnimationFrame()</code> will always do the best it can with what it has available.</p>

<h3 id="How_does_requestAnimationFrame_differ_from_setInterval_and_setTimeout">How does requestAnimationFrame() differ from setInterval() and setTimeout()?</h3>

<p>Let's talk a little bit more about how the <code>requestAnimationFrame()</code> method differs from the other methods used earlier. Looking at our code from above:</p>

<pre class="brush: js">function draw() {
   // Drawing code goes here
   requestAnimationFrame(draw);
}

draw();</pre>

<p>Let's now see how to do the same thing using <code>setInterval()</code>:</p>

<pre class="brush: js">function draw() {
   // Drawing code goes here
}

setInterval(draw, 17);</pre>

<p>As we covered earlier, you don't specify a time interval for <code>requestAnimationFrame()</code>. It just runs it as quickly and smoothly as possible in the current conditions. The browser also doesn't waste time running it if the animation is offscreen for some reason, etc.</p>

<p><code>setInterval()</code>, on the other hand <em>requires</em> an interval to be specified. We arrived at our final value of 17 via the formula <em>1000 milliseconds / 60Hz</em>, and then rounded it up. Rounding up is a good idea; if you rounded down, the browser might try to run the animation faster than 60 FPS, and it wouldn't make any difference to the animation's smoothness, anyway. As we said before, 60Hz is the standard refresh rate.</p>

<h3 id="Including_a_timestamp">Including a timestamp</h3>

<p>The actual callback passed to the <code>requestAnimationFrame()</code> function can be given a parameter, too: a <em>timestamp</em> value, that represents the time since the <code>requestAnimationFrame()</code> started running.</p>

<p>This is useful as it allows you to run things at specific times and at a constant pace, regardless of how fast or slow your device might be. The general pattern you'd use looks something like this:</p>

<pre class="brush: js">let startTime = null;

function draw(timestamp) {
    if (!startTime) {
      startTime = timestamp;
    }

   currentTime = timestamp - startTime;

   // Do something based on current time

   requestAnimationFrame(draw);
}

draw();</pre>

<h3 id="Browser_support">Browser support</h3>

<p><code>requestAnimationFrame()</code> is supported in more recent browsers than <code>setInterval()</code>/<code>setTimeout()</code>.  Interestingly, it is available in Internet Explorer 10 and above.</p>

<p>So, unless you need to support older versions of IE, there is little reason to not use <code>requestAnimationFrame()</code>.</p>

<h3 id="A_simple_example">A simple example</h3>

<p>Enough with the theory! Let's build your own personal <code>requestAnimationFrame()</code> example. You're going to create a simple "spinner animation"—the kind you might see displayed in an app when it is busy connecting to the server, etc.</p>

<div class="notecard note">
<p><strong>Note:</strong> In a real world example, you should probably use CSS animations to run this kind of simple animation. However, this kind of example is very useful to demonstrate <code>requestAnimationFrame()</code> usage, and you'd be more likely to use this kind of technique when doing something more complex such as updating the display of a game on each frame.</p>
</div>

<ol>
 <li>
  <p>Grab a basic HTML template (<a href="https://github.com/mdn/learning-area/blob/master/html/introduction-to-html/getting-started/index.html">such as this one</a>).</p>
 </li>
 <li>
  <p>Put an empty {{htmlelement("div")}} element inside the {{htmlelement("body")}}, then add a ↻ character inside it. This circular arrow character will act as our spinner for this example.</p>
 </li>
 <li>
  <p>Apply the following CSS to the HTML template (in whatever way you prefer). This sets a red background on the page, sets the <code>&lt;body&gt;</code> height to <code>100%</code> of the {{htmlelement("html")}} height, and centers the <code>&lt;div&gt;</code> inside the <code>&lt;body&gt;</code>, horizontally and vertically.</p>

  <pre class="brush: css">html {
  background-color: white;
  height: 100%;
}

body {
  height: inherit;
  background-color: red;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
}

div {
  display: inline-block;
  font-size: 10rem;
}</pre>
 </li>
 <li>
  <p>Insert a {{htmlelement("script")}} element just above the closing <code>&lt;/body&gt;</code> tag.</p>
 </li>
 <li>
  <p>Insert the following JavaScript inside your <code>&lt;script&gt;</code> element. Here, you're storing a reference to the <code>&lt;div&gt;</code> inside a constant, setting a <code>rotateCount</code> variable to <code>0</code>, setting an uninitialized variable that will later be used to contain a reference to the <code>requestAnimationFrame()</code> call, and setting a <code>startTime</code> variable to <code>null</code>, which will later be used to store the start time of the <code>requestAnimationFrame()</code>.</p>

  <pre class="brush: js">const spinner = document.querySelector('div');
let rotateCount = 0;
let startTime = null;
let rAF;
</pre>
 </li>
 <li>
  <p>Below the previous code, insert a <code>draw()</code> function that will be used to contain our animation code, which includes the <code>timestamp</code> parameter:</p>

  <pre class="brush: js">function draw(timestamp) {

}</pre>
 </li>
 <li>
  <p>Inside <code>draw()</code>, add the following lines. They will define the start time if it is not defined already (this will only happen on the first loop iteration), and set the <code>rotateCount</code> to a value to rotate the spinner by (the current timestamp, take the starting timestamp, divided by three so it doesn't go too fast):</p>

  <pre class="brush: js">  if (!startTime) {
   startTime = timestamp;
  }

  rotateCount = (timestamp - startTime) / 3;
</pre>
 </li>
 <li>
  <p>Below the previous line inside <code>draw()</code>, add the following block — this ensures that the value of <code>rotateCount</code> is between <code>0</code> and <code>359</code>, by setting the value to its modulo of <code>360</code> (i.e. the remainder left over when the value is divided by <code>360</code>) — so the circle animation can continue uninterrupted, at a sensible, low value. Note that this isn't strictly necessary, but it is easier to work with values of <code>0</code>–<code>359</code> degrees than values like <code>"128000 degrees"</code>.</p>

  <pre class="brush: js">  rotateCount %= 360; </pre>
 </li>
 <li>Next, below the previous block add the following line to actually rotate the spinner:
  <pre class="brush: js">spinner.style.transform = `rotate(${rotateCount}deg)`;</pre>
 </li>
 <li>
  <p>At the very bottom inside the <code>draw()</code> function, insert the following line. This is the key to the whole operation — you are setting the variable defined earlier to an active <code>requestAnimation()</code> call, which takes the <code>draw()</code> function as its parameter. This starts the animation off, constantly running the <code>draw()</code> function at a rate as near 60 FPS as possible.</p>

  <pre class="brush: js">rAF = requestAnimationFrame(draw);</pre>
 </li>
 <li>
  <p>Below the <code>draw()</code> function definition, add a call to the <code>draw()</code> function to start the animation.</p>

  <pre class="brush: js">draw();</pre>
 </li>
</ol>

<div class="notecard note">
<p><strong>Note:</strong> You can find the finished <a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/simple-raf-spinner.html">example live on GitHub</a>. (You can see the <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/simple-raf-spinner.html">source code</a>, also.)</p>
</div>

<h3 id="Clearing_a_requestAnimationFrame_call">Clearing a requestAnimationFrame() call</h3>

<p>Clearing a <code>requestAnimationFrame()</code> call can be done by calling the corresponding <code>cancelAnimationFrame()</code> method. (Note that the function name starts with "cancel", not "clear" as with the "set..." methods.) </p>

<p>Just pass it the value returned by the <code>requestAnimationFrame()</code> call to cancel, which you stored in the variable <code>rAF</code>:</p>

<pre class="brush: js">cancelAnimationFrame(rAF);</pre>

<h3 id="Active_learning_Starting_and_stopping_our_spinner">Active learning: Starting and stopping our spinner</h3>

<p>In this exercise, we'd like you to test out the <code>cancelAnimationFrame()</code> method by taking our previous example and updating it, adding an event listener to start and stop the spinner when the mouse is clicked anywhere on the page.</p>

<p>Some hints:</p>

<ul>
 <li>A <code>click</code> event handler can be added to most elements, including the document <code>&lt;body&gt;</code>. It makes more sense to put it on the <code>&lt;body&gt;</code> element if you want to maximize the clickable area — the event bubbles up to its child elements.</li>
 <li>You'll want to add a tracking variable to check whether the spinner is spinning or not, clearing the animation frame if it is, and calling it again if it isn't.</li>
</ul>

<div class="notecard note">
<p><strong>Note:</strong> Try this yourself first; if you get really stuck, check out of our <a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/start-and-stop-spinner.html">live example</a> and <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/start-and-stop-spinner.html">source code</a>.</p>
</div>

<h3 id="Throttling_a_requestAnimationFrame_animation">Throttling a requestAnimationFrame() animation</h3>

<p>One limitation of <code>requestAnimationFrame()</code> is that you can't choose your frame rate. This isn't a problem most of the time, as generally you want your animation to run as smoothly as possible. But what about when you want to create an old school, 8-bit-style animation?</p>

<p>This was a problem, for example, in the Monkey Island-inspired walking animation from our <a href="/en-US/docs/Learn/JavaScript/Client-side_web_APIs/Drawing_graphics">Drawing Graphics</a> article:</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/apis/drawing-graphics/loops_animation/7_canvas_walking_animation.html", '100%', 260)}}</p>

<p>In this example, you have to animate both the position of the character on the screen, and the sprite being shown. There are only 6 frames in the sprite's animation. If you showed a different sprite frame for every frame displayed on the screen by <code>requestAnimationFrame()</code>, Guybrush would move his limbs too fast and the animation would look ridiculous. This example therefore throttles the rate at which the sprite cycles its frames using the following code:</p>

<pre class="brush: js">if (posX % 13 === 0) {
  if (sprite === 5) {
    sprite = 0;
  } else {
    sprite++;
  }
}</pre>

<p>So the code only cycles the sprite once every 13 animation frames.</p>

<p>...Actually, it's about every 6.5 frames, as we update <code>posX</code> (character's position on the screen) by two each frame:</p>

<pre class="brush: js">if (posX &gt; width/2) {
  newStartPos = -( (width/2) + 102 );
  posX = Math.ceil(newStartPos / 13) * 13;
  console.log(posX);
} else {
  posX += 2;
}</pre>

<p>This is the code that calculates how to update the position in each animation frame.</p>

<p>The method you use to throttle your animation will depend on your particular code. For instance, in the earlier spinner example, you could make it appear to move slower by only increasing <code>rotateCount</code> by one on each frame, instead of two.</p>

<h2 id="Active_learning_a_reaction_game">Active learning: a reaction game</h2>

<p>For the final section of this article, you'll create a 2-player reaction game. The game will have two players, one of whom controls the game using the <kbd>A</kbd> key, and the other with the <kbd>L</kbd> key.</p>

<p>When the <em>Start</em> button is pressed, a spinner like the one we saw earlier is displayed for a random amount of time between 5 and 10 seconds. After that time, a message will appear saying <code>"PLAYERS GO!!"</code> — once this happens, the first player to press their control button will win the game.</p>

<p>{{EmbedGHLiveSample("learning-area/javascript/asynchronous/loops-and-intervals/reaction-game.html", '100%', 500)}}</p>

<p>Let's work through this:</p>

<ol>
 <li>
  <p>First of all, download the <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/reaction-game-starter.html">starter file for the app</a>. This contains the finished HTML structure and CSS styling, giving us a game board that shows the two players' information (as seen above), but with the spinner and results paragraph displayed on top of one another. You just have to write the JavaScript code.</p>
 </li>
 <li>
  <p>Inside the empty {{htmlelement("script")}} element on your page, start by adding the following lines of code that define some constants and variables you'll need in the rest of the code:</p>

  <pre class="brush: js">const spinner = document.querySelector('.spinner p');
const spinnerContainer = document.querySelector('.spinner');
let rotateCount = 0;
let startTime = null;
let rAF;
const btn = document.querySelector('button');
const result = document.querySelector('.result');</pre>

  <p>In order, these are:</p>

  <ol>
   <li>A reference to the spinner, so you can animate it.</li>
   <li>A reference to the {{htmlelement("div")}} element that contains the spinner, used for showing and hiding it.</li>
   <li>A rotate count. This determines how much you want to show the spinner rotated on each frame of the animation.</li>
   <li>A null start time. This will be populated with a start time when the spinner starts spinning.</li>
   <li>An uninitialized variable to later store the {{domxref("Window.requestAnimationFrame", "requestAnimationFrame()")}} call that animates the spinner.</li>
   <li>A reference to the Start button.</li>
   <li>A reference to the results paragraph.</li>
  </ol>
 </li>
 <li>
  <p>Next, below the previous lines of code, add the following function. It takes two numbers and returns a random number between the two. You'll need this to generate a random timeout interval later on.</p>

  <pre class="brush: js">function random(min,max) {
  var num = Math.floor(Math.random()*(max-min)) + min;
  return num;
}</pre>
 </li>
 <li>
  <p>Next add  the <code>draw()</code> function, which animates the spinner. This is very similar to the version from the simple spinner example, earlier:</p>

  <pre class="brush: js">function draw(timestamp) {
  if(!startTime) {
   startTime = timestamp;
  }

  rotateCount = (timestamp - startTime) / 3;

  rotateCount %= 360;

  spinner.style.transform = 'rotate(' + rotateCount + 'deg)';
  rAF = requestAnimationFrame(draw);
}</pre>
 </li>
 <li>
  <p>Now it is time to set up the initial state of the app when the page first loads. Add the following two lines, which hide the results paragraph and spinner container using <code>display: none;</code>.</p>

  <pre class="brush: js">result.style.display = 'none';
spinnerContainer.style.display = 'none';</pre>
 </li>
 <li>
  <p>Next, define a <code>reset()</code> function, which sets the app back to the original state required to start the game again after it has been played. Add the following at the bottom of your code:</p>

  <pre class="brush: js">function reset() {
  btn.style.display = 'block';
  result.textContent = '';
  result.style.display = 'none';
}</pre>
 </li>
 <li>
  <p>Okay, enough preparation!  It's time to make the game playable! Add the following block to your code. The <code>start()</code> function calls <code>draw()</code> to start the spinner spinning and display it in the UI, hides the <em>Start</em> button so you can't mess up the game by starting it multiple times concurrently, and runs a <code>setTimeout()</code> call that runs a <code>setEndgame()</code> function after a random interval between 5 and 10 seconds has passed. The following block also adds an event listener to your button to run the <code>start()</code> function when it is clicked.</p>

  <pre class="brush: js">btn.addEventListener('click', start);

function start() {
  draw();
  spinnerContainer.style.display = 'block';
  btn.style.display = 'none';
  setTimeout(setEndgame, random(5000,10000));
}</pre>

  <div class="notecard note">
  <p><strong>Note:</strong> You'll see this example is calling <code>setTimeout()</code> without storing the return value. (So, not <code>let myTimeout = setTimeout(functionName, interval)</code>.) </p>

  <p>This works just fine, as long as you don't need to clear your interval/timeout at any point. If you do, you'll need to save the returned identifier!</p>
  </div>

  <p>The net result of the previous code is that when the <em>Start</em> button is pressed, the spinner is shown and the players are made to wait a random amount of time before they are asked to press their button. This last part is handled by the <code>setEndgame()</code> function, which you'll define next.</p>
 </li>
 <li>
  <p>Add the following function to your code next:</p>

  <pre class="brush: js">function setEndgame() {
  cancelAnimationFrame(rAF);
  spinnerContainer.style.display = 'none';
  result.style.display = 'block';
  result.textContent = 'PLAYERS GO!!';

  document.addEventListener('keydown', keyHandler);

  function keyHandler(e) {
    let isOver = false;
    console.log(e.key);

    if (e.key === "a") {
      result.textContent = 'Player 1 won!!';
      isOver = true;
    } else if (e.key === "l") {
      result.textContent = 'Player 2 won!!';
      isOver = true;
    }

    if (isOver) {
      document.removeEventListener('keydown', keyHandler);
      setTimeout(reset, 5000);
    }
  };
}</pre>

  <p>Stepping through this:</p>

  <ol>
   <li>First, cancel the spinner animation with {{domxref("window.cancelAnimationFrame", "cancelAnimationFrame()")}} (it is always good to clean up unneeded processes), and hide the spinner container.</li>
   <li>Next, display the results paragraph and set its text content to "PLAYERS GO!!" to signal to the players that they can now press their button to win.</li>
   <li>Attach a <code><a href="/en-US/docs/Web/API/Document/keydown_event">keydown</a></code> event listener to the document. When any button is pressed down, the <code>keyHandler()</code> function is run.</li>
   <li>Inside <code>keyHandler()</code>, the code includes the event object as a parameter (represented by <code>e</code>) — its {{domxref("KeyboardEvent.key", "key")}} property contains the key that was just pressed, and you can use this to respond to specific key presses with specific actions.</li>
   <li>Set the variable <code>isOver</code> to false, so we can track whether the correct keys were pressed for player 1 or 2 to win. We don't want the game ending when a wrong key was pressed.</li>
   <li>Log <code>e.key</code> to the console, which is a useful way of finding out the <code>key</code> value of different keys you are pressing.</li>
   <li>When <code>e.key</code> is "a", display a message to say that Player 1 won, and when <code>e.key</code> is "l", display a message to say Player 2 won. (<strong>Note:</strong> This will only work with lowercase a and l — if an uppercase A or L is submitted (the key plus <kbd>Shift</kbd>), it is counted as a different key!) If one of these keys was pressed, set <code>isOver</code> to <code>true</code>.</li>
   <li>Only if <code>isOver</code> is <code>true</code>, remove the <code>keydown</code> event listener using {{domxref("EventTarget.removeEventListener", "removeEventListener()")}} so that once the winning press has happened, no more keyboard input is possible to mess up the final game result. You also use <code>setTimeout()</code> to call <code>reset()</code> after 5 seconds — as explained earlier, this function resets the game back to its original state so that a new game can be started.</li>
  </ol>
 </li>
</ol>

<p>That's it—you're all done!</p>

<div class="notecard note">
<p><strong>Note:</strong> If you get stuck, check out <a href="https://mdn.github.io/learning-area/javascript/asynchronous/loops-and-intervals/reaction-game.html">our version of the reaction game</a> (see the <a href="https://github.com/mdn/learning-area/blob/master/javascript/asynchronous/loops-and-intervals/reaction-game.html">source code</a> also).</p>
</div>

<h2 id="Conclusion">Conclusion</h2>

<p>So that's it — all the essentials of async loops and intervals covered in one article. You'll find these methods useful in a lot of situations, but take care not to overuse them! Because they still run on the main thread, heavy and intensive callbacks (especially those that manipulate the DOM) can really slow down a page if you're not careful.</p>

<p>{{PreviousMenuNext("Learn/JavaScript/Asynchronous/Introducing", "Learn/JavaScript/Asynchronous/Promises", "Learn/JavaScript/Asynchronous")}}</p>

<h2 id="In_this_module">In this module</h2>

<ul>
 <li><a href="/en-US/docs/Learn/JavaScript/Asynchronous/Concepts">General asynchronous programming concepts</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Asynchronous/Introducing">Introducing asynchronous JavaScript</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Asynchronous/Timeouts_and_intervals">Cooperative asynchronous JavaScript: Timeouts and intervals</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Asynchronous/Promises">Graceful asynchronous programming with Promises</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Asynchronous/Async_await">Making asynchronous programming easier with async and await</a></li>
 <li><a href="/en-US/docs/Learn/JavaScript/Asynchronous/Choosing_the_right_approach">Choosing the right approach</a></li>
</ul>