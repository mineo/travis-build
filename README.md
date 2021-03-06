# Travis Build [![Build Status](https://travis-ci.org/travis-ci/travis-build.svg?branch=master)](https://travis-ci.org/travis-ci/travis-build)

Travis Build exposes an API that [Travis
Workers](https://github.com/travis-ci/worker) and [Job
Board](https://github.com/travis-ci/job-board) use to generate a bash script
which is then copied to the job execution environment and executed, with the
resulting output streamed back to Travis.

This code base has gone through several iterations of development, and was
originally extracted from [the legacy Travis
Worker](https://github.com/travis-ci/travis-worker), before taking its current
form.

## Running test suites

Run

``` bash
bundle exec rake spec
```

## Use as addon for CLI

You can set travis-build up as a plugin for the [command line
client](https://github.com/travis-ci/travis.rb):

``` bash
ln -s PATH_TO_TRAVIS_BUILD ~/.travis/travis-build
gem install bundler
bundle install --gemfile ~/.travis/travis-build/Gemfile
bundler binstubs travis
```

You will now be able to run `travis compile`, which produces the bash script
that runs the specified job, except that the secure environment variables are
not defined, and that the build matrix expansion is not considered, e.g:

``` bash
~/.travis/travis-build/bin/travis compile
```

### _Important_

The resulting script contains commands that make changes to the system on which
it is executed (e.g., edit `/etc/resolv.conf`, install software).  Some require
`sudo` privileges and they are not easily undone.

It is highly recommended that you run this in a container or other virtualized
environment.

### Invocation

The command can be invoked in 3 ways:

Without an argument, it produces the bash script for the local `.travis.yml`
without considering `env` and `matrix` values (`travis-build` is unable to
expand these keys correctly).

``` bash
~/.travis/travis-build/bin/travis compile
```

With a single integer, it produces the script for the given build (or the first
job of that build matrix).

``` bash
~/.travis/travis-build/bin/travis compile 8
```

With an argument of the form `M.N`, it produces the bash script for the job
`M.N`.

``` bash
~/.travis/travis-build/bin/travis compile 351.2
```

The generated script can be used in a container or virtualized environment that
closely mimics Travis CI's build environment to aid you in debugging the build
failures.  Instructions for running such a container are available
[in the Travis CI docs](https://docs.travis-ci.com/user/common-build-problems/#running-a-container-based-docker-image-locally).

## Raw CLI script

In addition to the travis CLI plugin you can also run the standalone CLI script:

``` bash
bundle exec script/compile < payload.json > build.sh
```

## Docker container

If you want to run travis-build locally on your machine (e.g. to interact with
[worker](https://github.com/travis-ci/worker)), you can also run it as a docker
container with docker-compose:

``` bash
docker-compose up --build
```

## License & copyright information

See [LICENSE](./LICENSE) file.

Copyright (c) 2011-2016 [Travis CI development
team](https://github.com/travis-ci).
