---


---

<h1 id="the-event-loop-explained">The Event Loop Explained</h1>
<h3 id="parallel-processing">Parallel Processing</h3>
<p>In the early 2000s, CPU manufacturers hit a wall. At the time, clock cycles were king. The more cycles your CPU could produce, the more instructions it could preform a second, and the faster the machine. With increased cycles came increased heat - too much to dissipate. Clever manufacturers instead opted to increase the number of cores, allowing for performance-seeking processes to access an entire core, leaving the rest for other tasks. Clever programmers used specialized languages to access these other cores and tease out speed.  Nowadays, those same clever developers use these multiple cores for synchronous tasks, such as background processes or running two applications at once.</p>
<p>Javascript (JS) is not that clever. JS is a single-threaded language, meaning that it can access to only one core to execute a single line of instructions. This is a big limitation. For example, if there was a timer on a website and another  JS event were to happen, such as an animation, the timer would halt while the browser handled the other instructions. This limitation is detrimental for the future of Javascript development. The solution? The event loop.</p>
<h3 id="event-loop">Event Loop</h3>
<p>The event loop is not just for Javascript. It is the standard that defines how a web browser front end works.</p>
<p><img src="https://media.giphy.com/media/RhBsyPkh1BC87DSIJe/giphy.gif" alt="event loop 1"></p>
<p>The purple ball represents the current state of the event loop. Like a movie, the browser redraws the screen on every frame. Between frames Javascript runs. When the script event ends, control is passed back to the browser to redraw any changes that occurred. This keeps animations or UX elements fluid while still computing the Javacript in the background.</p>
<h3 id="clogging-the-drain">Clogging the Drain</h3>
<p>In a new tab, try pasting the following code into your <a href="https://kb.mailster.co/how-can-i-open-the-browsers-console/">browser’s console</a>. You will have to end the tab’s process afterwards.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">while</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token punctuation">{</span> console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Section is cool"</span><span class="token punctuation">)</span> <span class="token punctuation">}</span>
</code></pre>
<p>Try selecting text or clicking on buttons. Can’t? Let’s see why.</p>
<p>Here is the event loop while our code is running.</p>
<p><img src="https://i.imgur.com/C1P1MR7.jpg" alt="blocked event loop"></p>
<p>It is stuck in our Javascript infinite loop. The website can’t re-render or handle other events, like clicks, because we haven’t given control back to the browser.</p>
<p>This is called blocking the event loop. Long tasks like loops can unintentionally block the event loop, and make web apps and sites feel slow and unresponsive.</p>
<h3 id="unclogging-the-drain">Unclogging the Drain</h3>
<p>Fortunately, we have tools to get around this. Copy this code into a browser console.</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">let</span> loop <span class="token operator">=</span> <span class="token function">setInterval</span><span class="token punctuation">(</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">=&gt;</span> <span class="token punctuation">{</span> 
	console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"Section is cool"</span><span class="token punctuation">)</span> 
<span class="token punctuation">}</span><span class="token punctuation">,</span> <span class="token number">0</span><span class="token punctuation">)</span>
</code></pre>
<p>In this case, we use <code>setInterval(foo, 0)</code>. This adds a callback to the callback queue. Functions like <code>setInterval</code>, <code>setTimeout</code>,  and <em>promises</em> put code on the queue. When all tasks end, Javascript will start running code put on the queue in a first in first out structure. We can use these asynchronous tools to write non-blocking code.</p>
<h3 id="welcome-to-hell">Welcome to Hell</h3>
<p>Asynchronous Javascript is not perfect. Some Javascript developers describe <em>callback hell</em> when code contains many functions, usually nested, that add callbacks to the callback queue. This means some functions are not resolved top to bottom, and instead are called at a later time. Take the following code for example:</p>
<pre class=" language-javascript"><code class="prism  language-javascript"><span class="token keyword">function</span> <span class="token function">callbackFunction</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">{</span>
	console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"called foo"</span><span class="token punctuation">)</span>
<span class="token punctuation">}</span>

<span class="token function">foo</span><span class="token punctuation">(</span>callbackFunction<span class="token punctuation">)</span> <span class="token comment">//calls callbackFunction as async</span>

console<span class="token punctuation">.</span><span class="token function">log</span><span class="token punctuation">(</span><span class="token string">"finished script"</span><span class="token punctuation">)</span>
</code></pre>
<p>While seemingly innocuous, this code has some interesting aspects to it. Due to hoisting (a subject for another article), the function callbackFunction is defined. Then we invoke foo. This places callbackFunction on the callback stack. “Finished script” is now logged. Now that the stack is clear, callbackFunction can be invoked and print “called foo” to the log. We end up with a console log that looks like</p>
<pre><code>finished script
called foo
</code></pre>
<p>This non-linear execution is difficult to understand, and leads to callback hell and undefined behavior.</p>
<h3 id="importance">Importance</h3>
<p>As browsers and Javascript engines become faster and more powerful, client-side Javascript is more efficient. Non-blocking asynchronous code is crucial for responsive web apps that use web/server requests or complex processes during run-time.</p>
<p>Understanding the event loop is the first step to creating great web-based software.</p>

