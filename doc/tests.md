# Working with Privacy Badger's tests

We have a few different types of tests:

* We use [unit tests](/doc/tests.md#unit-tests) for confirming that smaller pieces of code behave as expected.
* [Functional tests](/doc/tests.md#functional-tests) test the UI and that things integrate together properly.
* [Travis CI](/doc/tests.md#travis-ci) runs all these automatically for every pull request on both Chrome and Firefox.

## Travis CI

Every pull request runs the full suite of functional and unit tests on [Travis CI](https://travis-ci.org/). We test on latest stable Chrome and Firefox releases, as well as on Chrome Beta, Firefox Beta and Firefox ESR.

See [`.travis.yml`](/.travis.yml) for Travis configuration, [`scripts/setup_travis.sh`](/scripts/setup_travis.sh) for test setup, and [`scripts/run_travis.sh`](/scripts/run_travis.sh) for test execution procedures.

We use [ESLint](https://eslint.org) to flag potential JavaScript errors and style issues. Please see our [developer guide](/doc/develop.md#lint-your-changes) for setup instructions.

## Unit tests

We use [QUnit](https://qunitjs.com/) for unit tests.
Unit tests are defined in [`/src/tests/tests`](/src/tests/tests). Unit test dependencies live in [`/src/tests/lib`](/src/tests/lib).

To run unit tests, first [load Privacy Badger from source code](/doc/develop.md#install-from-source) (as we don't ship unit tests with published versions).
Once you loaded Badger from source, click on its button in your browser toolbar to open Badger's popup.
Then in the popup, click on the gear icon (⚙) to open the options page.
Your browser should navigate to an internal URL that starts with `chrome-extension://` or `moz-extension://` and ends with `/skin/options.html`.
Replace `/skin/options.html` with `/tests/index.html` and hit <kbd>Enter</kbd>.
This will open the unit test suite and run the tests.

## Functional tests

Our functional tests are written in [Python](https://www.python.org/) and driven by
[Selenium](https://selenium-python.readthedocs.io/) and [pytest](https://docs.pytest.org/en/latest/).
To run them in Chrome, you'll need to [install `chromedriver`](https://github.com/EFForg/privacybadger/blob/f780438ca146e932ebc80fa163ddcfdae4e56c25/scripts/setup_travis.sh#L4-L12). In Firefox, you need to [install `geckodriver`](https://github.com/EFForg/privacybadger/blob/f780438ca146e932ebc80fa163ddcfdae4e56c25/scripts/setup_travis.sh#L14-L28).
You also need some Python packages that can be installed by running:
```bash
$ pip install -r tests/requirements.txt
```

Now you should be able to run the Selenium tests!
Try them out by running the code below.
This should take several minutes.
```bash
$ BROWSER=chrome pytest -v
```

The `BROWSER` environment variable must be set. It must be one of:
* `BROWSER=/path/to/a/browser`
* the name of a browser executable that can be found like `which $BROWSER`
* or simply `BROWSER=chrome` or `BROWSER=firefox` if you have them installed

### Examples

Note that to use a debugger like `pdb` or `ipdb` you must pass the `-s` (`--capture=no`) flag to pytest.
```bash
# run qunit_test.py, with firefox, with verbose output (-v)
$ BROWSER=/usr/bin/firefox pytest -v tests/selenium/qunit_test.py

# run a specific test on a specific class in a specific module, on google-chrome-stable
$ BROWSER=google-chrome-stable pytest super_cookie_test.py::SuperCookieTest::test_should_detect_ls_of_third_party_frame

# run any tests whose name (including the module and class) matches the string cookie_test
# this is often useful as a less verbose way to run a single test
$ BROWSER=firefox pytest -k cookie_test
```

More pytest invocations can be found [here](https://docs.pytest.org/en/latest/usage.html) (these are very useful).

If you are on Linux, you can also run the tests headlessly (without displaying a GUI).
Install `Xvfb` with your system package manager, then set the `ENABLE_XVFB=1` environment variable.
Like this:

```bash
$ BROWSER=~/Downloads/firefox/firefox ENABLE_XVFB=1 pytest -s -v -k pbtest_org
```

Refer to the our Travis CI scripts for more information:
[`scripts/setup_travis.sh`](/scripts/setup_travis.sh) and
[`scripts/run_travis.sh`](/scripts/run_travis.sh).

