# Enterprise Scan Action

This action runs a vulnerability scan against a target site. On completion, a JUnit XML report is generated containing information about the vulnerabilities found, where they were 
located, additional information about the vulnerability and links to our learning resources with suggestions on how to fix them.

In order to use this action you will require an installation of Burp Suite Enterprise, configured with an API Key

## Inputs

Inputs can be used to provide values to the container. If a custom configuration file is provided, the values in that file will be
used in preference to the input values.

### `enterprise-server-url`

**Optional** The URL of your Enterprise server.

### `enterprise-api-key`

**Optional** The API key that you copied when you created an API user.

### `start-url`

**Optional** The URL of the website you want Burp Scanner to scan.

### `correlation-id`

**Optional** You only need to input a correlation ID if you want to view the scan results on the Burp Suite Enterprise Edition web interface. You can use a text string up to 64 
characters long.

### `report-file-path`

**Optional** The filename used for the scan report. This filepath relates to the enterprise scan container.

**Default** `burp_junit_report.xml`

## Examples

Below are some examples of how to use the action to run a scan against our very own [Gin and Juice Shop](https://ginandjuice.shop) site. This is a deliberately
vulnerable web application designed for testing web vulnerability scanners.

### Basic Usage

```
steps:
  - name: Run Enterprise Scan Action Step
    uses: PortSwigger/decentralized-scan-github-action@main
    with:
      enterprise-server-url: <your-enterprise-installation-url>
      enterprise-api-key: <your-enterprise-installation-api-key>
      start-url: 'https://ginandjuice.shop'
```

The Enterprise Scan container produces a JUnit XML report of the scan on completion. This report will only include vulnerability details if vulnerabilities were found by the 
scanner. By default, the report will be written to `burp_junit_report.xml` in the working directory.

## Suggested Usage

By default, if the scanner finds any issue with a severity level of `LOW` or above, it will fail a workflow build (the scan container will exit with a non-zero exit code).

The threshold mentioned above, along with other configuration options, can be set by creating a file named `burp_config.xml` in the working directory. An 
[example configuration template](https://github.com/PortSwigger/decentralized-scan-container-examples) is available for information.
