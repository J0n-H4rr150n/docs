# crt.sh

<p><a target="_blank" rel="noreferrer" href="https://crt.sh/">https://crt.sh/</a></p>
<h3>crt.sh lookup</h3>
<p>While <code>crt.sh</code> offers a convenient web interface, you can also leverage its API for automated searches directly from your terminal. Let's see how to find all 'dev' subdomains on <code>facebook.com</code> using <code>curl</code> and <code>jq</code>:</p>
<pre><code class="language-shell-session">[!bash!]$ curl -s &quot;https://crt.sh/?q=facebook.com&amp;output=json&quot; | jq -r '.[]
    | select(.name_value | contains(&quot;dev&quot;)) | .name_value' | sort -u
    
*.dev.facebook.com
*.newdev.facebook.com
*.secure.dev.facebook.com
dev.facebook.com
devvm1958.ftw3.facebook.com
facebook-amex-dev.facebook.com
facebook-amex-sign-enc-dev.facebook.com
newdev.facebook.com
secure.dev.facebook.com
</code></pre>
<ul>
<li>
<code>curl -s &quot;https://crt.sh/?q=facebook.com&amp;output=json&quot;</code>: This command fetches the JSON output from crt.sh for certificates matching the domain <code>facebook.com</code>.</li>
<li>
<code>jq -r '.[] | select(.name_value | contains(&quot;dev&quot;)) | .name_value'</code>: This part filters the JSON results, selecting only entries where the <code>name_value</code> field (which contains the domain or subdomain) includes the string &quot;<code>dev</code>&quot;. The <code>-r</code> flag tells <code>jq</code> to output raw strings.</li>
<li>
<code>sort -u</code>: This sorts the results alphabetically and removes duplicates.</li>
</ul>