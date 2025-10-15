# To me, performance is a feature

I simply enjoy using fast websites more than slow ones.  
This is a living, non-exhaustive list of the decisions and principles that guide my approach.

## Interactions

- **Design and optimize for two classes of users: anonymous and logged-in.**  
  We minimize the HTML, CSS, and JavaScript footprint for anonymous users so their pages load faster. Only a minimal stub of functionality loads initially, and features like editing “rez in” dynamically when the user focuses on the input area.  
  For logged-in users, the footprint is naturally larger, but we can safely add features for our most engaged community members without harming the experience for the vast majority of anonymous visitors.

- **Serve the absolute minimum necessary to anonymous users.**

- **You can never serve a webpage faster than you can render it on the server.**  
  Simply displaying the server render time in the upper-right corner of every page forced us to fix performance regressions and omissions.

## Minimizing HTTP Requests

- **Combine files**: Reduce the number of HTTP requests by combining scripts into a single file and styles into one stylesheet. This can be challenging when assets vary between pages, but automating it as part of your release process significantly improves response times.

- **Use CSS sprites**: Combine background images into a single image and display specific segments with `background-position`. This reduces image requests.

- **Avoid image maps**: Although they reduce requests, they are tedious to maintain and inaccessible for navigation. Use only when images are contiguous (e.g., navigation bars).

- **Inline images carefully**: Using the `data:` URL scheme embeds images directly in HTML. This increases page size but can reduce requests if inlined into cached stylesheets. Browser support may vary.

- **Optimize for empty caches**: 40–60% of daily visitors come with an empty cache. Making your site fast for first-time visitors has the greatest impact on user experience.

## Content Delivery & Caching

- **Use a Content Delivery Network (CDN)**: The closer the server to the user, the faster the load time. Distribute content geographically to improve perceived speed.

- **Set appropriate cache headers**:
  - *Static components*: Use far-future `Expires` headers (“Never expire” policy). Update filenames (e.g., `yahoo_2.0.6.js`) when components change—ideally automated in the build process.
  - *Dynamic components*: Use appropriate `Cache-Control` headers for conditional requests.

## Compression

- **Gzip components**: Compression reduces response sizes by up to 70%. About 90% of browsers support gzip.  
  - Apache 1.3 uses `mod_gzip`; Apache 2.x uses `mod_deflate`.  
  - Compress HTML, scripts, stylesheets, XML, and JSON.  
  - Don’t gzip images or PDFs—they’re already compressed and doing so wastes CPU and may increase file size.

## Loading Order

- **Put stylesheets at the top**: Placing CSS in the `<head>` allows progressive rendering and makes pages appear to load faster.

- **Put scripts at the bottom**: Scripts block parallel downloads. While a script is downloading, the browser won’t start other requests—even from different hostnames.  
  If a script must use `document.write`, it cannot be moved. In those cases, consider using the `defer` attribute to allow rendering to continue.

## CSS and JavaScript Best Practices

- **Avoid CSS expressions**: They can trigger thousands of evaluations (e.g., from mouse movement), causing major performance issues.

- **Use external files when possible**:  
  External JavaScript and CSS benefit from browser caching, reducing subsequent page load times. Inline assets increase HTML size and are re-downloaded with every request.  
  However, for homepages or single-page sessions, inlining key assets may improve perceived speed. A hybrid approach works well—inline critical CSS/JS for first load, and lazy-load full external files afterward.

## Reduce DNS Lookups

- The Domain Name System (DNS) maps hostnames to IP addresses, just as phonebooks map people's names to their phone numbers. When you type www.yahoo.com into your browser, a DNS resolver contacted by the browser returns that server's IP address. DNS has a cost. *It typically takes 20-120 milliseconds* for DNS to lookup the IP address for a given hostname. The browser can't download anything from this hostname until the DNS lookup is completed. DNS lookups are cached for better performance. This caching can occur on a special caching server, maintained by the user's ISP or local area network, but there is also caching that occurs on the individual user's computer. The DNS information remains in the operating system's DNS cache (the "DNS Client service" on Microsoft Windows). My guideline is to split these components across at least two but no more than four hostnames. This results in a good compromise between reducing DNS lookups and allowing a high degree of parallel downloads.

## Minify JavaScript and CSS

- Two popular tools for minifying JavaScript code are JSMin and YUI Compressor. The YUI compressor can also minify CSS. In addition to minifying external scripts and styles, inlined <script> and <style> blocks can and should also be minified. Even if you gzip your scripts and styles, minifying them will still reduce the size by 5% or more.

## Avoid Redirects

- The main thing to remember is that redirects slow down the user experience. Inserting a redirect between the user and the HTML document delays everything in the page since nothing in the page can be rendered and no components can start being downloaded until the HTML document has arrived.
  
- One of the most wasteful redirects happens frequently and web developers are generally not aware of it. It occurs when a trailing slash (/) is missing from a URL that should otherwise have one.

## Remove Duplicate Scripts

- One way to avoid accidentally including the same script twice is to implement a script management module in your templating system. The typical way to include a script is to use the SCRIPT tag in your HTML page.

## Configure ETags

- Entity tags (ETags) are a mechanism that web servers and browsers use to determine whether the component in the browser's cache matches the one on the origin server.
- If you're not taking advantage of the flexible validation model that ETags provide, it's better to just remove the ETag altogether. The Last-Modified header validates based on the component's timestamp. And removing the ETag reduces the size of the HTTP headers in both the response and subsequent requests.

## Make Ajax Cacheable
The most important way to improve the performance of Ajax is to make the responses cacheable, as seen in Add an Expires or a Cache-Control Header.
