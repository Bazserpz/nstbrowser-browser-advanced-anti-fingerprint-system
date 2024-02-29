# nstbrowser-browser-advanced-anti-fingerprint-system
Providing a blazing fast framework for web automation, webscraping, bots and any other creative ideas which are normally hindered by annoying anti bot systems like Captcha / CloudFlare / Imperva / hCaptcha

# Nstbrowser Puppeteer Integration

Integrate the power of Nstbrowser with Puppeteer for enhanced web automation capabilities. This guide provides step-by-step instructions on how to use Nstbrowser with Puppeteer, enabling you to leverage custom browser profiles, configurations, and more.

## Getting Started

1. **Launch nstbrowser agent client**: Start the client, which listens on port `8848`.
2. **Obtain remote debugging address**: Fetch the `browserWSEndpoint` for connecting to Puppeteer.

### Connect to a Launched Browser

Use the `connectToLaunchedBrowser` function to connect to a browser that has already been launched with a specific profile.

```javascript
import puppeteer from 'puppeteer-core';

async function connectToLaunchedBrowser(profileId) {
  const host = 'localhost:8848';
  const apiKey = 'your api key';
  const query = new URLSearchParams({
    'x-api-key': apiKey,
  });
  const browserWSEndpoint = `ws://${host}/devtool/browser/${profileId}?${query.toString()}`;
  console.log('browserWSEndpoint: ', browserWSEndpoint);
  await execPuppeteer(browserWSEndpoint);
}

connectToLaunchedBrowser('your profileId').then();
```

### Launch and Connect to Browser

The `launchAndConnectToBrowser` function allows you to launch a browser with a specified profile and connect to it.

```javascript
import puppeteer from 'puppeteer-core';

async function launchAndConnectToBrowser(profileId) {
  const host = 'localhost:8848';
  const apiKey = 'your api key';
  const config = {
    headless: true,
    autoClose: true,
  };
  const query = new URLSearchParams({
    'x-api-key': apiKey,
    config: encodeURIComponent(JSON.stringify((config))),
  });
  const browserWSEndpoint = `ws://${host}/devtool/launch/${profileId}?${query.toString()}`;
  console.log('browserWSEndpoint: ', browserWSEndpoint);
  await execPuppeteer(browserWSEndpoint);
}

launchAndConnectToBrowser('your profileId').then();
```

### Create and Connect to Browser

Create a new browser profile based on custom configurations and connect to it using `createAndConnectToBrowser`.

```javascript
import puppeteer from 'puppeteer-core';

async function createAndConnectToBrowser() {
  const host = 'localhost:8848';
  const apiKey = 'your api key';
  const config = {
    once: true,
    headless: true,
    autoClose: false,
    remoteDebuggingPort: 9223,
    fingerprint: {
      name: 'browser113',
      platform: 'windows',
      kernel: 'chromium',
      kernelMilestone: '113',
      hardwareConcurrency: 4,
      deviceMemory: 8,
      proxy: '',
    },
  };
  const query = new URLSearchParams({
    'x-api-key': apiKey,
    config: encodeURIComponent(JSON.stringify((config))),
  });
  const browserWSEndpoint = `ws://${host}/devtool/launch?${query.toString()}`;
  console.log('browserWSEndpoint: ', browserWSEndpoint);
  await execPuppeteer(browserWSEndpoint);
}

createAndConnectToBrowser().then();
```

### Common Code for Executing Puppeteer

```javascript
async function execPuppeteer(browserWSEndpoint) {
  try {
    const browser = await puppeteer.connect({
      browserWSEndpoint: browserWSEndpoint,
      defaultViewport: null,
    });

    const page = await browser.newPage();
    await page.goto('https://nstbrowser.io/');
    await page.screenshot({ path: 'screenshot.png' });
    await browser.disconnect();
  } catch (err) {
    console.error(err);
  }
}
```

## Config Parameters

- `config.once`: Boolean | Disposable browser that does not create a Profile and automatically closes after execution.
- `config.headless`: Boolean | String | Enable headless mode; "new" for new headless mode.
- `config.autoClose`: Boolean | Automatically close the connection after the browser is closed.
- More parameters include `timedCloseSec`, `remoteDebuggingPort`, `fingerprint` configurations, and proxy settings.

---
