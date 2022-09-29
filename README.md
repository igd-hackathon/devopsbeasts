
                              ****Implementing Policy as Code to Prevent Infrastructure Vulnerabilities****

## Introduction

DevSecOps boomed in recent days by implementing various tools in CI/CD process for SAST/DAST and RASP. Most of the organization focuses on DevSecOps tool to identify vulnerabilities and run time attack on applications, still organizations are facing security attacks over cloud as well. Most of the Big organizations hire security advisors and skilled professionals for implementing security and compliance. In case of smaller organizations they fail to implement those high standard security compliance policies. Nowadays all the security can be made as policy as code. With emerging technologies Infrastructure Operations can be performed by DevOps itself, during that time policy as code can help build high security infrastructure. Though organizations use Infrastructure as Code for implementing security standards for their cloud subscriptions, Introducing Policy as Code concept in Infrastructure as Code, as a standard checklist will perform the security measures based on the policies that we configured and it will be applied uniformly for all the subscriptions that the organization opted for. We have open source and enterprise tools growing faster which implements Policy as Code standard and is very effective in identifying the security issues and vulnerabilities in Infrastructure as Code. By configuring and implementing the above Policy as Code tools via CI/CD, Git hooks in a centralized manner will help prevent the security issues and make the organizations infrastructure free from vulnerabilities and security attacks.



## Key features
* Keep policies in Centrailised repo (https://github.com/igd-hackathon/policyascode)
* 500+ Policies for security best practices.
* Scanning of Terraform, AWS CloudFormation Templates (CFT), Azure Resource Manager (ARM),Kubernetes (Helm).
* Integrates with docker image vulnerability scanning for AWS, Azure, GCP, Harbor container registries.

## Quick Start

1. [Install](#install)
2. [Scan](#scan)
3. [Integrate](#integrate)

### Step 1: Install
Terrascan supports multiple ways to install and is also available as a Docker image.
See Terrascan's [releases](https://github.com/tenable/terrascan/releases) page for the latest version of builds in all supported platforms. Select the correct binary for your platform.

#### Install as a native executable

```sh
$ curl -L "$(curl -s https://api.github.com/repos/tenable/terrascan/releases/latest | grep -o -E "https://.+?_Darwin_x86_64.tar.gz")" > terrascan.tar.gz
$ tar -xf terrascan.tar.gz terrascan && rm terrascan.tar.gz
$ install terrascan /usr/local/bin && rm terrascan
$ terrascan
```

#### Install on ArchLinux / Manjaro via `AUR`

ArchLinux and Manjaro users can install by:

```
yay -S terrascan
```

#### Install via `brew`

[Homebrew](https://brew.sh/) users can install by:

```sh
$ brew install terrascan
```

#### Docker image

Terrascan is also available as a Docker image and can be used as follows

```sh
$ docker run tenable/terrascan
```
Refer to [documentation](https://runterrascan.io/docs/getting-started/) for information.

### Step 2: Scan
To scan your code for security issues you can run the following (defaults to scanning Terraform).

```sh
$ terrascan scan
```
**Note**: Terrascan will exit with an error code if any errors or violations are found during a scan.

#### List of possible Exit Codes
| Scenario      | Exit Code |
| ----------- | ----------- |
| scan summary has errors and violations | 5 |
| scan summary has errors but no violations | 4 |
| scan summary has violations but no errors | 3 |
| scan summary has no violations or errors | 0 |
| scan command errors out due to invalid inputs | 1 |
### Step 3: Integrate with CI\CD

Terrascan can be integrated into CI/CD pipelines to enforce security best practices.
Please refer to our [documentation to integrate with your pipeline](https://runterrascan.io/docs/integrations/).

## Terrascan Commands
You can use the `terrascan` command with the following options:

```sh
$ terrascan
Terrascan

Usage:
  terrascan [command]

Available Commands:
  help        Help about any command
  init        Initialize Terrascan
  scan        Detect compliance and security violations across Infrastructure as Code.
  server      Run Terrascan as an API server
  version     Terrascan version

Flags:
  -c, --config-path string   config file path
  -h, --help                 help for terrascan
  -l, --log-level string     log level (debug, info, warn, error, panic, fatal) (default "info")
  -x, --log-type string      log output type (console, json) (default "console")
  -o, --output string        output type (human, json, yaml, xml) (default "human")

Use "terrascan [command] --help" for more information about a command.
```

## Policies
Terrascan policies are written using the [Rego policy language](https://www.openpolicyagent.org/docs/latest/policy-language/). Every rego includes a JSON "rule" file which defines metadata for the policy.
By default, Terrascan downloads policies from Terrascan repositories while scanning for the first time. However, if you want to download the latest policies, you need to run the Initialization process. See [Usage](https://runterrascan.io/docs/usage/command_line_mode/) for information about the Initialization process.

Note: The scan command will implicitly run the initialization process if there are no policies found.

## Docker Image Vulnerabilities
You can use the `--find-vuln` flag to collect vulnerabilities as reported in its registry as part of Terrascan's output. Currently Terrascan supports Elastic Container Registry (ECR), Azure Container Registry, Google Container Registry, and Google Artifact Registry.

The `--find-vuln` flag can be used when scanning IaC files as follows:

```
$ terrascan scan -i <IaC provider> --find-vuln
```
```
**## Intergated Terrascan with Github Actions using Centailised Repo**

on: [push]

jobs:

  terrascan-docker:
    runs-on: ubuntu-latest
    name: terrascan-action-docker
    steps:
    - name: Checkout repository
      uses: actions/checkout@v2
    - name: Check out my policy repo
      uses: actions/checkout@main
      with:
        repository: igd-hackathon/policyascode
        path: ./policyascode
    - name: Scan docker custom
      id: terrascan-k8s
      uses: tenable/terrascan-action@main
      with:
        iac_type: 'docker'
        iac_version: 'v1'
        policy_type: 'docker'
        only_warn: true
        sarif_upload: true
        iac_dir: 'local-policy/'
        policy_path: 'policyascode/'
        
    - name: Upload SARIF file
      uses: github/codeql-action/upload-sarif@v2
      with:
        sarif_file: terrascan.sarif


