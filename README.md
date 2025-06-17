<b><h2>To unblock a large number of requests websites & streams</h2></b><br>

Cors (Cloudflare Workers) <br>
Support cross-domain request<br>
Convert HTTP to HTTPS<br>

### SETUP CloudFlare ###<br>
1, Load 10 minute email & leave the page loaded (so we can verify the email from cloudflare)<br>
https://10minutemail.com/<br>

2, Sign up for free at Cloudflare using your 10 minute email address<br>
https://dash.cloudflare.com<br>
Verify the signup email! now you can close 10 minute email<br>

3, On cloudflare look on the left menus for 'Compute Workers' Cick it<br>
4, Start with Hello World!: Click 'Get Started'<br>
5, Click Deploy<br>
6, Click 'Edit Code' (Its at the top rightish)<br>

7, Look for (on the right side panel)<br>
Welcome to Cloudflare Workers! This is your first worker<br>
DELETE ALL the text you see so its blank<br>

8, Enter this _worker.js url into a browser<br>https://raw.githubusercontent.com/MarkMCFC/seep/refs/heads/main/pages/_worker.js<br>
Copy all the content/code you see from inside the worker.js file & paste it into cloudflare blank box (from part 7) (use cntrl+v to paste)<br>

After you have pasted the code click the DEPLOY BUTTON

Once it is deployed copy your url, you will see it under the deploy button, load the url into a browser.

End Points (How to use your proxy)
https://test.ytytuj.workers.dev/http://your.url.goes.here.m3u8 .ts<br>
or<br>
https://test.ytytuj.workers.dev/?url=http://your.url.goes.here.m3u8 .ts

Visit https://forum.tfms.xyz for more
