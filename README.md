To unblock a large number of requests,

Cors (Cloudflare Workers) 
Support cross-domain request    
Convert HTTP to HTTPS

### SETUP CloudFlare ###
1, Load 10 minute email & leave the page loaded (so we can verify the email from cloudflare)
https://10minutemail.com/

2, Sign up for free at Cloudflare using your 10 minute email address
https://dash.cloudflare.com
Verify the signup email! now you close 10 minute email

3, On cloudflare look on the left menus for 'Compute Workers' Cick it
4, Start with Hello World!: Click 'Get Started'
5, Click Deploy

6, Click 'Edit Code' (Its at the top rightish)

Look for (on the right side panel)
Welcome to Cloudflare Workers! This is your first worker
DELETE ALL the text you see so its blank

Copy & Paste All The Code below between ##### (There is alot of code make sure you copy it all) DO NOT INCLUDE THE HASH'S ##########
Paste it into the empty box (Use control+v to paste)

After you have pasted the below code click the DEPLOY BUTTON

Once it is deployed copy your url, you will see it under the deploy button, load the url into a browser


####################################

export default {
    async fetch(request, _env) {
        return await handleRequest(request);
    }
}

/**
 * Respond to the request
 * @param {Request} request
 */
async function handleRequest(request) {
    //请求头部、返回对象
    let reqHeaders = new Headers(request.headers),
        outBody, outStatus = 200, outStatusText = 'OK', outCt = null, outHeaders = new Headers({
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Methods": "GET, POST, PUT, PATCH, DELETE, OPTIONS",
            "Access-Control-Allow-Headers": reqHeaders.get('Access-Control-Allow-Headers') || "Accept, Authorization, Cache-Control, Content-Type, DNT, If-Modified-Since, Keep-Alive, Origin, User-Agent, X-Requested-With, Token, x-access-token"
        });

    try {
        //取域名第一个斜杠后的所有信息为代理链接
        let url = request.url.substr(8);
        url = decodeURIComponent(url.substr(url.indexOf('/') + 1));

        //需要忽略的代理
        if (request.method == "OPTIONS" || url.length < 3 || url.indexOf('.') == -1 || url == "favicon.ico" || url == "robots.txt") {
            //输出提示
            const invalid = !(request.method == "OPTIONS" || url.length === 0)
            outBody = JSON.stringify({
                code: invalid ? 400 : 0,
                usage: 'Host/{URL}',
                source: 'https://forum.tfms.xyz',
                note: 'Blocking a large number of requests, please deploy it yourself'
            });
            outCt = "application/json";
            outStatus = invalid ? 400 : 200;
        }
        else {
            url = fixUrl(url);

            //构建 fetch 参数
            let fp = {
                method: request.method,
                headers: {}
            }

            //保留头部其它信息
            const dropHeaders = ['content-length', 'content-type', 'host'];
            let he = reqHeaders.entries();
            for (let h of he) {
                const key = h[0], value = h[1];
                if (!dropHeaders.includes(key)) {
                    fp.headers[key] = value;
                }
            }

            // 是否带 body
            if (["POST", "PUT", "PATCH", "DELETE"].indexOf(request.method) >= 0) {
                const ct = (reqHeaders.get('content-type') || "").toLowerCase();
                if (ct.includes('application/json')) {
                    fp.body = JSON.stringify(await request.json());
                } else if (ct.includes('application/text') || ct.includes('text/html')) {
                    fp.body = await request.text();
                } else if (ct.includes('form')) {
                    fp.body = await request.formData();
                } else {
                    fp.body = await request.blob();
                }
            }

            // 发起 fetch
            let fr = (await fetch(url, fp));
            outCt = fr.headers.get('content-type');
            outStatus = fr.status;
            outStatusText = fr.statusText;
            outBody = fr.body;
        }
    } catch (err) {
        outCt = "application/json";
        outBody = JSON.stringify({
            code: -1,
            msg: JSON.stringify(err.stack) || err
        });
        outStatus = 500;
    }

    //设置类型
    if (outCt && outCt != "") {
        outHeaders.set("content-type", outCt);
    }

    let response = new Response(outBody, {
        status: outStatus,
        statusText: outStatusText,
        headers: outHeaders
    })

    return response;

    // return new Response('OK', { status: 200 })
}

// 补齐 url
function fixUrl(url) {
    if (url.includes("://")) {
        return url;
    } else if (url.includes(':/')) {
        return url.replace(':/', '://');
    } else {
        return "http://" + url;
    }
}

####################################
