# Bookmarklets for developers
A bookmarklet is a bookmark that instead of storing a URL to a page stores JavaScript code that can be executed on a page.
To save a bookmarklet, either drag it to the bookmarks bar or create a new bookmark with the `javascript:...` code as the URL.

You can try a bookmarklet directly by clicking it on this page. Or by copy&pasting it into the address bar, but you will have to readd the `javascript:` part in the beginning as it is stripped for security reasons.


---

## Text transform bookmarklets
These bookmarklets transform a text taken as input and do not load any external resources.

### AtoB: base64 to text
    javascript: void(() => {
        try {
            prompt('Decoded text:', atob(prompt('Decode base64:') ?? (function() {
                throw null;
            }())))
        } catch (e) {
            e && alert(e);
        }
    })()

### BtoA: text to base64
    javascript: void(() => {
        try {
            prompt('Encoded text:', btoa(prompt('Encode text as base64:') ?? (function() {
                throw null;
            }())))
        } catch (e) {
            e && alert(e);
        }
    })()

### Remove all newlines
    javascript: void(() => {
        try {
            prompt('Text without newlines:', (prompt('Remove newlines (\\n and \\r):') ?? function() {
                throw null
            }()).replaceAll('\n', '').replaceAll('\r', ''))
        } catch (e) {
            e && alert(e)
        }
    })();

### Decode URI Component
    javascript: void(() => {
        try {
            prompt('Decoded text:', decodeURIComponent(prompt('Decode URI Component:') ?? (function(){throw null;}())))
        } catch (e) {
            e && alert(e);
        }
    })()

### Encode URI Component
    javascript: void(() => {
        try {
            prompt('Decoded text:', encodeURIComponent(prompt('Encode URI Component:') ?? (function(){throw null;}())))
        } catch (e) {
            e && alert(e);
        }
    })()

### Generate random UUID
    javascript: void(() => {prompt('UUID:', self.crypto.randomUUID())})()

### Generate random number
    javascript: void(() => {
        const buffer = new Uint32Array(1);
        self.crypto.getRandomValues(buffer);
        const min = Math.ceil(parseInt(prompt('Min value:')));
        const max = Math.floor(parseInt(prompt('Max value:')));
        prompt('Random value between ' + min + ' and ' + max + ' (inclusive):', Math.floor(buffer[0] / (0xffffffff + 1) * (max - min + 1)) + min);
    })()

### Length of string
    javascript: void(() => {alert(prompt('Input:').length)})()

### Replace all occurrences of a string
    javascript: void(() => {
        prompt('Replaced output:', prompt('Input:').replaceAll(prompt('String to replace:'), prompt('Replace with:')))
    })()


---

## Local bookmarklets
These bookmarklets modify the content of the page and do not load any external resources.

### Update page title
    javascript:void(document.title=prompt('Enter page title') ?? document.title)

### Reload page in x seconds
    javascript:setTimeout(()=>location.reload(), prompt('Reload the page in seconds:')*1000)

### Rotate images
    javascript:var orientation = prompt('Rotate by degrees (90,180,270...):');
    [...document.querySelectorAll('img')].forEach(el => el.style.transform = 'rotate(' + (parseInt(orientation)) + 'deg)')
    
### contentEditable
    javascript:void(document.body.contentEditable=true)

### Dark color scheme
    javascript:void(document.documentElement.style.colorScheme='dark')

### Evaluate JavaScript code
    javascript: void(() => {
        try {
            alert(eval(prompt('JavaScript code to evaluate:') ?? (function(){throw null;}())))
        } catch (e) {
            e && alert(e);
        }
    })()

### Get document's current HTML source
    javascript: void(() => {prompt('Document outer HTML:',document.documentElement.outerHTML)})()

### Open native eye dropper
    javascript: void(async () => {
        try {
            prompt('Selected HEX color:', (await new EyeDropper().open()).sRGBHex);
        } catch (e) {
            alert(e);
        }
    })()

### Get user agent
    javascript: void(() => {prompt('User agent:', navigator.userAgent)})()

### Search selection using a search engine
    javascript: void(() => {
        open('https://google.com/search?q=' + encodeURIComponent(getSelection().toString().length === 0 ? prompt('Query to search:') ?? (function() {throw null;}()) : getSelection().toString()));
    })()

### Search selection using a search enging only on the current domain
    javascript: void(() => {
        open('https://google.com/search?q=site:' + location.hostname + ' ' + encodeURIComponent(getSelection().toString().length === 0 ? prompt('Query to search on ' + location.hostname + ':') ?? (function() {throw null;}()) : getSelection().toString()));
    })();

### Change password inputs to plaintext
    javascript: (() => {document.querySelectorAll('input[type=password]').forEach(el => el.type = 'text')})();

### Open light/dark themed textpad
    data:text/html,<title>Textpad</title><style>@media (prefers-color-scheme: dark){html {color-scheme: dark;}}</style><body contenteditable spellcheck="false">


---

## External page bookmarklets
These bookmarklets open an external site with some information from the current page.

### Create a QR code of the current page
    javascript: void(
        open('https://chart.apis.google.com/chart?cht=qr&chs=300x300&chld=L|2&chl=' + encodeURIComponent(location.href), null, 'location=no,status=yes,menubar=no,scrollbars=no,resizable=yes,width=500,height=500,modal=yes,dependent=yes')
    );
    
### Create a QR code from text
    javascript: void(() => {
        open('https://chart.apis.google.com/chart?cht=qr&chs=300x300&chld=L|2&chl=' + (prompt('Enter text for QR code:') ?? (function() {
            throw null;
        }())), null, 'location=no,status=yes,menubar=no,scrollbars=no,resizable=yes,width=500,height=500,modal=yes,dependent=yes')
    })();

### Open Alexa Site Info
    javascript:void(open('https://www.alexa.com/siteinfo/' + location.hostname));

### Open this page in Web Archive
    javascript:void(open('https://web.archive.org/web/*/' + encodeURIComponent(location.href)));

### Open this page in archive.is
    javascript:void(open('https://archive.is/search/?q=' + encodeURIComponent(location.href)));

### Open this page in Google Web Cache
    javascript:void(open('https://google.com/search?q=cache:' + encodeURIComponent(location.href)));

### Google Page Speed Test 
    javascript:void(open('https://developers.google.com/speed/pagespeed/insights/?url=' + encodeURIComponent(location.href)));

### Is it down?
    javascript:void(open('https://downforeveryoneorjustme.com/' + location.hostname));


---

## Remote script bookmarklets
⚠ These bookmarklets modify the content of the page using an external script. Make sure you trust and understand the source code before running it on a sensitive website.

All the bookmarklets below load remote scripts with a subresource integrity check. This means if the content of the external script changes, the script won't load.

The following bookmarklets won't work on pages with a Content Security Policy that blocks external scripts.

### Mobile DevTools
    javascript: void(() => {
        const script = document.createElement('script');
        script.crossOrigin = 'anonymous';
        script.integrity = 'sha256-Zz2LCa5JP/pTvQZgI64e/nH4Wp84M2rLGUb2AtrGtPY=';
        script.onload = () => eruda.init();
        script.src = 'https://cdn.jsdelivr.net/npm/eruda@2.4.1/eruda.js';
        document.head.appendChild(script);
    })();

