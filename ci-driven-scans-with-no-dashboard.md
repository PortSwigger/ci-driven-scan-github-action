# CI-driven scans with no dashboard
This action enables you to integrate vulnerability scans into your CI/CD pipeline. A CI-driven scan runs Burp Scanner from a Docker container on a CI/CD platform agent node.
On completion, it generates a JUnit XML report about the vulnerabilities found. This report includes:
* The locations of the vulnerabilities
* Additional information about each vulnerability
* Links to our learning resources, with remediation advice

## About CI-driven scans with no dashboard
* Easily integrate Burp Suite Enterprise Edition with your CI/CD pipeline
* Run Burp Scanner directly from your CI/CD platform
* Supports [login details and recorded login sequences](https://portswigger.net/burp/documentation/enterprise/working-with-sites/configure-logins/)

For full documentation about these types of scan, please see [Integrating CI-driven scans with no dashboard](https://portswigger.net/burp/documentation/enterprise/integrate-ci-cd-platforms/ci-driven-nodash).

## Inputs
Inputs can be used to provide values to the container. If you use a configuration file, the values in that file have priority over the input values.

### `enterprise-server-url`

The URL supplied with your PortSwigger account

### `enterprise-api-key`

The API key supplied with your PortSwigger account

### `start-url`

The URL of the website you want Burp Scanner to scan.

**The above values must be specified either as inputs or in a configuration file.**

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
  - name: Run CI-driven scan Action Step
    uses: PortSwigger/ci-driven-scan-github-action@main
    with:
      enterprise-server-url: <the-url-supplied-with-your-portswigger-account>
      enterprise-api-key: <the-api-key-supplied-with-your-portswigger-account>
      start-url: 'https://ginandjuice.shop'
```

The scan container produces a JUnit XML report when the scan completes. This report only includes vulnerability details if vulnerabilities are found by 
Burp Scanner. By default, the report is saved as `burp_junit_report.xml` in the container's working directory.

### Advanced Usage
You can use a configuration file to set more advanced options.
Create a configuration file and save it as burp_config.yml in the root of your repository. To learn more, see https://portswigger.net/burp/documentation/enterprise/integrate-ci-cd-platforms/ci-driven-nodash/create-config

Make sure you include the URL, and API key for your license, and at least one Start URL.

```
steps:
  - name: Checkout
    uses: actions/checkout@v3

  - name: Run CI-driven scan Action Step
    uses: PortSwigger/ci-driven-scan-github-action@main

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