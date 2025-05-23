---
title: 'Setup'
---

# Setup

## Platform support

Each platform that cibuildwheel supports has its own prerequisites and platform-specific behaviors. cibuildwheel supports the following platforms:

* [Linux](./platforms/linux.md)
* [Windows](./platforms/windows.md)
* [macOS](./platforms/macos.md)
* [iOS](./platforms/ios.md)
* [Experimental: Pyodide (WebAssembly)](./platforms/pyodide.md)

## Run cibuildwheel locally (optional) {: #local}

Before getting to CI setup, it can be convenient to test cibuildwheel
locally to quickly iterate and track down issues without even touching CI.

Install cibuildwheel and run a build like this:

```sh
# using pipx (https://github.com/pypa/pipx)
pipx run cibuildwheel

# or,
pip install cibuildwheel
cibuildwheel
```

You should see the builds taking place. You can experiment with options using environment variables or pyproject.toml.

!!! tab "Environment variables"

    cibuildwheel will read config from the environment. Syntax varies, depending on your shell:

    > POSIX shell (Linux/macOS)

    ```sh
    # run a command to set up the build system
    export CIBW_BEFORE_ALL='uname -a'

    cibuildwheel
    ```

    > CMD (Windows)

    ```bat
    set CIBW_BEFORE_ALL='uname -a'

    cibuildwheel
    ```

!!! tab "pyproject.toml"

    If you write your options into [`pyproject.toml`](options.md#configuration-file), you can work on your options locally, and they'll be automatically picked up when running in CI.

    > pyproject.toml

    ```
    [tool.cibuildwheel]
    before-all = "uname -a"
    ```

    Then invoke cibuildwheel, like:

    ```console
    cibuildwheel
    ```

## Configure a CI service

### GitHub Actions [linux/mac/windows] {: #github-actions}

To build Linux, macOS, and Windows wheels using GitHub Actions, create a `.github/workflows/build_wheels.yml` file in your repo.

!!! tab "Action"
    For GitHub Actions, `cibuildwheel` provides an action you can use. This is
    concise and enables easier auto updating via GitHub's Dependabot; see
    [Automatic updates](faq.md#automatic-updates).

    > .github/workflows/build_wheels.yml

    ```yaml
    {% include "../examples/github-minimal.yml" %}
    ```

    Use `env:` to pass [build options](options.md) and `with:` to set
    `package-dir: .`, `output-dir: wheelhouse` and `config-file: ''`
    locations (those values are the defaults).

!!! tab "pipx"
    The GitHub Actions runners have pipx installed, so you can easily build in
    just one line. This is internally how the action works; the main benefit of
    the action form is easy updates via GitHub's Dependabot.

    > .github/workflows/build_wheels.yml

    ```yaml
    {% include "../examples/github-pipx.yml" %}
    ```

!!! tab "Generic"
    This is the most generic form using setup-python and pip; it looks the most
    like the other CI examples. If you want to avoid having setup that takes
    advantage of GitHub Actions features or pipx being preinstalled, this might
    appeal to you.

    > .github/workflows/build_wheels.yml
    {%
       include-markdown "../README.md"
       start="<!--generic-github-start-->"
       end="<!--generic-github-end-->"
    %}

Commit this file, and push to GitHub - either to your default branch, or to a PR branch. The build should start automatically.

For more info on this file, check out the [docs](https://help.github.com/en/actions/reference/workflow-syntax-for-github-actions).

[`examples/github-deploy.yml`](https://github.com/pypa/cibuildwheel/blob/main/examples/github-deploy.yml) extends this minimal example to include iOS and Pyodide builds, and a demonstration of how to automatically upload the built wheels to PyPI.


### Azure Pipelines [linux/mac/windows] {: #azure-pipelines}

To build Linux, Mac, and Windows wheels on Azure Pipelines, create a `azure-pipelines.yml` file in your repo.

> azure-pipelines.yml

```yaml
{% include "../examples/azure-pipelines-minimal.yml" %}
```

Commit this file, enable building of your repo on Azure Pipelines, and push.

Wheels will be stored for you and available through the Pipelines interface. For more info on this file, check out the [docs](https://docs.microsoft.com/en-us/azure/devops/pipelines/yaml-schema).

### Travis CI [linux/windows] {: #travis-ci}

To build Linux and Windows wheels on Travis CI, create a `.travis.yml` file in your repo.

> .travis.yml

```yaml
{% include "../examples/travis-ci-minimal.yml" %}
```

Commit this file, enable building of your repo on Travis CI, and push.

Then setup a deployment method by following the [Travis CI deployment docs](https://docs.travis-ci.com/user/deployment/), or see [Delivering to PyPI](deliver-to-pypi.md). For more info on `.travis.yml`, check out the [docs](https://docs.travis-ci.com/).

[`examples/travis-ci-deploy.yml`](https://github.com/pypa/cibuildwheel/blob/main/examples/travis-ci-deploy.yml) extends this minimal example with a demonstration of how to automatically upload the built wheels to PyPI.

### AppVeyor [linux/mac/windows] {: #appveyor}

To build Linux, Mac, and Windows wheels on AppVeyor, create an `appveyor.yml` file in your repo.

> appveyor.yml

```yaml
{% include "../examples/appveyor-minimal.yml" %}
```

Commit this file, enable building of your repo on AppVeyor, and push.

AppVeyor will store the built wheels for you - you can access them from the project console. Alternatively, you may want to store them in the same place as the Travis CI build. See [AppVeyor deployment docs](https://www.appveyor.com/docs/deployment/) for more info, or see [Delivering to PyPI](deliver-to-pypi.md) below.

For more info on this config file, check out the [docs](https://www.appveyor.com/docs/).

### CircleCI [linux/mac] {: #circleci}

To build Linux and Mac wheels on CircleCI, create a `.circleci/config.yml` file in your repo,

> .circleci/config.yml

```yaml
{% include "../examples/circleci-minimal.yml" %}
```

Commit this file, enable building of your repo on CircleCI, and push.

!!! note
    CircleCI doesn't enable free macOS containers for open source by default, but you can ask for access. See [here](https://circleci.com/docs/2.0/oss/#overview) for more information.

CircleCI will store the built wheels for you - you can access them from the project console. Check out the CircleCI [docs](https://circleci.com/docs/2.0/configuration-reference/#section=configuration) for more info on this config file.

### Gitlab CI [linux] {: #gitlab-ci}

To build Linux wheels on Gitlab CI, create a `.gitlab-ci.yml` file in your repo,

> .gitlab-ci.yml

```yaml
{% include "../examples/gitlab-minimal.yml" %}
```

Commit this file, and push to Gitlab. The pipeline should start automatically.

Gitlab will store the built wheels for you - you can access them from the Pipelines view. Check out the Gitlab [docs](https://docs.gitlab.com/ee/ci/yaml/) for more info on this config file.

### Cirrus CI [linux/mac/windows] {: #cirrus-ci}

To build Linux, Mac, and Windows wheels on Cirrus CI, create a `.cirrus.yml` file in your repo,

> .cirrus.yml

```yaml
{% include "../examples/cirrus-ci-minimal.yml" %}
```

Commit this file, enable building of your repo on Cirrus CI, and push.

Cirrus CI will store the built wheels for you - you can access them from the individual task view. Check out the Cirrus CI [docs](https://cirrus-ci.org/guide/writing-tasks/) for more info on this config file.

> ⚠️ Got an error? Check the [FAQ](faq.md).

## Next steps

Once you've got the wheel building successfully, you might want to set up [testing](options.md#test-command) or [automatic releases to PyPI](deliver-to-pypi.md#automatic-method).

<script>
  document.addEventListener('DOMContentLoaded', function() {
    $('.toctree-l3>a, .rst-content h3').each(function(i, el) {
      var text = $(el).text()
      var match = text.match(/(.*) \[([a-z/]+)\]/);

      if (match) {
        var iconHTML = $.map(match[2].split('/'), function(ident) {
          switch (ident) {
            case 'linux':
              return '<i class="fa fa-linux" aria-hidden="true"></i>'
            case 'windows':
              return '<i class="fa fa-windows" aria-hidden="true"></i>'
            case 'mac':
              return '<i class="fa fa-apple" aria-hidden="true"></i>'
          }
        }).join(' ');

        $(el).append(
          $('<div>')
            .append(iconHTML)
            .css({float: 'right'})
        )
        $(el).contents()
          .filter(function(){ return this.nodeType == 3; }).first()
          .replaceWith(match[1]);
      }
    });
  });
</script>
