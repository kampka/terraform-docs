---
title: "Configuration"
description: "terraform-docs configuration file, i.e. .terraform-docs.yml"
menu:
  docs:
    parent: "user-guide"
weight: 120
toc: true
---

The `terraform-docs` configuration is a yaml file. This is a convenient way to
share the configuation amongst teammates, CI, or other toolings. To do so you
can use `-c` or `--config` flag which accepts name of the config file.

Default name of this file is `.terraform-docs.yml`, and it will get picked it
up (if existed) without needing to explicitly passing with config flag.

```console
$ tree
.
├── main.tf
├── ...
├── ...
└── .terraform-docs.yml
$ terraform-docs .
```

Or you can use a config file with any arbitrary name:

```console
$ tree
.
├── main.tf
├── ...
├── ...
└── .tfdocs-config.yml
$ terraform-docs -c .tfdocs-config.yml .
```

## Options

Below is a complete list of options that you can use with `terraform-docs`, with their
corresponding default values (if applicable).

```yaml
formatter: <FORMATTER_NAME>
header-from: main.tf

sections:
  hide-all: false
  hide: []
  show-all: true
  show: []

output:
  file: ""
  mode: inject
  template: |-
    <!-- BEGIN_TF_DOCS -->
    {{ .Content }}
    <!-- END_TF_DOCS -->

output-values:
  enabled: false
  from: ""

sort:
  enabled: true
  by:
    - required
    - type

settings:
  anchor: true
  color: true
  default: true
  escape: true
  indent: 2
  required: true
  sensitive: true
  type: true
```

**Note:** The following options cannot be used together:

- `sections.hide` and `sections.show`
- `sections.hide-all` and `sections.show-all`
- `sections.hide-all` and `sections.hide`
- `sections.show-all` and `sections.show`
- `sort.by.required` and `sort.by.type`

## Formatters

The following options are supported out of the box by terraform-docs and can be
used for `FORMATTER_NAME`:

- `asciidoc` - [reference]({{< ref "asciidoc" >}})
- `asciidoc document` - [reference]({{< ref "asciidoc-document" >}})
- `asciidoc table` - [reference]({{< ref "asciidoc-table" >}})
- `json` - [reference]({{< ref "json" >}})
- `markdown` - [reference]({{< ref "markdown" >}})
- `markdown document` - [reference]({{< ref "markdown-document" >}})
- `markdown table` - [reference]({{< ref "markdown-table" >}})
- `pretty` - [reference]({{< ref "pretty" >}})
- `tfvars hcl` - [reference]({{< ref "tfvars-hcl" >}})
- `tfvars json` - [reference]({{< ref "tfvars-json" >}})
- `toml` - [reference]({{< ref "toml" >}})
- `xml` - [reference]({{< ref "xml" >}})
- `yaml` - [reference]({{< ref "yaml" >}})

**Note:** You need to pass name of a plugin as `formatter` in order to be able to
use the plugin. For example, if plugin binary file is called `tfdocs-format-foo`,
formatter name must be set to `foo`.

## header-from

Relative path to a file to extract header for the generated output from. Supported
file formats are `.adoc`, `.md`, `.tf`, and `.txt`. Default value is `main.tf`.

## Sections

The following options are supported and can be used for `sections.show` and
`sections.hide`:

- `header`
- `inputs`
- `modules`
- `outputs`
- `providers`
- `requirements`
- `resources`

## Output

Insert generated output to file if `output.file` (or `--output-file string` CLI
flag) is not empty. Insersion behavior can be controlled by `output.mode` (or
`--output-mode string` CLI flag):

- `inject` (default)

  Partially replace the `output-file` with generated output. This will fail if
  `output-file` doesn't exist.

- `replace`

  Completely replace the `output-file` with generated output. This will create
  the `output-file` if it doesn't exist.

The output generated by formatters (`markdown`, `asciidoc`, etc) will first be
inserted into a template before getting saved into the file. This template can
be customized with `output.template` or `--output-template string` CLI flag.
The default template value is:

```go
<!-- BEGIN_TF_DOCS -->
{{ .Content }}
<!-- END_TF_DOCS -->
```

This template consists of three items, all of them are mandatory and have to be on
separate lines:

- begin comment
- `{{ .Content }}` slug
- end comment

You may change the wording of comment as you wish, but the comment must be present
in the template. Also note that `SPACE`s inside `{{ }}` are mandatory.