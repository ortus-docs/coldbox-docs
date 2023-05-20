---
description: The best way to get started with ColdBox
---

# Application Templates

The best way to get started with ColdBox is with our application templates that you can find here: [coldbox-templates](https://github.com/coldbox-templates).  We have a curated collection of starter templates to get you kicking the tires quickly.  You will do so via the `coldbox create app` command in the CLI:

{% embed url="https://github.com/coldbox-templates" %}
github.com/coldbox-templates
{% endembed %}

```bash
coldbox create app help
```

Here is a listing of the latest supported templates:

| Template              | Slug          | Description                                                                              |
| --------------------- | ------------- | ---------------------------------------------------------------------------------------- |
| Default               | `default`     | The default ColdBox application template                                                 |
| Elixir                | `elixir`      | The `default` template with ColdBox elixir support for asset pipelines                   |
| Modern (experimental) | `modern`      | A fresh new approach to ColdBox applications that are non-root based. Still experimental |
| Rest                  | `rest`        | A base REST API using ColdBox                                                            |
| Rest HMVC             | `rest-hmvc`   | An HMVC REST API using modules                                                           |
| Super Simple          | `supersimple` | Barebones conventions baby!                                                              |



## CommandBox Integration

The `coldbox create app` command has integration to our application templates via the `skeleton` argument. This can be the name of each of the templates in our repositories or you can use the following alternatives:

* A name of a ForgeBox entry: `cbtemplate-advanced-script,cbtemplate-simple`
* A Github shortcut: `github-username/repo`
* An HTTP/S URL to a zip file containing a template: [http://myapptemplates.com/template.zip](http://myapptemplates.com/template.zip)
* A folder containing a template: `/opt/shared/templates/my-template`
* A zip file containing the template: `/opt/shared/templates/my-template.zip`
