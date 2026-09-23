---
description: Deploy ColdBox on BoxLang — MiniServer, Docker, DigitalOcean, servlet containers, desktop, and pre-compiled builds.
icon: rocket
---

# Deploying on BoxLang

BoxLang is the recommended ColdBox runtime, and it offers the widest range of deployment targets. Every option below is documented in depth in the [BoxLang book](https://boxlang.ortusbooks.com/getting-started/running-boxlang).

## The Preferred Stack

```bash
# Install the pre-compiled ColdBox edition into the BoxLang engine
box install bx-coldbox

# Start your app on the BoxLang MiniServer
box server start serverConfigFile=server-boxlang.json
```

Using `bx-coldbox` gives you native compilation, faster startups, and BoxLang Prime across CacheBox, LogBox, and WireBox.

## Runtime Options

### MiniServer

[BoxLang MiniServer](https://boxlang.ortusbooks.com/getting-started/running-boxlang/miniserver) is the lightweight, production-grade web server built for BoxLang — the default for new apps and the server [CbGenesis](https://cbgenesis.coldbox.org) ships with. Run it directly or through CommandBox.

### Docker

Official images live at [ortussolutions/boxlang](https://hub.docker.com/r/ortussolutions/boxlang). A minimal Dockerfile:

```dockerfile
FROM ortussolutions/boxlang:miniserver
COPY . /app
WORKDIR /app
RUN box install
EXPOSE 8080
CMD ["box", "server", "start", "--no-open-browser"]
```

Full guidance: [BoxLang Docker docs](https://boxlang.ortusbooks.com/getting-started/running-boxlang/docker).

### DigitalOcean App Platform

BoxLang apps deploy straight to [DigitalOcean App Platform](https://boxlang.ortusbooks.com/getting-started/running-boxlang/digitalocean-app) — point it at your repo, set the run command, done.

### Servlet Containers (WAR)

Need Tomcat/Jetty or an existing Java EE estate? Package your app as a WAR with the [boxlang-servlet](https://github.com/ortus-boxlang/boxlang-servlet) runtime and deploy it like any Java web app.

### Serverless

BoxLang runs on [AWS Lambda](https://boxlang.ortusbooks.com/getting-started/running-boxlang/aws-lambda) and [Google Cloud Functions](https://boxlang.ortusbooks.com/getting-started/running-boxlang/google-cloud-functions) — ColdBox REST APIs work well as function targets.

### Desktop Applications

BoxLang apps can ship as cross-platform desktop applications — see [Desktop Applications](../../digging-deeper/desktop-applications.md).

## Production Notes

- Pre-compile with `Build.bx` (included in the [BoxLang template](https://github.com/coldbox-templates/boxlang)) for faster cold starts
- Set `enableNullSupport` in `Application.bx` for full null handling
- Keep `boxlang.json` and `server.json` in source control so every environment boots identically

## See Also

- [Deployment Overview](README.md) — engine-agnostic best practices
- [Adobe & Lucee](other-engines.md)
