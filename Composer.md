# Composer
## Overview
This document outlines the Composer configuration for the OAI-PMH implementation project. It includes the necessary dependencies, autoloading configuration, and scripts to facilitate development and testing.

## Install Composer (if not already installed)
Visit: https://getcomposer.org/download/

## Initialize Composer in your project
In your project root, run:

```bash
composer init
Follow the prompts to generate a composer.json file.
```

## Autoloading
### Autoloading with Composer
To enable autoloading of your classes, you need to define the PSR-4 autoloading rules in your `composer.json` file. This allows Composer to automatically load classes based on their namespace and directory structure.

### Autoloading Configuration
For the OAI-PMH implementation, you will typically have your classes organized under a `src/` directory, with namespaces that reflect their location. Edit your `composer.json` file to include the following autoloading configuration:

```json
{
  "autoload": {
    "psr-4": {
      "OaiPmh\\": "src/"
    }
  }
}
```

For testing purposes, you can also set up autoloading for your test classes. If your tests are located in a `tests/` directory, you can add the following to the `autoload-dev` section:

```json
{
  "autoload-dev": {
    "OaiPmh\\Tests\\": "tests/"
  }
}
```

### Generating the Autoloader
After setting up autoloading rules, run:

```bash
composer dump-autoload
```

This generates the vendor/autoload.php file and prepares the class map.

### Example Autoloading Usage
In your entry point (e.g., public/index.php, cli.php, or test bootstrap):

```php
require_once __DIR__ . '/../vendor/autoload.php';

use OaiPmh\UseCase\Identify;

// Now you can instantiate your classes without manual requires:
$useCase = new Identify();
```
### Namespace Structure
Ensure your classes are organized in a way that matches the namespace structure defined in your `composer.json`. For example, if you have a class `Identify` in the `UseCase` namespace, it should be located at `src/UseCase/Identify.php`.

Example for src/UseCase/Identify.php:

```php
<?php

namespace OaiPmh\UseCase;

class Identify
{
    // ...
}
```
## PHPUnit
To test the OAI-PMH implementation PHPUnit will be used.

PHPUnit can be installed using composer.
Run the following command to add PHPUnit as a development dependency:

```bash
composer require --dev phpunit/phpunit
```

To be able for PHPUnit to run the tests and measure coverage, a `phpunit.xml` configuration file is created in the root of the project. This file defines how PHPUnit should run the tests.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<phpunit bootstrap="vendor/autoload.php"
         colors="true"
         stopOnFailure="false">
    <testsuites>
        <testsuite name="Unit">
            <directory suffix="Test.php">tests</directory>
        </testsuite>
    </testsuites>
    <coverage processUncoveredFiles="true">
        <include>
            <directory suffix=".php">./src</directory>
        </include>
        <report>
            <clover outputFile="coverage/clover.xml"/>
            <html outputDirectory="coverage/html"/>
        </report>
    </coverage>
</phpunit>
```

This tells PHPUnit:
- Autoload via Composer
- Run all *Test.php files in the tests/ directory
- Generate code coverage reports in both Clover XML format and HTML format

## PHPStan
PHPStan is a static analysis tool for PHP that helps catch bugs and enforce coding standards.
To integrate PHPStan into your project, you can add it as a development dependency:

```bash
composer require --dev phpstan/phpstan
```
### PHPStan Configuration
Create a `phpstan.neon` configuration file in the root of your project:

```neon
parameters:
    level: max
    paths:
        - src
        - tests
    autoload_files:
        - vendor/autoload.php
includes:
        - vendor/phpstan/phpstan/conf/bleedingEdge.neon
        - vendor/phpstan/phpstan/conf/strictRule.neon
```

## php_code_sniffer
PHP_CodeSniffer is a tool that helps you maintain coding standards in your PHP codebase. It can automatically detect violations of a defined coding standard and can also fix some of them.
To integrate PHP_CodeSniffer into your project, you can add it as a development dependency:

```bash
composer require --dev squizlabs/php_codesniffer
```
### PHP_CodeSniffer Configuration

In phpcs.xml, you can define the coding standards and rules you want to enforce. Currently the configuration is set to use:
- PSR-12 as the coding standard
- Custom rules for whitespace and function argument spacing

The `phpcs.xml` file is created in the root of your project with the following content:

```xml
<?xml version="1.0"?>
<ruleset name="MyProject Coding Standards">
    <description>Enforces PSR-12 with some customizations</description>

    <!-- Base standard -->
    <rule ref="PSR12" />

    <!-- Include folders -->
    <file>src</file>
    <file>tests</file>

    <!-- Exclude specific files/folders -->
    <exclude-pattern>vendor/*</exclude-pattern>

    <!-- Show warnings and errors -->
    <arg name="colors"/>
    <arg name="extensions" value="php"/>

    <!-- Custom rule tweaks -->
    <rule ref="Generic.WhiteSpace.DisallowTabIndent">
        <severity>5</severity>
    </rule>

    <rule ref="Squiz.Functions.FunctionDeclarationArgumentSpacing">
        <properties>
            <property name="equalsSpacing" value="1"/>
        </properties>
    </rule>
</ruleset>
```

Next codesniffer needs to be added to the settings.json file of your project. The configuration uses the `phpcs.xml` file for the coding standard, which is located in the root of the project. It defines:
- The path to the coding standard file
- The folder where the PHP_CodeSniffer executables are located
- Automatic detection of the coding standard
- The action to run PHP_CodeSniffer on save

```json
{
    "phpSniffer.standard": "${workspaceFolder}/phpcs.xml",
    "phpSniffer.executablesFolder": ".\\vendor\\bin",
    "phpSniffer.autoDetect": true,
    "phpSniffer.run": "onSave"
}
```
