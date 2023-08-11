# writefreely-docker

This is a [Docker][docker] image for [WriteFreely][writefreely], set up in a way
that makes it easier to deploy it in production, including the initial setup step.

Forked from [algernon/writefreely-docker](https://git.madhouse-project.org/algernon/writefreely-docker/src/tag/v0.13.2-2)

 [docker]: https://www.docker.com/
 [writefreely]: https://github.com/writeas/writefreely

## Overview

On default the image is set up to use SQLite for the database, it does not support MySQL
out of the box - but you can always provide your own `config.ini`. The config
file, the database, and the generated keys are all stored on the single volume
the image uses, mounted on `/data`.

The primary purpose of the image is to provide a single-step setup and upgrade
experience, where the initial setup and any upgrades are handled by the image
itself. As such, the image will create a default `config.ini` unless one already
exists, with reasonable defaults. It will also run database migrations, and save
a backup before doing so (which it will delete, if no migrations were
necessary).

### Breaking change!

As of 2023, the image runs as the `writefreely` user (uid 5000), rather than
root. Existing installs may need some permission changes to adjust to this.

## Getting started

To get started, the easiest way to test it out is running the following command:

```shell
docker run -p 8080:8080 -it --rm -v /some/path/to/data:/data tourblion/writefreely
```

Then point your browser to `http://localhost:8080`, and you should see
WriteFreely up and running.

## Setup

The image will perform an initial setup, unless the supplied volume already
contains a `config.ini`. Settings can be tweaked via environment variables, of
which you can find a list below. Do note that these environment variables are
*only* used for the initial setup as of this writing! If a configuration file
already exists, the environment variables will be blissfully ignored.

### Environment variables

- `WRITEFREELY_BIND_HOST` and `WRITEFREELY_BIND_PORT` determine the host and port WriteFreely will bind to. Defaults to `0.0.0.0` and `8080`, respectively.
- `WRITEFREELY_SITE_NAME` is the site title one wants. Defaults to "A Writefreely blog".
- `WRITEFREELY_SINGLE_USER`, `WRITEFREELY_OPEN_REGISTRATION`,
  `WRITEFREELY_MIN_USERNAME_LEN`, `WRITEFREELY_MAX_BLOG`,
  `WRITEFREELY_FEDERATION`, `WRITEFREELY_PUBLIC_STATS`, `WRITEFREELY_PRIVATE`,
  `WRITEFREELY_LOCAL_TIMELINE`, and `WRITEFREELY_USER_INVITES` all correspond to
  the similarly named `config.ini` settings. See the [WriteFreely docs][wf:docs]
  for more information about them.
- `WRITEFREELY_ADMIN_USER` and `WRITEFREELY_ADMIN_PASSWORD` will be used to automatically create an admin user, if they're specified. If either is missing, and admin user will not be created.

 [wf:docs]: https://writefreely.org/docs/latest/admin/config

### Build arguments

- `WRITEFREELY_UID` sets the default user (and group) id of the `writefreely` user created during build. Defaults to `5000`, only used during the build.
- `WRITEFREELY_VERSION` controls which version of Writefreel the image is build from. Defaults to `v0.13.2`, can be any tag or branch, or commit id.
- `WRITEFREELY_FORK` sets which fork - if any - to use. Defaults to `writefreely/writefreely`, and must be a GitHub repository at the moment.
