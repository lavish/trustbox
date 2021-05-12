TrustBox
========

<p><a href="http://trustbox.hackthe.space/">TrustBox</a>, the world's first trusted site to temporarily store your data. Drag your files and drop them on the TrustBox icon to safely save them in your browser (Chromium-based browser only for now!).</p>

<p>Your mission is to get the flag. How surprising! This time we provide part of the code used by our bot to understand what are the requirements of the page you have to craft (see below). In a nutshell, the bot will enter the flag inside an <code>input</code> element with <code>id="flag"</code> and submit it if there is a <code>script</code> element with <code>id="pwn"</code> and a non-empty <code>src</code> attribute. Notice that the script and the input elements must be inserted in the top frame of the root page of the application.</p>

```Javascript
const executeBot = (targeturl) =>
    runBrowser(async (browser, page) => {
        // Listen for browser events
        browser.on('targetcreated', async (target) => {
            // When a new tab/page is created
            if (target.type() === 'page') {
                const page = await target.page();
                const pageUrl = page.url();
                // If the new tab is in my origin
                const hostname = url.parse(pageUrl, false).host
                const path = url.parse(pageUrl, false).pathname
                if (hostname == APP_HOST && path == '/') {
                    // Insert the flag into <input id="flag"/>
                    // if there is a <script id="pwn" src="..."> in the page
                    await page.waitForSelector('script#pwn')
                    let script_src = await page.$eval('script#pwn', (sc) => sc.src)
                    if (script_src) {
                        await page.waitForSelector('input#flag')
                        await page.focus('input#flag')
                        await page.keyboard.type(FLAG, {delay: 10})
                        await delay(100)
                        // Press enter (submit the form if any)
                        await page.keyboard.press(String.fromCharCode(13))
                        await delay(1000)  // wait 1 second before exiting
                    }
                }
                await page.close()
            }
        })
        // Visit the user provided page
        await page.goto(targeturl) 
        await delay(1000 * WAIT_TIME)  // wait WAIT_TIME seconds before exiting
    })
```
