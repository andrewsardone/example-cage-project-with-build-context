An example [`cage` project][1] that demonstrates a bug with `cage up` when
using a git repo build context.

## `cage build` works

`cage build` properly generates a `.cage/pods/frontend.yml` file, and builds
the image:

```
❯ cage build
# docker build output

❯ cat .cage/pods/frontend.yml
---
version: "2"
services:
  web:
    build: "https://github.com/andrewsardone/intro-graphql-talk-ruby.git"
    environment:
      DATABASE_URL: "postgres://postgres@db:5432/example-cage-project-with-build-context_development"
      GLOBAL_PASSWORD: magic
      PROJECT_NAME: "example-cage-project-with-build-context"
      RACK_ENV: development
      RAILS_ENV: development
      SOME_PASSWORD: secret
    labels:
      io.fdy.cage.pod: frontend
      io.fdy.cage.shell: bash
      io.fdy.cage.srcdir: /usr/src/app
      io.fdy.cage.target: development
      io.fdy.cage.test: bundle exec rake
    ports:
      - "3000:3000" 

```

Note the generated `.cage/pods/frontend.yml` includes the proper `build` entry.

## `cage up` does _not_ work

`cage up` doesn't generate a proper `.cage/pods/frontend.yml` file; it is
missing a `build` entry:

```
❯ cage up
WARNING: docker-compose.yml file validation disabled until valico is updated (from compose_yml::v2::validate)
WARNING: docker-compose.yml file validation disabled until valico is updated (from compose_yml::v2::validate)
ERROR: The Compose file is invalid because:
Service web has neither an image nor a build context specified. At least one must be provided.
Error: error running 'docker-compose -p examplecageprojectwithbuildcontext -f /Users/andrew/source/andrewsardone/example-cage-project-with-build-context/.cage/pods/frontend.yml up -d'

❯ cat .cage/pods/frontend.yml
---
version: "2"
services:
  web:
    environment:
      DATABASE_URL: "postgres://postgres@db:5432/example-cage-project-with-build-context_development"
      GLOBAL_PASSWORD: magic
      PROJECT_NAME: "example-cage-project-with-build-context"
      RACK_ENV: development
      RAILS_ENV: development
      SOME_PASSWORD: secret
    labels:
      io.fdy.cage.pod: frontend
      io.fdy.cage.shell: bash
      io.fdy.cage.srcdir: /usr/src/app
      io.fdy.cage.target: development
      io.fdy.cage.test: bundle exec rake
    ports:
      - "3000:3000"
```

[1]: https://github.com/faradayio/cage
