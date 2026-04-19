---
title:  "Install WhatsApp on Mac with Nativefier"
date:   2026-04-19
badges:
 - type: info
   tag: mac
 - type: info
   tag: nativefier
tags:
  - mac
  - nativefier
  - whatsapp
---

WhatsApp has an official Mac app, but it can be heavy on resources and sometimes lacks flexibility. An alternative is to wrap WhatsApp Web into a standalone desktop app using **Nativefier**.

<!--more-->

## What is Nativefier?

[Nativefier](https://github.com/nativefier/nativefier) is a command-line tool that creates a desktop application from any web page using Electron. It gives you a lightweight, standalone window without the overhead of a full browser.

## Prerequisites

You need **Node.js** and **npm** installed. If you don't have them, install via Homebrew:

```bash
brew install node
```

## Install Nativefier

```bash
npm install -g nativefier
```

## Create the WhatsApp App

Run the following command:

```bash
nativefier --name "WhatsApp" "https://web.whatsapp.com" --single-instance
```

This will generate a folder in your current directory containing the `.app` bundle.

### Useful options

* `--icon icon.png` - Use a custom icon (download a WhatsApp icon in `.png` format first)
* `--single-instance` - Prevents opening multiple instances
* `--internal-urls ".*whatsapp.*"` - Keeps all WhatsApp-related navigation inside the app
* `--darwin-dark-mode-support` - Enables dark mode support on macOS

A more complete example:

```bash
nativefier \
  --name "WhatsApp" \
  --single-instance \
  --internal-urls ".*whatsapp.*" \
  --darwin-dark-mode-support \
  "https://web.whatsapp.com"
```

## Move the App to Applications

```bash
mv WhatsApp-darwin-*/WhatsApp.app /Applications/
```

You can now launch **WhatsApp** from Spotlight or your Applications folder. On first launch, scan the QR code with your phone to link the session, just like you would in the browser.

## Notes

* **Nativefier is archived** - The project is no longer actively maintained but still works. An actively maintained alternative is [Pake](https://github.com/nicedayfor/pake).
* The app stores its session data locally, so you won't need to re-scan the QR code every time.
* To update the app, simply re-run the `nativefier` command and replace the old `.app` bundle.
