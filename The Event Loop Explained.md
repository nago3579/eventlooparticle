---


---

<h1 id="the-event-loop-explained">The Event Loop Explained</h1>
<h3 id="parallel-processing">Parallel Processing</h3>
<p>In the early 2000s, CPU manufacturers hit a wall. At the time, clock cycles were king. The more cycles your CPU could produce, the more instructions it could preform a second, and the faster the machine. But with increased cycles came increased heat - too much to dissipate. Clever manufactures instead increased the number of cores, allowing for performance-seeking processes to take up a whole core and leave the rest for other tasks. Clever programmers used clever languages to access these other cores and tease out speed.  Nowadays, those same clever developers also use these multiple cores for synchronous tasks like background processes or running two applications at once.</p>
<p>Javascript is not that clever. JS a single-threaded language, meaning that it has access to only one core to execute one line of instructions. This is a big limitation. For example, if there was a timer on a website and some other JS event were to happen, such as an animation, the timer would halt while the browser handles the other instructions. <strong>Javascript’s workaround is the event loop.</strong></p>
<h3 id="event-loop">Event Loop</h3>
<p>The event loop isn’t just for Javascript. It’s the standard that defines how a web browser front end works.<br>
<img src="https://media.giphy.com/media/RhBsyPkh1BC87DSIJe/giphy.gif" alt="event loop 1"><br>
The purple ball represents the current state of the event loop. Like a movie, the browser redraws the screen every frame. Between frames Javascript runs. When the script event ends, control is passed back to the browser to redraw any changes that happen. This keeps animations fluid while still computing the Javacript in the background.</p>
<h3 id="clogging-the-drain">Clogging the Drain</h3>
<p>In a new tab, try pasting the following code into your <a href="https://kb.mailster.co/how-can-i-open-the-browsers-console/">browser’s console</a>. You’ll have to end the tab’s process after.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">while</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">{</span> console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Section is cool"</span><span class="token punctuation">)</span> <span class="token punctuation">}</span>
</code></pre>
<p>Try selecting text or clicking on buttons. Can’t? Lets see why.</p>
<p>Here’s the event loop while our code is running.</p>
<p><img src="https://i.imgur.com/C1P1MR7.jpg" alt="blocked event loop"><br>
It’s stuck in our Javascript infinite loop. The website can’t re-render or handle other events, like clicks, because we haven’t given control back to the browser.</p>
<p>This is called blocking the event loop. Long tasks like loops can unintentionally block the event loop, and make web apps and site feel slow and unresponsive.</p>
<h3 id="unclogging-the-drain">Unclogging the Drain</h3>
<p>Fortunately, we have tools to get around this. Copy this code into a browser console.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">let</span> loop <span class="token operator">=</span> <span class="token function">setInterval</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span> 
	console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Section is cool"</span><span class="token punctuation">)</span> 
<span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">)</span>
</code></pre>
<p>In this case, we use <code>setInterval(foo, 0)</code>. This adds a callback to the callback queue. Functions like <code>setInterval</code>, <code>setTimeout</code>,  and <em>promises</em> put code on the queue. When all tasks end, Javascript will start running code put on the queue in a first in first out structure. We can use these asynchronous tools to write non-blocking code.</p>
<h3 id="welcome-to-hell">Welcome to Hell</h3>
<p>Asynchronous Javascript is not perfect. Some Javascript developers describe callback hell when code contains many functions, usually nested, that add callbacks to the callback queue. This means some functions are not resolved top to bottom, and are instead are called at a later time. Take the following code for example.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">function</span> <span class="token function">callbackFunction</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
	console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"called foo"</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>

<span class="token function">foo</span><span class="token punctuation">(</span>callbackFunction<span class="token punctuation">)</span> <span class="token comment">//calls callbackFunction as async</span>

console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"finished script"</span><span class="token punctuation">)</span>
</code></pre>
<p>While seemingly innocuous, this code has some interesting aspects to it. Due to hoisting (a subject for another article), the function callbackFunction is defined. Then we invoke foo. This places callbackFunction on the callback stack. “finished script” is now logged. Now that the stack is clear, callbackFunction can now be invoked and print “called foo” to the log. We’ll end up with a console log that looks like</p>
<pre class=" language-javascript"><code class="prism  language-javascript">finished script
called foo
</code></pre>
<p>This non-linear execution is difficult to understand, and leads to callback hell and undefined behavior.</p>
<h3 id="importance">Importance</h3>
<p>As browsers and Javascript engines become faster and more powerful, client-side Javascript is more efficient. Non-blocking asynchronous code is crucial for responsive web apps that use web/server requests or complex processes during run-time.</p>
<p>Understanding the event loop is the first step to creating great web-based software.</p>

