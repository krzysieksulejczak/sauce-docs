---
id: yaml
title: Configuring Your TestCafe Tests
sidebar_label: YAML Configuration
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import useBaseUrl from '@docusaurus/useBaseUrl';

`saucectl` relies on a YAML specification file to determine exactly which tests to run and how to run them. To customize `saucectl` to run your TestCafe tests, simply modify the properties of the YAML file accordingly. This page defines each of the configuration properties specific to running TestCafe tests.

## Setting an Alternative Configuration File

By default, `saucectl` looks for the `config.yml` file in the `.sauce` folder of your project root, but you can actually specify a different file, or if you are using multiple frameworks or need to configure different sets of tests to run separately, you may choose to have multiple configuration files that you can direct `saucectl` to reference as necessary.

Use the following configuration at runtime to direct `saucectl` to use any configuration file you choose:

```bash
saucectl run -c ./path/to/{config-file}.yml
```

:::note YAML Required
While you can use multiple files of different names or locations to specify your configurations, each file must be a `*.yml` and follow the `saucectl` syntax. Our IDE Integrations (e.g., [Visual Studio Code](/dev/cli/saucectl/usage/ide/vscode)) can help you out by validating the YAML files and provide handy suggestions, so make sure to check them out!
:::

## Example Configuration

```yaml reference
https://github.com/saucelabs/saucectl-testcafe-example/blob/master/.sauce/config.yml
```

Each of the properties supported for running TestCafe tests through `saucectl` is defined below.

## `apiVersion`

<p><small>| REQUIRED | STRING |</small></p>

Identifies the version of the underlying configuration schema. At this time, `v1alpha` is the only supported value.

```yaml
apiVersion: v1alpha
```

---

## `kind`

<p><small>| REQUIRED | STRING/ENUM |</small></p>

Specifies which framework is associated with the automation tests configured in this specification.

```yaml
kind: testcafe
```

---

## `showConsoleLog`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Controls whether the contents of `console.log` are always shown in the local output of saucectl. By default (false), `console.log` is only shown for failed suites.

```yaml
showConsoleLog: true
```

---

## `defaults`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies any default settings for the project.

```yaml
defaults:
  timeout: 15m
```

---

### `timeout`

<p><small>| OPTIONAL | DURATION |</small></p>

Instructs how long (in `ms`, `s`, `m`, or `h`) `saucectl` should wait for each suite to complete. You can override this setting for individual suites using the `timeout` setting within the [`suites`](#suites) object. If not set, the default value is `0` (unlimited).

```yaml
  timeout: 15m
```

---

## `sauce`

<p><small>| OPTIONAL | OBJECT |</small></p>

The parent property containing all settings related to how tests are run and identified in the Sauce Labs platform.

```yaml
sauce:
  region: eu-central-1
  metadata:
    tags:
      - e2e
      - release team
      - other tag
    build: Release $CI_COMMIT_SHORT_SHA
  concurrency: 10
```

---

### `region`

<p><small>| OPTIONAL | STRING/ENUM |</small></p>

Specifies through which Sauce Labs data center tests will run. Valid values are: `us-west-1` or `eu-central-1`.

```yaml
  region: eu-central-1
```

---

### `metadata`

<p><small>| OPTIONAL | OBJECT |</small></p>

The set of properties that allows you to provide additional information about your project that helps you distinguish it in the various environments in which it is used and reviewed, and also helps you apply filters to easily isolate tests based on metrics that are meaningful to you, as shown in the following example:

```yaml
metadata:
  build: RC 10.4.a
  tags:
    - e2e
    - release team
    - beta
    - featurex
```

---

### `concurrency`

<p><small>| OPTIONAL | INTEGER |</small></p>

Sets the maximum number of suites to execute at the same time. If the test defines more suites than the max, excess suites are queued and run in order as each suite completes.

:::caution
For tests running on Sauce, set this value to equal or less than your Sauce concurrency allowance, as setting a higher value may result in jobs dropped by the server.
:::

```yaml
  concurrency: 5
```

Alternatively, you can override the file setting at runtime by setting the concurrency flag as an inline parameter of the `saucectl run` command:

```bash
saucectl run --ccy 5
```

---

### `retries`

<p><small>| OPTIONAL | INTEGER |</small></p>

Sets the number of times to retry a failed suite. For more settings, you can refer to [passThreshold](#passThreshold).

```yaml
  retries: 1
```

Alternatively, you can override the file setting at runtime by setting the retries flag as an inline parameter of the `saucectl run` command:

```bash
saucectl run --retries 1
```

---

### `tunnel`

<p><small>| OPTIONAL | OBJECT |</small></p>

`saucectl` supports using [Sauce Connect](/secure-connections/sauce-connect/proxy-tunnels/) to establish a secure connection with Sauce Labs. To do so, launch a tunnel; then provide the name and owner (if applicable) in this property.

```yaml
sauce:
  tunnel:
    name: your_tunnel_name
    owner: tunnel_owner_username
```

---

#### `name`

<p><small>| OPTIONAL | STRING |</small></p>

Identifies an active Sauce Connect tunnel to use for secure connectivity to the Sauce Labs cloud.

:::note
This property replaces the former `id` property, which is deprecated.
:::

```yaml
sauce:
  tunnel:
    name: your_tunnel_name
```

---

#### `owner`

<p><small>| OPTIONAL | STRING |</small></p>

Identifies the Sauce Labs user who created the specified tunnel, which is required if the user running the tests did not create the tunnel.

:::note
This property replaces the former `parent` property, which is deprecated.
:::

```yaml
sauce:
  tunnel:
    name: your_tunnel_name
    owner: tunnel_owner_username
```

---

### `visibility`

<p><small>| OPTIONAL | STRING |</small></p>

Sets the visibility level of test results for suites run on Sauce Labs. If unspecified or empty, `team` visibility will be applied. Valid values are:

- `public`: Accessible to anyone.
- `public restricted`: Share your job's results page and video, but keeps the logs only for you.
- `share`: Only accessible to people with a valid link.
- `team`: (Default) Only accessible to people under the same root account as you.
- `private`: Only you (the owner) will be able to view assets and test results page.

```yaml
sauce:
  visibility: private
```

---

### `launchOrder`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies the execution order for your test suites. When set to `fail rate`, test suites with the highest failure rate will execute first. If unspecified, test suites will execute in the order in which they are written in the configuration file.

```yaml
sauce:
  launchOrder: fail rate
```

---

## `env`

<p><small>| OPTIONAL | OBJECT |</small></p>

A property containing one or more environment variables that are global for all tests suites in this configuration. Values set in this global property will overwrite values set for the same environment variables set at the suite level.

```yaml
  env:
    hello: world
    my_var: $MY_VAR  # You can also pass through existing environment variables through parameter expansion
```

---

## `rootDir`

<p><small>| REQUIRED | OBJECT |</small></p>

The directory of files that need to be bundled and uploaded for the tests to run. Ignores what is specified in `.sauceignore`. See [Tailoring Your Test File Bundle](#tailoring-your-test-file-bundle) for more details. The following examples show the different relative options for setting this value.

```yaml
  rootDir: "./" # Use the current directory
```

```yaml
  rootDir: "packages/subpackage" # Some other package from within a monorepo
```

:::caution
Only the files contained within `rootDir` will be available during the tests. Any reference to a file that is not included in `rootDir` will make the tests fail.
:::

---

## `npm`

<p><small>| OPTIONAL | OBJECT |</small></p>

A parent property specifying the configuration details for any `npm` dependencies. Packages listed are installed in the environment prior to your tests executing.

```yaml
npm:
  registry: https://registry.npmjs.org
  packages:
    lodash: "4.17.20"
    "@babel/preset-typescript": "7.12"
    "@testcafe/react": "^5.0.1"
```

---

### `registry`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies the location of the npm registry source. If the registry source is a private address and you are running tests on Sauce Cloud, you can provide access to the registry source using [Sauce Connect](/dev/cli/saucectl/#run-tests-on-sauce-labs-with-sauce-connect).

```yaml
  registry: https://registry.npmjs.org
```

---

### `packages`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies any npm packages that are required to run tests and should, therefore, be installed on the Sauce Labs VM. See [Including Node Dependencies](#including-node-dependencies).

```yaml
  packages:
    lodash: "4.17.20"
    "@babel/preset-typescript": "7.12"
    "@testcafe/react": "^5.0.1"
```

---

### `dependencies`

<p><small>| OPTIONAL | ARRAY |</small></p>

Specifies any npm packages that are required to run tests and should, therefore, be included in the bundle.
Unlike `packages`, which installs dependencies on the VM, the dependencies specified here have to be already installed in the local `node_modules` folder. These dependencies, along with any related transitive dependencies, are then included in the bundle that is uploaded to Sauce Labs.

If you have already been including `node_modules` in your bundle, then this feature will help you speed up your tests by reducing the amount of files in the bundle. A smaller bundle will upload and extract faster, which speeds up the setup on the VM, facilitating a faster test feedback cycle.

Take note that the syntax is different from `packages`. It's a simple **list** of dependencies, without the need to specify the version.

```yaml
npm:
  dependencies:
    - lodash
```

To use this feature, make sure that `node_modules` is not ignored via `.sauceignore`.

:::caution
This feature is highly experimental.
:::

---

## `reporters`

<p><small>| OPTIONAL | OBJECT |</small></p>

Configures additional reporting capabilities provided by `saucectl`.

```yaml
reporters:
junit:
enabled: true
filename: saucectl-report.xml
```

---

### `junit`

<p><small>| OPTIONAL | OBJECT |</small></p>

The JUnit reporter gathers JUnit reports from all jobs and combines them into a single report.

```yaml
reporters:
junit:
enabled: true
filename: saucectl-report.xml
```

---

### `json`

<p><small>| OPTIONAL | OBJECT |</small></p>

The JSON reporter gathers test results from all jobs and combines them into a single report.

```yaml
reporters:
  json:
    enabled: true
    filename: saucectl-report.json
    webhookURL:saucectl-report.json
```

---

#### `enabled`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Toggles the reporter on/off.

```yaml
    enabled: true
```

---

#### `webhookURL`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies the webhook URL. When saucectl test is finished, it'll send an HTTP POST with a JSON payload to the configured webhook URL.

```yaml
    webhookURL: https://my-webhook-url
```

---

#### `filename`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies the report filename. Defaults to "saucectl-report.json".

```yaml
    filename: my-saucectl-report.json
```

---

## `artifacts`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies how to manage test artifacts, such as logs, videos, and screenshots.

```yaml
artifacts:
  cleanup: true
  download:
    when: always
    match:
      - junit.xml
    directory: ./artifacts/
```

---

### `cleanup`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

When set to `true`, all contents of the specified download directory are cleared before any new artifacts from the current test are downloaded.

```yaml
  cleanup: true
```

---

### `download`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies the settings related to downloading artifacts from tests run by `saucectl`.

```yaml
  download:
    when: always
    match:
      - junit.xml
    directory: ./artifacts/
```

---

#### `when`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies when and under what circumstances to download artifacts. Valid values are:

- `always`: Always download artifacts.
- `never`: Never download artifacts.
- `pass`: Download artifacts for passing suites only.
- `fail`: Download artifacts for failed suites only.

```yaml
    when: always
```

---

#### `match`

<p><small>| OPTIONAL | STRING/ARRAY |</small></p>

Specifies which artifacts to download based on whether they match the name or file type pattern provided. Supports the wildcard character `*` (use quotes for best parsing results with wildcard).

```yaml
  match:
    - junit.xml
    - "*.log"
```

---

#### `directory`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies the path to the folder location in which to download artifacts. A separate subdirectory is generated in this location for each suite for which artifacts are downloaded. The name of the subdirectory will match the suite name. If a directory with the same name already exists, the new one will be suffixed by a serial number.

```yaml
    directory: ./artifacts/
```

---

## `notifications`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies how to set up automatic test result alerts.

```yaml
notifications:
  slack:
    channels:
      - "saucectl-results"
      - "testcafe-tests"
    send: always
```

---

### `slack`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies the settings related to sending tests result notifications through Slack. See [Slack Integration](/basics/integrations/slack) for information about integrating your Sauce Labs account with your Slack workspace.

```yaml
  slack:
    channels: "saucectl-testcafe"
    send: always
```

---

#### `channels`

<p><small>| OPTIONAL | STRING/ARRAY |</small></p>

The set of Slack channels to which the test result notifications are to be sent.

```yaml
  slack:
    channels:
      - "saucectl-results"
      - "testcafe-team"
    send: always
```

---

#### `send`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies when and under what circumstances to send notifications to specified Slack channels. Valid values are:

- `always`: Send notifications for all test results.
- `never`: Do not send any test result notifications.
- `pass`: Send notifications for passing suites only.
- `fail`: Send notifications for failed suites only.

```yaml
  slack:
    channels: "saucectl-testcafe-tests"
    send: always
```

---

## `testcafe`

<p><small>| REQUIRED | OBJECT |</small></p>

The parent property containing the details specific to the TestCafe project.

```yaml
testcafe:
  version: 2.2.0
```

---

### `version`

<p><small>| REQUIRED | STRING |</small></p>

The version of TestCafe that is compatible with the tests defined in this file. See [Supported Testing Platforms](/web-apps/automated-testing/testcafe#supported-testing-platforms) for the list of TestCafe versions supported by `saucectl` and their compatible test platforms.

```yaml
  version: 2.2.0
```

:::tip
You can also define a path to your `package.json`. This will make saucectl use the same `testcafe` package version that's defined in your projects `devDependencies` or `dependencies` map.

The path to your `package.json` file will be relative to the `rootDir` of your configuration.
:::

---

## `suites`

<p><small>| REQUIRED | OBJECT |</small></p>

The set of properties providing details about the test suites to run. May contain multiple suite definitions. See the full [example config](#example-configuration) for an illustration of multiple suite definitions.

---

### `name`

<p><small>| REQUIRED | STRING |</small></p>

The name of the test suite, which will be reflected in the results and related artifacts.

```yaml
  - name: "saucy test"
```

---

### `env`

<p><small>| OPTIONAL | OBJECT |</small></p>

A property containing one or more environment variables that may be referenced in the tests for this suite. Expanded environment variables are supported. Values set here will be overwritten by values set in the global `env` property.

```yaml
  env:
    hello: world
    my_var: $MY_VAR
```

---

### `browserName`

<p><small>| REQUIRED | STRING |</small></p>

The name of the browser in which to run this test suite.
Available browser names: `chrome`, `firefox`, `microsoftedge`(only for sauce mode) and `safari`(only for sauce mode on macOS or iOS simulators)

```yaml
    browser: "firefox"
```

---

### `browserVersion`

<p><small>| OPTIONAL | STRING |</small></p>

The version of the browser to use for this test suite.

```yaml
    browserVersion: "85.0"
```

---

### `browserArgs`

<p><small>| OPTIONAL | ARRAY |</small></p>

Pass flags to configure how TestCafe launches the selected browser. Review supported flags for [Chrome/Chromium](https://peter.sh/experiments/chromium-command-line-switches/)

```yaml
    browserArgs: ["--no-sandbox", "--disable-features=site-per-process"]
```

---

### `headless`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Determines whether to run the test suite in headless mode.

```yaml
  headless: true
```
---

### `platformName`

<p><small>| OPTIONAL | STRING |</small></p>

A specific operating system and version on which to run the specified browser and test suite. Defaults to a platform that is supported by `saucectl` for the chosen browser.

```yaml
    platformName: "Windows 10"
```

---

### `screenResolution`

<p><small>| OPTIONAL | STRING |</small></p>

Specifies a browser window screen resolution, which may be useful if you are attempting to simulate a browser on a particular device type. See [Test Configurations](/basics/test-config-annotation/test-config) for a list of available resolution values.

```yaml
    screenResolution: "1920x1080"
```

---

### `shard`

<p><small>| OPTIONAL | STRING |</small></p>

When sharding is configured, saucectl automatically splits the tests (e.g., by spec or concurrency) so that they can easily run in parallel.
For sharding by concurrency, saucectl splits test files into several groups (the number of groups is determined by the concurrency setting). Each group will then run as an individual job.

Selectable values: `spec` to shard by spec file, `concurrency` to shard by concurrency. Remove this field or leave it empty `""` for no sharding.

:::tip
To split tests in the most efficient way possible, use:

- `spec` when the number of specs is less than the configured concurrency.
- `concurrency` when the number of specs is larger than the configured concurrency.
:::

```yaml
suites:
  - name: "I am sharded"
    shard: spec
```

---

### `src`

<p><small>| REQUIRED | OBJECT |</small></p>

The explicit name, file glob, or location of the test files to be included in this suite.

```yaml
  src:
    - "tests/test_file1.test.js"
    - "tests/integrations"
    - "*/*.test.js"
```

---

### `excludedTestFiles`

<p><small>| OPTIONAL | ARRAY/REGEX |</small></p>

Excludes test files to skip the tests. Files are matched by shell pattern, such as the explicit name, file glob, or location of the test files.

```yaml
      excludedTestFiles: [ "*/*.test.js" ]
```

---

### `filter`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specify a set of criteria to limit which tests in the `src` directory to execute for the suite.

```yaml
suites:
  - name: Example Suite
    filter:
      test: browser-should-display-time
      testGrep: browser.*
      fixture: browswer-expectations
      fixtureGrep: browser.*
      testMeta:
        region: us-west-1
      fixtureMeta:
        env: staging
```

---

#### `test`

<p><small>| OPTIONAL | STRING |</small></p>

Runs a test with the specified name.

```yaml
filter:
  test: browser-should-display-time
```

---

#### `testGrep`

<p><small>| OPTIONAL | STRING/REGEX |</small></p>

Runs tests whose names match the specified `grep` pattern.

```yaml
filter:
  testGrep: should-.*
```

---

#### `fixture`

<p><small>| OPTIONAL | STRING |</small></p>

Runs a test with the specified fixture name.

```yaml
filter:
  fixture: browswer-expectations
```

---

#### `fixtureGrep`

<p><small>| OPTIONAL | STRING/REGEX |</small></p>

Runs any tests included in fixtures whose names match the specified `grep` patterns.

```yaml
filter:
  fixtureGrep: browser-.*
```

---

#### `testMeta`

<p><small>| OPTIONAL | KEY-VALUE |</small></p>

Runs any tests whose metadata matches the specified key-value pairs. Accepts one or more key-value definitions. If multiple pairs are specified, matching tests must contain all of the specified metadata values.

```yaml
filter:
  testMeta:
    region: us-west-1
```

---

#### `fixtureMeta`

<p><small>| OPTIONAL | KEY-VALUE |</small></p>

Runs any tests included in fixtures whose metadata matches the specified key-value pairs. Accepts one or more key-value definitions. If multiple pairs are specified, matching fixtures must contain all of the specified metadata values.

```yaml
filter:
  fixtureMeta:
    env: staging
```

---

### `simulators`

<p><small>| OPTIONAL | OBJECT |</small></p>

The property containing details about on which simulators the tests in this suite will run. This property can include multiple device definitions.

```yaml
  simulators:
    - name: iPhone 12 Simulator
      platformName: iOS
      platformVersions:
        - "14.3"
```

---

### `screenshots`

<p><small>| OPTIONAL | OBJECT |</small></p>

A parent property containing the details about whether and how to handle screenshots for this test suite. [See Testcafe Documentation](https://testcafe.io/documentation/402638/reference/configuration-file#screenshots).

```yaml
  screenshots:
    takeOnFails: true
    fullPage: true
```

---

### `disableScreenshots`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Prevents TestCafe from taking screenshots. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#disablescreenshots).

```yaml
  disableScreenshots: true
```

---

### `speed`

<p><small>| OPTIONAL | FLOAT64 |</small></p>

Allows you to alter the test execution speed for the test suite. Tests are run at the maximum speed by default, but you can slow the test down by setting a value between `1` (the fastest) and `0.01` (the slowest).

```yaml
  speed: 0.1
```

---

### `tsConfigPath`

<p><small>| OPTIONAL | STRING |</small></p>

This field has been deprecated as of TestCafe v1.10.0. See [TestCafe Documentation](https://testcafe.io/documentation/402638/reference/configuration-file#tsconfigpath). Please refer to [compilerOptions](#compileroptions).

The absolute or relative path to the TypeScript configuration file. Relative paths are resolved against the current directory (the directory from which you run TestCafe).

```yaml
  tsConfigPath: /path/to/file
```

---

### `compilerOptions`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies test compilation settings. The current version of TestCafe can only configure the TypeScript compiler. See [TestCafe Documentation](https://testcafe.io/documentation/402638/reference/configuration-file#compileroptions).

```yaml
  compilerOptions:
    configPath: /path/to/tsconfig.json
    customCompilerModulePath: ../typescript@4
```

---

#### `configPath`

<p><small>| OPTIONAL | STRING |</small></p>

The absolute or relative path to the TypeScript configuration file. Relative paths are resolved against the current directory (the directory from which you run TestCafe).

```yaml
    configPath: /path/to/tsconfig.json
```

:::note
We recommend that you avoid the use of special characters when naming your config file. It may prevent TestCafe tests from launching.
:::

---

#### `customCompilerModulePath`

<p><small>| OPTIONAL | STRING |</small></p>

TestCafe ships with a `typescript@3` compiler. This field is for compiling your tests with a different compiler.

```yaml
    customCompilerModulePath: ../typescript@4
```

:::note
We recommend that you avoid the use of special characters when naming your config file. It may prevent TestCafe tests from launching.
:::

---

#### `options`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies the compiler options. See [TypeScript documentation](https://www.typescriptlang.org/docs/handbook/compiler-options.html).

```yaml
    options:
      showConfig: true
```

---

### `clientScripts`

<p><small>| OPTIONAL | STRING/ARRAY |</small></p>

A list of one or more filepath values for scripts to inject into all pages visited during the test. See [TestCafe definition](https://testcafe.io/documentation/402783/reference/test-api/fixture/clientscripts).

```yaml
  clientScripts: ["/path/to/file1", "/path/to/file2"]
```

---

### `skipJsErrors`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Determines whether to ignore JavaScript errors on a webpage. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#skipjserrors).

```yaml
  skipJsErrors: true
```

---

### `quarantineMode`

<p><small>| OPTIONAL | OBJECT |</small></p>

Determines whether to enable quarantine mode for tests that fail. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#quarantinemode).

```yaml
  quarantineMode:
    attemptLimit: 5
    successThreshold: 3
```

---

#### `attemptLimit`

<p><small>| OPTIONAL | INTEGER |</small></p>

The maximum number of test execution attempts. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#quarantinemodeattemptlimit).

```yaml
  quarantineMode:
    attemptLimit: 5
```

---

#### `successThreshold`

<p><small>| OPTIONAL | INTEGER |</small></p>

The number of successful attempts necessary to confirm a test’s success. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#quarantinemodesuccessthreshold).

```yaml
  quarantineMode:
    successThreshold: 3
```

---

### `skipUncaughtErrors`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Determines whether to ignores uncaught errors and unhandled promise rejections in test code. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#skipuncaughterrors).

```yaml
  skipUncaughtErrors: true
```

---

### `selectorTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Specifies the time (in milliseconds) within which selectors may attempt to return a node. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#selectortimeout).

```yaml
  selectorTimeout: 1000
```

---

### `assertionTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Specifies the time (in milliseconds) TestCafe may attempt to successfully execute an assertion if a selector property or a client function was passed as an actual value. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#assertiontimeout).

```yaml
  assertionTimeout: 1000
```

---

### `pageLoadTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Specifies the time (in milliseconds) passed after the `DOMContentLoaded` event, within which TestCafe waits for the `window.load` event to fire. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#pageloadtimeout).

```yaml
  pageLoadTimeout: 1000
```

---

### `ajaxRequestTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Specifies wait time (in milliseconds) for fetch/XHR requests. If TestCafe receives no response within the specified period, it throws an error. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#ajaxrequesttimeout).

```yaml
  ajaxRequestTimeout: 40000
```

---

### `pageRequestTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Specifies time (in milliseconds) to wait for HTML pages. If TestCafe does not receive a page within the specified period, it throws an error. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#pagerequesttimeout).

```yaml
  pageRequestTimeout: 8000
```

---

### `browserInitTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Time (in milliseconds) for browsers to connect to TestCafe and report that they are ready to test. If one or more browsers fail to connect within the specified period, TestCafe throws an error. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#browserinittimeout).

```yaml
  browserInitTimeout: 180000
```

---

### `testExecutionTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Maximum test execution time (in milliseconds). When the total execution time of a test exceeds this value, TestCafe terminates the test. This behavior occurs even if the browser is responsive. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#testexecutiontimeout).

```yaml
  testExecutionTimeout: 180000
```

---

### `runExecutionTimeout`

<p><small>| OPTIONAL | INTEGER |</small></p>

Maximum test run execution time (in milliseconds). When the total execution time of a run exceeds this value, TestCafe terminates the test run. This behavior occurs even if one of the tests or hooks is active. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#runexecutiontimeout).

```yaml
  runExecutionTimeout: 180000
```

---

### `stopOnFirstFail`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Determines whether to stop a test run if a test fails. See [TestCafe definition](https://testcafe.io/documentation/402638/reference/configuration-file#stoponfirstfail).

```yaml
  stopOnFirstFail: true
```

---

### `disablePageCaching`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

Determines whether to prevent the browser from caching page content. See [TestCafe definition](https://devexpress.github.io/testcafe/documentation/reference/configuration-file.html#disablePageCaching).

```yaml
  disablePageCaching: true
```

---

### `timeout`

<p><small>| OPTIONAL | DURATION |</small></p>

Instructs how long `saucectl` should wait for the suite to complete, potentially overriding the default project timeout setting.

When the suite reaches the timeout limit, its status is set to '?' in the CLI. This does not reflect the actual status of the job in the Sauce Labs web UI or API.

:::note
Setting `0` reverts to the value set in `defaults`.
:::

```yaml
  timeout: 15m
```

---

### `preExec`

<p><small>| OPTIONAL | STRING/ARRAY |</small></p>

Specifies which commands needs to be executed before the tests are actually started. The commands are executed from the root directory of your project.

:::note
There is a 300-second limit for all `preExec` commands to complete.
:::

```yaml
  preExec:
    - node ./scripts/pre-execution-script.js
```

---

### `timeZone`

<p><small>| OPTIONAL | STRING |</small></p>

Allows you to set a custom time zone for your test based on a city name. Most major cities are supported.

```yaml
  timeZone: New_York
```

---

### `passThreshold`

<p><small>| OPTIONAL | INTEGER |</small></p>

Specifies the minimum number of successful attempts for a suite to be considered as `passed`. It should be used along with [retries](#retries).

:::note
For example, setting `retries` to 3 and `passThreshold` to 2.
The max attempt would be 4 times. If the test passed twice, it'd stop and be marked as `passed`. Otherwise, it'd be marked as `failed`.
:::

```yaml
sauce:
  retries: 3
suite:
  - name: My Saucy Test
    passThreshold: 2
```

---

### `smartRetry`

<p><small>| OPTIONAL | OBJECT |</small></p>

Specifies the retry strategy to apply for that suite. Requires [retries](#retries) to be >= 1.

```yaml
sauce:
  retries: 3
suite:
  - name: My Saucy Test
    smartRetry:
      failedOnly: true
```

---

#### `failedOnly`

<p><small>| OPTIONAL | BOOLEAN |</small></p>

When set to `true`, only the tests that failed during the previous attempt are retried.

```yaml
suite:
  - name: My Saucy Test
    smartRetry:
      failedOnly: true
```
