---
id: cypress
title: Cypress on Sauce Labs
sidebar_label: Using Cypress
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
import useBaseUrl from '@docusaurus/useBaseUrl';

[Cypress](https://docs.cypress.io/guides/overview/why-cypress.html) is an end-to-end JavaScript testing framework that you can use to test your web apps remotely on Sauce Labs cloud using the [`saucectl` CLI](/dev/cli/saucectl).

## System Requirements

Supported OS:
- Windows 10 / Windows 11
- macOS 10.14+
- Linux

## Supported Languages

JavaScript is supported out of the box. TypeScript and Cucumber are also supported, but require additional dependencies at runtime. See our [example repo](https://github.com/saucelabs/saucectl-cypress-example/tree/main/v1/examples) for working end-to-end examples.

## Supported Testing Platforms

Sauce Labs supports the following test configurations for Cypress:

<table id="table-fw">
  <tr>
    <th>Cypress Version</th>
    <th>Supported Platforms</th>
    <th>Supported Browsers</th>
    <th>End of Life</th>
  </tr>
  <tbody>
    <tr>
      <td rowspan='2'>12.11.0</td>
      <td><b>macOS:</b> 11.00, 12, 13</td>
      <td rowspan='2'>Chrome, Firefox, Microsoft Edge, Webkit (Experimental)</td>
      <td rowspan='2'>May 11, 2024</td>
    </tr>
    <tr>
      <td><b>Windows:</b> 10, 11</td>
    </tr>
  </tbody>
  <tbody>
    <tr>
      <td rowspan='2'>12.6.0</td>
      <td><b>macOS:</b> 11.00, 12, 13</td>
      <td rowspan='2'>Chrome, Firefox, Microsoft Edge, Webkit (Experimental)</td>
      <td rowspan='2'>Mar 01, 2024</td>
    </tr>
    <tr>
      <td><b>Windows:</b> 10, 11</td>
    </tr>
  </tbody>
  <tbody>
    <tr>
      <td rowspan='2'>12.3.0</td>
      <td><b>macOS:</b> 11.00, 12, 13</td>
      <td rowspan='2'>Chrome, Firefox, Microsoft Edge, Webkit (Experimental)</td>
      <td rowspan='2'>Jan 15, 2024</td>
    </tr>
    <tr>
      <td><b>Windows:</b> 10, 11</td>
    </tr>
  </tbody>
  <tbody>
    <tr>
      <td rowspan='2'>11.2.0</td>
      <td><b>macOS:</b> 11.00, 12, 13</td>
      <td rowspan='2'>Chrome, Firefox, Microsoft Edge, Webkit (Experimental)</td>
      <td rowspan='2'>Nov 30, 2023</td>
    </tr>
    <tr>
      <td><b>Windows:</b> 10, 11</td>
    </tr>
  </tbody>
  <tbody>
    <tr>
      <td rowspan='2'>10.10.0</td>
      <td><b>macOS:</b> 11.00, 12, 13</td>
      <td rowspan='2'>Chrome, Firefox, Microsoft Edge, Webkit (Experimental)</td>
      <td rowspan='2'>Oct 20, 2023</td>
    </tr>
    <tr>
      <td><b>Windows:</b> 10, 11</td>
    </tr>
  </tbody>
  <tbody>
    <tr>
      <td rowspan='2'>10.7.0</td>
      <td><b>macOS:</b> 11.00, 12, 13</td>
      <td rowspan='2'>Chrome, Firefox, Microsoft Edge</td>
      <td rowspan='2'>Sep 7, 2023</td>
    </tr>
    <tr>
      <td><b>Windows:</b> 10, 11</td>
    </tr>
  </tbody>
  <tbody>
  <tr>
    <td rowspan='2'>10.3.1</td>
    <td><b>macOS:</b> 11.00, 12, 13</td>
    <td rowspan='2'>Chrome, Firefox, Microsoft Edge</td>
    <td rowspan='2'>Jul 29, 2023</td>
  </tr>
  <tr>
    <td><b>Windows:</b> 10, 11</td>
  </tr>
  </tbody>
  <tbody>
  <tr>
    <td rowspan='2'>9.7.0</td>
    <td><b>macOS:</b> 11.00, 12, 13</td>
    <td rowspan='2'>Chrome, Firefox, Microsoft Edge</td>
    <td rowspan='2'>Jun 6, 2023</td>
  </tr>
  <tr>
    <td><b>Windows:</b> 10, 11</td>
  </tr>
  </tbody>
  <tbody>
  <tr>
    <td rowspan='1'>8.6.0</td>
    <td><b>Windows:</b> 10</td>
    <td>Chrome, Firefox, Microsoft Edge</td>
    <td rowspan='2'>Oct 13, 2022</td>
  </tr>
  </tbody>
</table>

## How to Get Started

- [Quickstart](/web-apps/automated-testing/cypress/quickstart): Use our demo repo to quickly set up and run a sample Cypress project and test to see the results.
- [Run your own tests](/web-apps/automated-testing/cypress/yaml): Customize `saucectl` to run your existing tests just by modifying the `config.yml` file for your project.
- Try Cypress with Cucumber: `saucectl` supports Cypress using Cucumber, and the demo repo includes examples for [Cypress 9 and below](https://github.com/saucelabs/saucectl-cypress-example/tree/main/v1alpha/examples/cucumber) and [Cypress 10 and above](https://github.com/saucelabs/saucectl-cypress-example/tree/main/v1/examples/cucumber).
- [Incorporate saucectl in your pipeline](/dev/cli/saucectl/usage/use-cases/#integrating-saucectl-in-your-ci-pipeline): Cypress on Sauce supports CI integrations with Circle CI, GitLab, Jenkins, and GitHub Actions.

## Cypress Plugin for Sauce Labs

If you would prefer to stay in Cypress, try the new [Cypress Sauce Labs Plugin](https://github.com/saucelabs/sauce-cypress-plugin). Connect to your Sauce Labs account from within your Cypress project to configure and run your tests directly from Cypress.

## Limitations

:::caution Special Characters in Test Names
We recommend that you avoid the use of special characters when naming your tests. If your test name contains any special characters, your test may not run or its artifacts may not be visible in our platform.
:::

:::caution Cypress 11+
A bug impacting Cypress 11+ is causing the Cypress UI to display a browser that is not the one actually used.
Edge is shown on Windows, and Webkit is shown on macOS.

The browser selected in your configuration file is used, and the console log of Cypress is still correct.

The issue has been reported to Cypress.
See: https://github.com/cypress-io/cypress/issues/25755 for more information.
:::

:::caution Firefox 101 + Windows
Cypress does not currently work with Firefox 101 on Windows.
:::

:::caution Firefox 105
Cypress does not currently work with Firefox 105.
See https://github.com/cypress-io/cypress/issues/23897 for more information.
:::

:::caution Webkit
Cypress 12.6.0 does not work on Windows with Webkit browser.

Cypress 12+ does not work on macOS 11 with Webkit browser.
:::
