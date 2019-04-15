# Senior Fraud/Risk Engineer Security Challenges

## Challenge 1

Bandcamp's Senior Fraud/Risk Engineer job page (<https://bandcamp.com/jobs>) contains a security challenge, as per this hint:

`To apply, gather the crumbs (starting with your cookies).`

Chrome DevTools shows the following cookie in the cookies area:

Name: `fraud_job_url` 
Value: `Over+here%3A+bandcamp.com%2Fectotherm%2Fsnakeoil_requests%3Fsnakeoil_param%3Dfrog`

Although an online URL decoder will work, here is a simple Python example:

```python
import urllib
urllib.parse.unquote('Over+here%3A+bandcamp.com%2Fectotherm%2Fsnakeoil_requests%3Fsnakeoil_param%3Dfrog')
```
Decoded value: `Over+here:+bandcamp.com/ectotherm/snakeoil_requests?snakeoil_param=frog`

Time to navigate to `https://bandcamp.com/ectotherm/snakeoil_requests?snakeoil_param=frog`.

## Challenge 2

The new page contains the following text/hint:

```
Applying is not a quick win,
To do so you must first log in.
Take over the account
Whose details lie about -
найти необычный IP-адрес to begin.
```
Translated portion: `find unusual IP address to begin`

Below that text, a table containing the following columns: `ip_address`, `fan_url`, and `user_agent`.

Considering the instruction (in Russian) to find the unusual IP address, we can paste the IPs into MaxMind's GeoIP2 City Database Demo (https://www.maxmind.com/en/geoip-demo).

A Russian IP is identified as 46.254.222.156. Going back to the table on Bandcamp, the corresponding `fan_url` is `http://bandcamp.com/gilamonster`.

Time to navigate to `http://bandcamp.com/gilamonster`.

## Challenge 3

There was not much assurance Challenge 2 was solved successfully. However, the gilamonster user profile page appears to provide plenty of clues that this is another challenge and not an ordinary user page. In fact, we can see the following code right at the top of the page: `it("dirty");`.

Three images are displayed. Normally, these images would be album art. Instead, we see small text (which makes for unusual album art). Obtaining full-size image URLs is straightforward. For reference, the images are displayed as follows.

**Image 1**
URL: `https://f4.bcbits.com/img/a3232630976_16.jpg`
![Image 1](https://f4.bcbits.com/img/a3232630976_16.jpg)
```javascript
it = function(str) {
	var the_answer = "";
	for (var i = 0; i < str.length; i++) {
		the_answer += window[str[i]](0);
	}
	return the_answer;
};

lookup = [
	"f", "y", "d", "s", "v", "k", "e", "i", "n", "p",
	"a", "t", "l", "r", "j", "u", "z", "o", "g", "w",
	"c", "q", "m", "h", "x", "b"
];
```

**Image 2**
URL: `https://f4.bcbits.com/img/a0819169199_16.jpg`
![Image 2](https://f4.bcbits.com/img/a0819169199_16.jpg)
```javascript
var a = function(int) { return b(++int); }; 
var b = function(int) { return c(++int); }; 
var c = function(int) { return d(++int); };
var d = function(int) { return e(++int); }; 
var e = function(int) { return f(++int); }; 
var f = function(int) { return g(++int); };
var g = function(int) { return h(++int); }; 
var h = function(int) { return i(++int); }; 
var i = function(int) { return j(++int); };
var j = function(int) { return k(++int); }; 
var k = function(int) { return l(++int); }; 
var l = function(int) { return m(++int); }; 
var m = function(int) { return n(++int); };
var n = function(int) { return o(++int); };
var o = function(int) { return p(++int); }; 
var p = function(int) { return q(++int); }; 
var q = function(int) { return r(++int); }; 
var r = function(int) { return s(++int); }; 
var s = function(int) { return t(++int); }; 
var t = function(int) { return u(++int); }; 
var u = function(int) { return v(++int); }; 
var v = function(int) { return w(++int); }; 
var w = function(int) { return x(++int); }; 
var x = function(int) { return y(++int); }; 
var y = function(int) { return z(++int); }; 
var z = function(int) { return lookup[int]; }; 
```

**Image 3**
URL: `https://f4.bcbits.com/img/a2722538093_16.jpg`
![Image 3](https://f4.bcbits.com/img/a2722538093_16.jpg)

```javascript
window.Launder("what_it()_returns");
```

Two lines of text appear below each image. Reading from left to right, a sentence forms: `Think It() Through Then Call Launder()` This is a clue that indicates we need to analyze the information and then call Launder(). If needed, we can later look at the page source to learn about Launder().

The code in the images appears to be JavaScript pseudocode. `it()` builds and returns a string called `the_answer`. However, `it()` itself is not very useful. `lookup[]` appears to be an array to be used for decoding. `z()` in Image 2 accesses `lookup[]`. By studying Image 2's code, we can see that for a given character, an integer is recursively incremented until lookup occurs in `lookup[].` Recall the clue that appears at the top of the page: `it("dirty");`. The code implies a new string is built character by character. Considering this, we can try to mentally run "dirty" through the translation code, starting with "d":

We land on `var d = function(int) { return e(++int); };`. Following the code, we can increment `n` (arbitrary variable) by 1 until `lookup[]` is accessed. Then we can see the resulting character for `lookup[n]`. In the case of "d" where n=22, `lookup[22]`="m".

Therefore, repeating for each character:

```
lookup[22] = 'm'  // 'd'
lookup[17] = 'o'  // 'i'
lookup[8] =  'n'  // 'r'
lookup[6] =  'e'  // 't'
lookup[1] =  'y'  // 'y'
```

See a pattern? The word "dirty" produces "money". Given the clues, guessing may have been faster. For example, many English speakers have heard the expressions "dirty money" and "launder money." 

To complete the challenge, `Launder('money');` in the DevTools console produces an alert containing: `https://bandcamp.com/ectotherm/snakeoil_crumbs`.

## Challenge 4

The new page contains the following text/hint:

`Collect your crumbs...`

```
POST https://bandcamp.com/ectotherm/get_the_snakeoil_password HTTP/1.1
Host: bandcamp.com
User-Agent: SnakeOil/5.0 (USER 9.0; Agent 6.0; String/5.0) ?????/321
Content-Type: application/x-www-form-urlencoded
Content-Length: 45

snakeoil_param=????&snakeoil_user=???????????
```

We can see this is a POST request that needs sending. "Collect your crumbs" is a hint referring to past challenges. 

Missing POST data to solve:
* `?????` in User-Agent = **Gecko** (refer to the user-agent string in the Challenge 2's qualifying row and notice the missing five characters match the length of Gecko)
* `snakeoil_param=????` value = **frog** (refer to Challenge 1)
* `snakeoil_user=???????????` value =  **gilamonster**. Originally, I considered the value may have been **ectotherm** (refer to Challenge 2). Bandcamp user pages are structured as `https://bandcamp.com/username`. However, **gilamonster** matches the 11 missing characters and we have already been introduced to the special user page `http://bandcamp.com/gilamonster`.

Putting the POST request together:

```
POST https://bandcamp.com/ectotherm/get_the_snakeoil_password HTTP/1.1
Host: bandcamp.com
User-Agent: SnakeOil/5.0 (USER 9.0; Agent 6.0; String/5.0) Gecko/321
Content-Type: application/x-www-form-urlencoded
Content-Length: 45

snakeoil_param=frog&snakeoil_user=gilamonster
```

We can now send a POST request programmatically or use a tool such as Fiddler, Postman, etc.

Here is a Python example:

```python
import requests
from requests.exceptions import HTTPError

try:
    response = requests.post(
        'https://bandcamp.com/ectotherm/get_the_snakeoil_password',
        headers={
            'Host': 'bandcamp.com',
            'User-Agent':
                'SnakeOil/5.0 (USER 9.0; Agent 6.0; String/5.0) Gecko/321',
            'Content-Type': 'application/x-www-form-urlencoded',
            'Content-Length': '45'
        },
        data={
            'snakeoil_param': 'frog',
            'snakeoil_user': 'gilamonster'
        },
        timeout=1
    )
except HTTPError as http_err:
    print(f'HTTP error: {http_err}')
except Exception as err:
    print(f'Other error: {err}')
else:
    print(response.text)
```

The response contains a simple clue and a login URL. After logging in, a special contact email is displayed for responding to the Senior Fraud/Risk Engineer role. This information is intentionally omitted here.

*All challenges completed.*
