# Decentralized scan container Action

This action enables you to integrate vulnerability scans into your CI/CD pipeline. A decentralized scan container runs Burp Scanner from a Docker container on a CI/CD platform agent node. 

On completion, it generates a JUnit XML report about the vulnerabilities found. This report includes:
* The locations of the vulnerabilities
* Additional information about each vulnerability 
* Links to our learning resources, with remediation advice


In order to use this action you need an installation of Burp Suite Enterprise Edition. You need to create an API user with the External Scan Initiator role and provide the API key to this action. To learn more, see [Creating API users](https://portswigger.net/burp/documentation/enterprise/api-documentation/create-api-user.html).

## About decentralized scan containers
* Easily integrate Burp Suite Enterprise Edition with your CI/CD pipeline
* Run Burp Scanner directly from your CI/CD platform
* Supports [login details and recorded login sequences](https://portswigger.net/burp/documentation/enterprise/working-with-sites/configure-logins/)

For full documentation about decentralized scan containers, please see [Integrating decentralized scan containers](https://portswigger.net/burp/documentation/enterprise/integrate-ci-cd-platforms/decentralized-scan-containers/index.html).


## Inputs

Inputs can be used to provide values to the container. If you use a configuration file, the values in that file have priority over the input values.

### `enterprise-server-url`

The URL of your Enterprise server.

### `enterprise-api-key`

The API key for your API user.

### `start-url`

The URL of the website you want Burp Scanner to scan.

**The above values must be specified either as inputs or in a configuration file.**

### `correlation-id`

**Optional** You only need to input a correlation ID if you want to view the scan results on the Burp Suite Enterprise Edition web interface. You can use a text string up to 64 
characters long. Scans with the same correlation ID will be treated as the same site in the web interface.

### `report-file-path`

**Optional** The output path for the scan report. This is relative to the working directory of the container.

**Default** `burp_junit_report.xml`

### `config-file-path`

**Optional** The path to the configuration file. This path must be an absolute path.

**Default** `burp_config.yml` in the container's working directory.

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

The scan container produces a JUnit XML report when the scan completes. This report only includes vulnerability details if vulnerabilities are found by 
Burp Scanner. By default, the report is saved as `burp_junit_report.xml` in the container's working directory.

### Advanced Usage

You can use a configuration file to set more advanced options.

Create a configuration file and save it as `burp_config.yml` in the root of your repository. To learn more, see https://portswigger.net/burp/documentation/enterprise/integrate-ci-cd-platforms/decentralized-scan-containers/create-config.html

Make sure you include the server URL, API key, and at least one Start URL.

```
steps:
  - name: Checkout
    uses: actions/checkout@v3

  - name: Run Enterprise Scan Action Step
    uses: PortSwigger/decentralized-scan-github-action@main

  # You can replace this next step with any JUnit XML parser.
  - name: Publish Test Report
    if: always()                                    # Forces this step to always run
    uses: mikepenz/action-junit-report@v3
    with:
      report_paths: 'burp_junit_report.xml'
      fail_on_failure: false
      require_tests: false
```

By default, if the scanner finds any issue with a severity level of `LOW` or above, it fails the workflow build (the scan container exits with a non-zero exit code).

You can edit your configuration file to change the threshold for exiting with a non-zero exit code.


