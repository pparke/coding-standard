silverorange Coding Standards
=============================
[![Build Status](https://travis-ci.org/silverorange/coding-standard.svg?branch=master)](https://travis-ci.org/silverorange/coding-standard)

Coding standards for silverorange PHP projects. These are standards to be used
with the [PHPCS](https://github.com/squizlabs/PHP_CodeSniffer/wiki) tool.

Usage
-----
To use these rules for a project:

### 1. install as a require-dev dependency using composer
```sh
$ composer require --dev silverorange/coding-standard squizlabs/phpcs
```

### 2. add a `post-install-cmd` and `post-update-cmd` to register the coding standard with phpcs
Post install and post update are both required because `composer install` without a lock file will not execute the `post-install-cmd` script.
```json
{
  "scripts": {
    "post-install-cmd": "./vendor/bin/phpcs --config-set installed_paths vendor/bin/silverorange/coding-standard/src",
    "post-update-cmd": "./vendor/bin/phpcs --config-set installed_paths vendor/bin/silverorange/coding-standard/src"
  }
}
```

### 3. create a phpcs.xml
```xml
<?xml version="1.0"?>
<ruleset name="MyProjectName">
  <description>A custom coding standard.</description>

  <arg name="colors"/>
  <arg name="tab-width" value="4"/>
  <arg name="extensions" value="php"/>
  <arg name="encoding" value="utf-8"/>
  <arg name="warning-severity" value="0"/>

  <rule src="SilverorangeLegacy"/>
</ruleset>
```

### 4. create a composer script to lint the project
```json
{
  "scripts": {
    "lint": "./vendor/bin/phpcs"
  }
}
```

### 5. create a .travis.yml file to enforce the rules
For new packages:
```yml
language: php
php:
  - '5.6'
install: composer install
script:
  - >-
      ./vendor/bin/phpcs
      --standard=Silverorange
      --encoding=utf-8
      --warning-severity=0
      --extensions=php
      $(git diff--diff-filter=ACRM --name-only HEAD~1)
---
```

For legacy packages:
```yml
language: php
php:
  - '5.6'
install: composer install
script:
  - >-
      ./vendor/bin/phpcs
      --standard=SilverorangeTransitional
      --tab-width=4
      --encoding=utf-8
      --warning-severity=0
      --extensions=php
      $(git diff --diff-filter=ACRM --name-only HEAD~1)
---
```

Standards
---------
Three standards are provided:

### SilverorangeLegacy
Intended for linting the entire project for a legacy package. This omits some
rules we'd like to use for new code written for legacy packages in order to
run without error. It is not recommended to use this standard for new projects.

[Documentation](doc/legacy/README.md) exists for the legacy standard.

### SilverorangeTransitional
Intended for use as a post-commit hook or CI test. This ensures all new code
added to legacy packages follows best practices within the legacy guidelines.
This includes rules that will not pass for the entire project, but should pass
for all modified files in a new pull request.

### Silverorange
Based on [PSR-2](http://www.php-fig.org/psr/psr-2/). This should be used for
all new silverorange PHP packages. PSR-2 builds on, and includes all rules
from [PSR-1](http://www.php-fig.org/psr/psr-1/)

For autoloading classes, projects must follow
[PSR-4](http://www.php-fig.org/psr/psr-4/). This allows efficient auto-loading
and promotes organizing code using namespaces.

Sublime Setup
-------------
If you are using Sublime Text:

1. Set up Sublime Linter with PHPCS as described [here](https://github.com/SublimeLinter/SublimeLinter-phpcs).

2. In the Sublime Linter settings, make sure you have the following settings (do not remove the other settings):

```
{
    "user": {
        "linters": {
            "phpcs": {
                "@disable": false,
                "args": []
            }
        }
    }
}

```

3. Create a Sublime project in the project root.

4. Add the following to the Sublime project settings:

```
{
    "SublimeLinter": {
        "linters": {
            "phpcs": {
                "phpcs_executable_path": "{project}/vendor/bin/phpcs"
            }
        }
    }
}

```

This will allow you to use different rulesets with different projects.
