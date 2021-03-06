# Serverless Python Requirements

[![serverless](http://public.serverless.com/badges/v3.svg)](http://www.serverless.com)
[![circleci](https://img.shields.io/circleci/project/github/RedSparr0w/node-csgo-parser/master.svg)](https://circleci.com/gh/UnitedIncome/serverless-python-requirements)
[![npm](https://nodei.co/npm/serverless-python-requirements.png?mini=true)](https://www.npmjs.com/package/serverless-python-requirements)

A Serverless v1.x plugin to automatically bundle dependencies from
`requirements.txt` and make them available in your `PYTHONPATH`.

**Requires Serverless >= v1.12**

## Install

```
sls plugin install -n serverless-python-requirements
```


## Cross compiling!
Compiling non-pure-Python modules or fetching their manylinux wheels is
supported on non-linux OSs via the use of Docker and the
[docker-lambda](https://github.com/lambci/docker-lambda) image.
To enable docker usage, add the following to your `serverless.yml`:
```yaml
custom:
  pythonRequirements:
    dockerizePip: true
```
The dockerizePip option supports a special case in addition to booleans of `'non-linux'` which makes
it dockerize only on non-linux environments.


To utilize your own Docker container instead of the default, add the following to your `serverless.yml`:
```yaml
custom:
  pythonRequirements:
    dockerImage: <image name>:tag
```
This must be the full image name and tag to use, including the runtime specific tag if applicable.

## Pipenv support :sparkles::cake::sparkles:
If you include a `Pipfile` and have `pipenv` installed instead of a `requirements.txt` this will use
`pipenv lock --r` to generate them. It is fully compatible with all options such as `zip` and
`dockerizePip`. If you don't want this plugin to generate it for you, set the following option:
```yaml
custom:
  pythonRequirements:
    usePipenv: false
```


## Dealing with Lambda's size limitations
To help deal with potentially large dependencies (for example: `numpy`, `scipy`
and `scikit-learn`) there is support for compressing the libraries. This does
require a minor change to your code to decompress them.  To enable this add the
following to your  `serverless.yml`:
```yaml
custom:
  pythonRequirements:
    zip: true
```

and add this to your handler module before any code that imports your deps:
```python
try:
  import unzip_requirements
except ImportError:
  pass
```

## Omitting Packages 
You can omit a package from deployment with the `noDeploy` option. Note that
dependencies of omitted packages must explicitly be omitted too.
By default, this will not install the AWS SDKs that are already installed on
Lambda. This example makes it instead omit pytest:
```yaml
custom:
  pythonRequirements:
    noDeploy:
      - pytest
```

## Extra Config Options
### extra pip arguments
You can specify extra arguments to be passed to pip like this:
```yaml
custom:
  pythonRequirements:
      dockerizePip: true
      pipCmdExtraArgs:
          - --cache-dir
          - .requirements-cache
```

### Customize requirements file name
[Some `pip` workflows involve using requirements files not named
`requirements.txt`](https://www.kennethreitz.org/essays/a-better-pip-workflow).
To support these, this plugin has the following option:

```yaml
custom:
  pythonRequirements:
    fileName: requirements-prod.txt
```

### Customize Python executable
Sometimes your Python executable isn't available on your `$PATH` as `python2.7`
or `python3.6` (for example, windows or using pyenv).
To support this, this plugin has the following option:
```yaml
custom:
  pythonRequirements:
    pythonBin: /opt/python3.6/bin/python
```

## Manual invocations

The `.requirements` and `requirements.zip`(if using zip support) files are left
behind to speed things up on subsequent deploys. To clean them up, run
`sls requirements clean`. You can also create them (and `unzip_requirements` if
using zip support) manually with `sls requirements install`.

## Invalidate requirements caches on package

If you are using your own Python library, you have to cleanup
`.requirements` on any update. You can use the following option to cleanup
`.requirements` everytime you package.

```
custom:
  pythonRequirements:
    invalidateCaches: true
```

## Contributors
 * [@dschep](https://github.com/dschep) - Lead developer & maintainer
 * [@azurelogic](https://github.com/azurelogic) - logging & documentation fixes
 * [@abetomo](https://github.com/abetomo) - style & linting
 * [@angstwad](https://github.com/angstwad) - `deploy --function` support
 * [@mather](https://github.com/mather) - the cache invalidation option
 * [@rmax](https://github.com/rmax) - the extra pip args option
 * [@bsamuel-ui](https://github.com/bsamuel-ui) - Python 3 support
 * [@suxor42](https://github.com/suxor42) - fixing permission issues with Docker on Linux
 * [@mbeltran213](https://github.com/mbeltran213) - fixing docker linux -u option bug
 * [@Tethik](https://github.com/Tethik) - adding usePipenv option
 * [@miketheman](https://github.com/miketheman) - fixing bug with includes when using zip option
 * [@wattdave](https://github.com/wattdave) - fixing bug when using `deploymentBucket`
