# isaac-scanner
Documentation and scripts for the ISAAC scanner, a compact images scanner to perform analysis on infrastructure-as-code.

Supported tools and platforms:
- Terraform
- Cloudformation 
- Kubernetes/Helm
- ARM templates / Bicep

ISAAC comes with a curated set of policies that cover not only security but also compliance and best practices. It also detects sensitive information that may be present in the code, such as credentials, authorisation tokens, and various forms of keys.

## Prerequisites
- [Docker](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-using-the-convenience-script) (v1.12.0+)

## How to use

### Installation
Download the `isaac` script from here [here](https://github.com/MeterianHQ/isaac-scanner/blob/main/isaac) and make it executable. A later invocation of the script will ensure, through Docker, that the scanner image [`meterian/isaac:latest`](https://hub.docker.com/r/meterian/cs-engine) is pulled. Subsequent invocations of the script will not ensure this, so newer versions of the image can be accessed through docker pull. 


### Operation
The scanner is fully integrated with the [Meterian Dashboard](https://www.meterian.com/dashboard/): every scan will be recorded and managed there.

### Executing a scan

You can perform a scan by simply moving in the folder where the IAC code is and run the script:

    $ isaac 

Note that to perform a scan a valid Meterian API token must be set as environment variable on your system. To gain a token, create one from your account at https://meterian.com/account/#tokens

Once you have a token populate a `METERIAN_API_TOKEN` environment variable with its value as shown below

```bash
export METERIAN_API_TOKEN=12345678-90ab-cdef-1234-567890abcdef
```

### Producing reports
At the moment you can generate the following reports with `isaac` by specifying the appropriate flag at command line invocation

`--report-json`</br>
Produces a JSON report file of the analysis</br>
Example: `--report-json=report.json`

`--report-pdf`</br>
Produces a PDF report file of the analysis</br>
Example: `--report-pdf=report.pdf`

`--report-sbom`</br>
Produces a SBOM report, the format is defined based on the extension of the file:
- .json - a standard licensing bible report, JSON format
- .pdf - a standard licensing bible report, PDF format
- .csv - a standard SBOM Meterian report, CSV format
- .cdx.xml - a standard CycloneDX SBOM report, XML format
- .cdx.json - a standard CycloneDX SBOM report, JSON format

Example: `--report-sbom=~/sbom.csv`
 
`--report-console`</br>
Emits  an analysis report on the console. By default this report will show colours if the target console supports it. Alternatively you can enforce the report to be emitted with colours or without with switches `color` or `nocolor`</br>
Example: `--report-console`, `--report-console:color`, `--report-console:nocolor`

### Local policy exclusions
Policy violations can be excluded directly from the report page on meterian.io. Should you need to exclude policies locally you can create a `.isaacignore` file in the root folder of your project containing the ID(s) in question.

Here's an example showing the contents of this exclusion file.
```
# No impact in our settings
ISA_C220

# Ignore the misconfiguration
ISA_C208

ISA_C1070
ISA_C198
```
**Note:** a comment preceding policy IDs is then reported in the report

Policy IDs can be found in the output of the console report (generated by invoking isaac with flag `--report-console`).

## Linux users
As the above offerings use Docker, here are some considerations to ensure they function properly.

### Option A - giving access to the docker socket
You can ensure that you can use Docker as non-root user by running this command:

```bash
    sudo setfacl --modify user:<user name or ID>:rw /var/run/docker.sock
```

Please note that this is effective only until the machine is restarted. **After a restart you will have to re-issue the command.**

### Option B - using a custom 'docker' group for your docker enabled users

Create the docker group.
```bash
sudo groupadd docker
```

Add your user to the docker group.
```bash
sudo usermod -aG docker $USER
```

Log out and log back in so that your group membership is re-evaluated. **This setup will be permanent and will work across restarts.**


## Opensource scanners used:

This product uses these opensource scanners:
- [Tfsec](https://github.com/aquasecurity/tfsec) (MIT License - Copyright (c) 2019 Liam Galvin)
- [Checkov](https://github.com/bridgecrewio/checkov) (Apache-2 License - Copyright 2019 Bridgecrew)
