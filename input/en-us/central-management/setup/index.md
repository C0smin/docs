---
Order: 20
xref: ccm-setup
Title: Setup
Description: Information on how to setup CCM
RedirectFrom: docs/central-management-setup
---

Installing CCM takes a little more pre-thought than simply running the package installs.
While it is envisioned that CCM will be installed across multiple servers (split installation), it is certainly possible to run CCM on a single server (monolithic).

When setting up Central Management, currently, the CCM packages do not provision the SQL Server Database Permissions that are required for the CCM components to function.  It is assumed that the necessary permissions have already been provided (see the [FAQ](#how-can-i-add-sql-server-permissions-through-powershell) for one method of doing it).

> :warning: **WARNINGS**
>
> * Unless otherwise noted, please follow these steps in **exact** order. These steps build on each other and need to be completed in order.
>
> * All deployed components of the CCM packages should **always** be the **SAME VERSION**. The only time you should not have this is when you are in a state of upgrading and that transition time should be quite short.
>

> :memo: **NOTES**
>
> * Please read through all of this prior to running installation as you could run into issues that require support to help you correct later.
>
> * If this seems like a lot to set up, you have access to the [Quick Start Guide](xref:c4b-quick-start-guide) which makes setup of Chocolatey Central Management just running one Powershell script. We also offer our [Chocolatey For Business Azure Environment](xref:qdeazure). It comes preloaded with Chocolatey Central Management and other Chocolatey recommended infrastructure.
>

## High Level Requirements

Central Management packages require at a minimum:

* Chocolatey for Business (C4B) Edition
* Windows Server 2012+

Each package further defines dependencies that they include.

Additionally, the Chocolatey Central Management components should be installed onto servers having a static IP address assigned, in addition to an appropriately configured DNS A record. Failure to ensure this configuration is applied could lead to unexpected errors with agent check-in and deployment operations.

## Step 1: Internalize Packages

> :memo: **NOTE**
>
> Make sure you have read over the [CCM Compatibility Matrix](xref:central-management#ccm-component-compatibility-matrix) prior to starting internalization as this will save you some headaches.

The complete installation of CCM requires several packages that are available from the community repository. Let's get them internalized. We will internalize them to a `C:\packages` directory. It is highly recommended that you push the packages to an internal repository before continuing with other steps in this guide. Change the values in the first lines of this script to match what you need in your environment.

```powershell
# To run this, you need Chocolatey for Business installed (chocolatey / chocolatey.extension).

# Update the values and remove the < >
$YourInternalRepositoryPushUrl = '<INSERT REPOSITORY URL HERE>'
$YourInternalRepositoryApiKey = '<YOUR API KEY HERE>'
# You get this from the chocolatey.license.xml file:
$YourBusinessLicenseGuid = '<INSERT C4B LICENSE GUID HERE>'

if(!(Test-Path C:\packages)){
  $null = New-Item C:\packages -ItemType Directory
}

# Download Chocolatey community related items, no internalization necessary
choco download chocolatey chocolateygui --force --source="'https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"

# This is for other Community Related Items
choco download dotnet4.5.2 dotnetfx --force --internalize --internalize-all-urls --append-use-original-location --source="'https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"


# This is for SQL Server Express
# Not necessary if you already have SQL Server
@('sql-server-express','sql-server-management-studio') | Foreach-Object {
  choco download $_ --force --internalize --internalize-all-urls --append-use-original-location --source="'https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"
}

# We must use the 3.x.x versions of these packages, so we need to download/internalize these specific items.  At the time of publishing, the most recent version of this package is 3.1.16, but later package versions (within the 3.x.x release) are expected to work.
@('aspnetcore-runtimepackagestore','dotnetcore-windowshosting') | Foreach-Object {
  choco download $_ --version 3.1.16 --force --internalize --internalize-all-urls --append-use-original-location --source="'https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"
}

# Starting with v0.6.2 of the CCM Database package, it uses dotnetcore-sdk so that it may be installed on a system without requiring IIS. At the time of publishing, the most recent version of this package is 3.1.410, but later package versions (within the 3.x.x release) are expected to work
choco download dotnetcore-sdk --version 3.1.410 --force --internalize --internalize-all-urls --append-use-original-location --source="'https://community.chocolatey.org/api/v2/'" --output-directory="'C:\packages'"

# Download Licensed Packages
## DO NOT RUN WITH `--internalize` and `--internalize-all-urls` - see https://github.com/chocolatey/chocolatey-licensed-issues/issues/155
choco download chocolatey-agent chocolatey.extension chocolatey-management-database chocolatey-management-service chocolatey-management-web --force --source="'https://licensedpackages.chocolatey.org/api/v2/'" --ignore-dependencies --output-directory="'C:\packages'"  --user="'user'" --password="'$YourBusinessLicenseGuid'"

# Push all downloaded packages to your internal repository
Get-ChildItem C:\packages -Recurse -Filter *.nupkg | Foreach-Object { choco push $_.Fullname --source="'$YourInternalRepositoryPushUrl'" --api-key="'$YourInternalRepositoryApiKey'"}
```

## Step 2: Setup Central Management Database

Please see [Central Management Database Setup](xref:ccm-database).

> :memo: **NOTE** While we'd like to support different database engines at some point in the distant future, currently only SQL Server is supported.

## Step 3: Setup Central Management Windows Service(s)

Please see [Central Management Service Setup](xref:ccm-service).

> :memo: **NOTE** If Step 1 is not succesful, do not move on to this step until you resolve issues with database setup.

## Step 4: Setup Central Management Website

Please see [Central Management Web Setup](xref:ccm-website).

> :memo: **NOTE** If Step 1 or 2 is not successful, do not move on to this step until you resolve issues with previous steps.

## Step 5: Setting up Agent Machines

Please see [Central Management Client Setup](xref:ccm-client).

## Upgrading?

Looking for upgrade instructions? See [Central Management Upgrade](xref:ccm-upgrade).

## Common Errors and Resolutions

### Executable script code found in signature block

When attempting to install some components of Chocolatey, you may have seen this error. This was a bug due to how the script at [Step 1: Internalize Packages](#step-1-internalize-packages) was [internalizing packages](https://github.com/chocolatey/chocolatey-licensed-issues/issues/155).

Please go back through Step 1 and re-internalize those packages. You may need to overwrite any you would have pushed up (many if it won't let you do a push). In Nexus, you can remove the existing items and then upload through there. In other repositories you may need to remove the existing package versions you deployed first.

### The client reports successful check-in, but nothing is showing up in CCM

You need to check the CCM service logs. The agent will always report success when it communicates with the service successfully. The service may reject what it receives, but due to security settings, it won't tell the client about that.

The logs are located at `$env:ChocolateyInstall\logs\ccm-service.log`. If you are on a version of CCM prior to 0.2.0, the log will be located at `$env:ChocolateyInstall\lib\chocolatey-management-service\tools\service\logs\chocolatey.service.host.log`.

[Chocolatey Central Management](xref:central-management)
