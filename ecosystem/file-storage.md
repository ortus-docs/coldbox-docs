---
description: Unified file storage in ColdBox with cbfs — local disk, Amazon S3, and Cloudflare R2 behind one API.
icon: folder-open
---

# File Storage

**cbfs** gives your application a single filesystem API regardless of where files actually live: local disk, Amazon S3, or Cloudflare R2.

```bash
box install cbfs
```

📖 **Full documentation:** [cbfs.ortusbooks.com](https://cbfs.ortusbooks.com)

## Quick Example

```javascript
property name="disk" inject="DiskProvider@cbfs";

// Same API, any backend
disk.put( "avatars/#user.getId()#.png", fileContent )
url     = disk.url( "avatars/#user.getId()#.png" )
exists  = disk.exists( "avatars/#user.getId()#.png" )
disk.delete( "avatars/#user.getId()#.png" )
```

Configure disks in your `ColdBox` class and switch backends per environment:

```javascript
// config/ColdBox.bx
cbfs : {
    disks : {
        "local" : { provider : "LocalProvider", properties : { rootPath : expandPath( "/storage" ) } },
        "r2"    : { provider : "R2Provider@cbfs-r2", properties : { /* credentials */ } }
    }
}
```

## Providers

| Provider | Module | Backend |
| --- | --- | --- |
| Local | built-in | Server filesystem |
| S3 | `s3sdk` | Amazon S3 |
| R2 | `cbfs-r2` / `r2sdk` | Cloudflare R2 |

## When to Use It

- User uploads that must survive deploys and scale-outs
- Switching from local disk in development to object storage in production with zero code changes
- Anything you'd otherwise hand-roll with `fileRead`/`fileWrite` and regret later

## See Also

- [Sending Files](../the-basics/event-handlers/sending-files.md) — streaming downloads to the browser
- [Deployment](../reference/deployment/README.md) — where to put writable storage per engine
