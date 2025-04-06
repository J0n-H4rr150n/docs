# curl

<h2>Basics</h2>

<p>Basic command:</p>
<pre><code>curl https://curl.ctfio.com</code></pre>

<p>POST request:</p>
<pre><code>curl -X POST https://curl.ctfio.com/endpoint_3 -d "show=flag"</code></pre>

<p>Headers:</p>
<pre><code>curl https://curl.ctfio.com/endpoint_5 -H "Show: flag"</code></pre>

<p>Cookies:</p>
<pre><code>curl https://curl.ctfio.com/endpoint_6 -H "Cookie: show=flag"</code></pre>
<pre><code>curl https://curl.ctfio.com/endpoint_6 -b "show=flag"</code></pre>

<h2>URL Encoding</h2>

<p>Confuse a webserver without messing up the payload</p>
<ul>
    <li>Set the value to <code>fl&ag</code> without the browser thinking it is a separator.</li>
</ul>
<pre><code>curl https://curl.ctfio.com/endpoint_7?show=fl%26ag</code></pre>

<h2>Example with upstream proxy</h2>
`curl --request POST "https://www.google.com" --proxy https://127.0.0.1:8085 --insecure`  

<h2>Resources</h2>
<ul>
    <li><a target="_blank" rel="noreferrer" href="https://www.w3schools.com/charsets/ref_html_ascii.asp">HTML ASCII Reference</a></li>
    <li><a target="_blank" rel="noreferrer" href="https://app.hackinghub.io/hubs/nahamsec-bug-bounty-course">Nahamsec - Bug Bounty Course</a></li>
</ul>