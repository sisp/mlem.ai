# Working with Remote Objects and Projects

[MLEM objects] (models, data, etc.) can live in different locations such as Git
repositories, cloud object storage, local directories, etc.

<admon type="note">

In this page we will work with model objects in Git repos, but the same
operations apply to any [object type] and location.

</admon>

[mlem objects]: /doc/user-guide/basic-concepts#mlem-objects
[object type]: /doc/user-guide/basic-concepts#mlem-object-types

## Remote MLEM projects

Although you can store MLEM objects in any location such as a Git repo, Cloud
storage, or external drives, creating a MLEM project lets you organize and
[discover](#listing-objects) MLEM objects consistently.

To create a MLEM project in a remote location, you can provide its URL or path
to `mlem init`.

## Listing objects

You can list MLEM objects inside a remote MLEM project (e.g. in a Git repo) with
`mlem list`. There's no need to download/clone the project.

```cli
$ mlem list https://github.com/iterative/example-mlem-get-started
Deployments:
 - myservice
Models:
 - rf
Envs:
 - staging
```

<admon type="note">

A [MLEM project] is required as target for `mlem list`. The other operations
(below) work with loose MLEM objects (not in a MLEM project) as well.

[mlem project]: /doc/command-reference/init

</admon>

## Loading objects (Python)

You can load [MLEM objects] from remote locations inside Python code with
`mlem.api.load()` by using an object name and its URL.

```py
from mlem.api import load

model = load(
    "rf",
    project="https://github.com/iterative/example-mlem-get-started",
    rev="main"
)
```

This fetches the `rf` model [from branch `main`] of the
`example-mlem-get-started` repo and loads it to memory.

[from branch `main`]:
  https://github.com/iterative/example-mlem-get-started/tree/main/.mlem/model

## Downloading objects

You can download MLEM objects to the local environment in with `mlem clone`
(CLI).

```cli
$ mlem clone rf \
  --project https://github.com/iterative/example-mlem-get-started \
  ml_model
⏳️ Loading meta from https://github.com/iterative/example-mlem-get-started/tree/main/.mlem/model/rf.mlem
🐏 Cloning https://github.com/iterative/example-mlem-get-started/tree/main/.mlem/model/rf.mlem
💾 Saving model to .mlem/model/ml_model.mlem
```

This places the `rf` model [from branch `main`] of the
`example-mlem-get-started` repo, renames it to `ml_model`, and places it in the
`.mlem/model` directory.

## Cloud storage

It's also possible to (down)load loose [MLEM objects] stored in any cloud
platform [supported by `fsspec`], e.g. Amazon S3. To do so, provide the file
system protocol & path as target/URL, e.g. `s3://<bucket>/`

<admon type="tip">

Loose objects are typically stored this way because they do not require
[versioning].

</admon>

```cli
$ mlem clone rf s3://example-mlem-get-started/rf
⏳️ Loading meta from .mlem/model/rf.mlem
🐏 Cloning .mlem/model/rf.mlem
💾 Saving model to s3://example-mlem-get-started/.mlem/model/rf.mlem
```

The `rf` model from S3 bucket `example-mlem-get-started` can also be
[loaded via API](#loading-objects-api) or used in the CLI as though it existed
locally:

```py
from mlem.api import load

model = load("rf", project="s3://example-mlem-get-started")
```

```cli
$ mlem apply rf \
  --project s3://example-mlem-get-started \
  test_x.csv --json
[1, 0, 2, 1, 1, 0, 1, 2, 1, 1, 2, 0, 0, 0, 0, 1, 2, 1, 1, 2, 0, 2, 0, 2, 2, 2, 2, 2, 0, 0, 0, 0, 1, 0, 0, 2, 1, 0]
```

[supported by `fsspec`]:
  https://filesystem-spec.readthedocs.io/en/latest/api.html#built-in-implementations
[versioning]: https://dvc.org/doc/use-cases/versioning-data-and-model-files
