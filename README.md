Mission Control
===============

[![CircleCI](https://img.shields.io/circleci/project/github/mozilla/missioncontrol/master.svg)](https://circleci.com/gh/mozilla/missioncontrol)
[![codecov](https://codecov.io/gh/mozilla/missioncontrol/branch/master/graph/badge.svg)](https://codecov.io/gh/mozilla/missioncontrol)

Mission Control is a monitoring service for Firefox release health, it allows you
to view in (near) real time the rate of crashes and other quantitative measures of
quality. It uses the dataset generated by the [telemetry-streaming](https://github.com/mozilla/telemetry-streaming) library.

The server-side backend is written in Python using Django. The UI is written in [React](https://reactjs.org/), [Redux](http://redux.js.org/) and [metricsgraphics](http://metricsgraphicsjs.org/).

Getting in touch
----------------

If you have any questions about Mission Control (either as a user or contributor), the best place to ask is the [#missioncontrol](https://client00.chat.mibbit.com/?server=irc.mozilla.org&channel=%23missioncontrol) channel on irc.mozilla.org ([learn more about irc @ Mozilla](https://wiki.mozilla.org/IRC)).

Contributing
------------

We welcome contributions to Mission Control! Working on the UI component (see
instructions immediately below) does not require any special access to Mozilla's
internal systems.

If you’re looking for a way to jump in and contribute, our list of [good first issues](https://github.com/mozilla/missioncontrol/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) is a great place to start.

Instructions for development (UI only)
--------------------------------------

If you only want to hack on the UI, you can set up a local-only of missioncontrol which
pulls data from the current production server. You only need to have
[yarn](https://yarnpkg.com/) installed.

Run:

```bash
yarn install
yarn start
```

This should start up a webserver at http://localhost:5000 which you can connect to.

Instructions for development (full stack)
-----------------------------------------

Make sure you have [docker](https://docker.io), [docker-compose](https://github.com/docker/compose), and [yarn](https://yarnpkg.com/) installed.

Then run:

```bash
yarn install
cp .env-dist .env
make build
make up
make fixtures
```

After you have brought the environment up, you can bring up a development version of
the server by running `make shell` and then running `./manage.py runserver`
from there. You should then be able to connect to `http://localhost:8000` from
your web browser.

By default the environment uses a rather improverished set of test data, so
the environment will not be that interesting. If you have Mozilla credentials,
you can set up `PRESTO_URL` and `SECRET_KEY` variables in a `.env` file to have it pull data
from a production dataset. Once you have that set up, you should be able to
download a set of recent data from a shell environment (`make shell`) via the
load_measure_data subcommand. E.g.:

```bash
./manage.py load_measure_data linux release main_crashes
```

The recommended way of running the tests locally is via the shell environment.
After running `make shell`, execute:

```bash
pytest tests/
```

By default all tests and linters are run. Often you just want to run a subset
of the python tests. You can do this by adding some arguments to your tox
invocation:

```bash
tox -e tests -- -k tests/test_api.py  # run only tests in test_api.py
```

Instructions for deployment
---------------------------

The target environment for this project follows the [dockerflow](https://github.com/mozilla-services/Dockerflow) conventions.
In order to run it correctly, a number of environment variables need to be set up.
The full list of variables can be found in the web section of the docker-compose.yml file.
From a services standpoint, this project requires:
 - a Postgres DB to store the application data, defined by DATABASE_URL
 - a Presto/Athena service, defined by PRESTO_URL
 - an optional Redis cache service, defined by CACHE_URL
