# Plex-Blackmagic

A Cloudflare Worker for Plex that uses blackmagic to change default client bitrates!

See this long Plex forum thread for more information: https://forums.plex.tv/t/default-all-clients-to-max-internet-streaming/440641

This is a fork of Plex-Blackmagic by [@zmike808](https://github.com/zmike808/Plex-Blackmagic), via changes from [@buroa](https://github.com/buroa/Plex-Blackmagic). I have updated the script to a modern Cloudflare Worker format and added the Cloudflare Button deploy option.

The default streaming quality for clients is set to 12Mbps (`BITRATE` variable set at `12000`). You can change this during the deploy.

## Pre-requisites

You're using a Cloudflare Tunnel to route traffic to your Plex server.

This is the best guide I've found: https://mythofechelon.co.uk/blog/2024/1/7/how-to-set-up-free-secure-high-quality-remote-access-for-plex

And here's one for Unraid: https://dicloak.com/video-insights-detail/simple-cloudflare-tunnel-setup-on-unraid-for-beginners

## Quick deploy to Cloudflare

In the instructions below, replace `plex.example.com` and `example.com` with your own subdomain and domain.

### 1. Deploy using the Cloudflare button:

[![Deploy to Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/mattradford/Plex-Blackmagic)

You will set your `URL` (e.g. `plex.example.com`) and `BITRATE` as part of the Worker setup.

### 2. Add routes:

* `Settings` > `Domains & Routes`
* Add a new route
* Choose zone (same as your domain)
* Choose `"Failure mode: Fail closed (block)"` for each route
* Add these routes:
  * `plex.example.com/video/:/transcode/universal/decision*`
  * `plex.example.com/video/:/transcode/universal/start*`
  * `plex.example.com/video/:/transcode/universal/subtitles*`

## Alternative setup

Fork this repository and add your variables and routes to `wrangler.jsonc`.

Then create a new Worker by importing your forked repository.

```
"vars": {
	"URL": "plex.example.com",
	"BITRATE": "12000"
},
"routes": [
		{
			"pattern": "plex.example.com/video/:/transcode/universal/decision*",
			"zone_name": "example.com"
		},
		{
			"pattern": "plex.example.com/video/:/transcode/universal/start*",
			"zone_name": "example.com"
		},
		{
			"pattern": "plex.example.com/video/:/transcode/universal/subtitles*",
			"zone_name": "example.com"
		},
	]
```
## Post-deploy
1) Start streaming something.
2) Go into your Worker and check the Logs for live activity. You should set `GET` requests against the routes you've set up.
