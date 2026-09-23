---
description: Queue background jobs in ColdBox with cbq — dispatch, workers, and providers.
icon: layer-group
---

# Queues

**cbq** brings Laravel-style job queues to ColdBox: dispatch work now, execute it later on a worker, with pluggable providers.

```bash
box install cbq
```

📖 **Full documentation:** [cbq.ortusbooks.com](https://cbq.ortusbooks.com)

## Quick Example

{% tabs %}
{% tab title="BoxLang" %}
```javascript
// models/jobs/WelcomeEmailJob.bx
class extends="cbq.models.Jobs.AbstractJob" {

    property name="userId"

    function handle(){
        var user = getInstance( "UserService" ).get( variables.userId )
        getInstance( "MailService@cbmailservices" ).send( buildWelcomeMail( user ) )
    }
}
```
{% endtab %}
{% tab title="CFML" %}
```javascript
// models/jobs/WelcomeEmailJob.cfc
component extends="cbq.models.Jobs.AbstractJob" {

    property name="userId"

    function handle(){
        var user = getInstance( "UserService" ).get( variables.userId )
        getInstance( "MailService@cbmailservices" ).send( buildWelcomeMail( user ) )
    }
}
```
{% endtab %}
{% endtabs %}

```javascript
// Dispatch from anywhere
property name="queue" inject="QueueProvider@cbq";

queue.job( "WelcomeEmailJob" )
    .setUserId( user.getId() )
    .dispatch()
```

## Workers

Run a worker process to consume the queue:

```bash
box cbq work
```

Workers are long-running processes — supervise them in production (systemd, Docker restart policies, or your platform's process manager) and restart them on deploys.

## When to Use It

- Anything slow that shouldn't block a request: mail, PDFs, report generation, third-party API calls
- Retryable work — failed jobs can be released and retried with backoff
- Smoothing traffic spikes by deferring heavy work

## Alternatives

- [Scheduled Tasks](../digging-deeper/scheduled-tasks.md) — time-based recurring work, not request-driven jobs
- [Async Programming](../digging-deeper/promises-async-programming/README.md) — in-process parallelism when you don't need persistence or retries

## See Also

- [Mail](mail.md) — the classic first job to queue
- [Deployment](../reference/deployment/README.md) — supervising workers in production
