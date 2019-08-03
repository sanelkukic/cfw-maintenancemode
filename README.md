# cfw-maintenancemode

MaintenanceMode is a Cloudflare Workers script that can do several things, but mainly it:

- Lets you setup a serverless maintenance page that automatically triggers when it finds an active maintenance on your StatusPage.io status page
- Lets you setup a serverless downtime page that automatically triggers when it finds an active incident on your StatusPage.io status page
- Lets you manually trigger either the maintenance or downtime pages
- Log requests to a Discord webhook (log visitor IP, request method, full request URL, the ASN, and the Cloudflare datacenter the request hit)

## Installation
> These docs assume that you have a website proxied by Cloudflare (setup with Cloudflare's nameservers and not via CNAME setup) and that you've already gone ahead and enabled [Cloudflare Workers](https://cloudflare.com/workers) on your Cloudflare-proxied website. Workers offers 100,000 free requests per day as well so what are you waiting for? lol :)

Before you do anything, install [Wrangler CLI](https://github.com/cloudflare/wrangler) first.

Create a new directory somewhere, and run the following command:

```
$ wrangler generate maintenancemode https://github.com/xDrixxyz/cfw-maintenancemode
```

Before you can setup this script on your website, please edit `index.js` and provide the following items in the `config` object.

- `config.statuspage` - String which contains the URL to your StatusPage.io status page
- `config.statuspage_id` - String which contains the ID of your StatusPage.io status page (more info below)
- `config.enable_discord_logging` - If set to true, please also specify `config.discord_webhook`. If you don't want to log requests to a Discord webhook, set it to false (boolean not string)
- `config.discord_webhook` - String containing the URL of the Discord webhook to send logs to.

#### Getting your StatusPage's ID

The easiest way to get the ID of your status page is to log into your StatusPage.io account.

To get your StatusPage.io page ID,

- Log into your account at [https://manage.statuspage.io/login](https://manage.statuspage.io/login)
- Select the status page to use with this script from the dropdown next to the StatusPage.io logo
- Click on the User Profile icon at the top-right.
- Click on the "Manage Account" link under the menu which appears
- Click on the "API" tab

Your page's ID will be under the `Page ID` field. Copy/paste it into the `config.statuspage_id` string in `index.js`

#### Force-enabling maintenance/downtime pages

If you want to forcefully enable the maintenance or downtime pages, you can set the `config.force_enable_maintenance` or `config.force_enable_downtime` booleans to `true`. Maintenance has priority over downtime.

You _will_ need to re-deploy the Workers script each time you do this, but that's a small price to pay compared to paying for Workers KV.

#### Customizing your maintenance/downtime pages

You can customize the full HTML of the maintenance/downtime pages.

This HTML is stored in a string variable in the code called `MAINTENANCE_PAGE_HTML` (for the maintenance page) and `DOWNTIME_PAGE_HTML` (for the downtime page).

I know you're about to say `WHY THE FUCK do you store it as a single line string constant?` to which my reply will be `idk portability ig` or `mate i made this script in the matter of a few hours what do you expect lol`.

If you know JavaScript, you can also do string interpolation to include variables like your status page's URL in your HTML, since the HTML _is_ stored as a single-line string constant.

## Deploy with Wrangler

If you haven't setup your Cloudflare account with Wrangler already, run:

```
$ wrangler config
```

Then edit the `wrangler.toml` file and set your Cloudflare account ID in the `account_id` field. You can find your account ID on the Wranglers section of your dashboard.

Set the ID of the zone to publish the Worker to in `zone_id`. You can find the zone ID in the Overview tab of your Cloudflare-registered domain.

Also set the routes you want this script to run on. Ideally you should set the following routes:

- `*.example.com/*`
- `example.com/*`

This way, all subdomains and subpaths of example.com, including example.com itself will have requests pass through the Worker script first.

Or if you want to make things easier on yourself, you can copy the `example-wrangler.toml` and edit the `account_id`, `zone_id`, and `route` to deploy the script to.

Make sure if you downloaded and edited the `example-wrangler.toml` that you delete any pre-existing `wrangler.toml` and rename `example-wrangler.toml` to `wrangler.toml`!

Once you've done _all_ that, run:

```
$ wrangler build
$ wrangler deploy
```

This will deploy the Worker script to the zone with the ID you specified, on the account with the ID you specified, to listen to the route you specified in `wrangler.toml`

## Support

If you have questions, need help setting the Workers script up, or found a bug, you can [create an issue](https://github.com/xDrixxyz/cfw-maintenancemode), email `support@xdrixxyz.eu.org`, or visit the [Support Center](https://support.xdrixxyz.eu.org)

## License

This script is licensed under the terms of the MIT License. You can find the full license text in the [LICENSE.txt](./LICENSE.txt) file in this folder.

Copyright 2019 xDrixxyz. All rights reserved.
