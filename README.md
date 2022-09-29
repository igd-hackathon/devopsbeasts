
                              ****Implementing Policy as Code to Prevent Infrastructure Vulnerabilities****


* Implementing  DevSecOps for Infrastructure Security by Policy as Code
* Building secure Infrastructure in proactive manner by automation way its more efficiency and fast
* Policy as Code is reusable framework and can be implemented as centralized manner throughout organization in git (https://github.com/igd-hackathon/policyascode)
* Policy as Code can be written by Python, Yaml and Rego languages
* We can easily customize polices for each environment as per organization requirements
* Using Open Policy Agent, we can achieve 500+  individual policies by using Rego language for  AWS, Azure, GCP, Kubernetes and Docker



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

Terrascan can be integrated into CI/CD pipelines to enforce security best practices in Github Actions, Bamboo, Codefresh and more CI Tools

**## Intergated Terrascan with Github Actions using Centailised Repo**

```
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

```


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


## DevOps Beast Authors

Rajasekar Jayaprakash
Ravishankar Rangaraj
Madhu A G
Harikrishna
