﻿---
Order: 190
xref: choco-command-uninstall
Title: Uninstall
Description: Uninstall Command (choco uninstall)
RedirectFrom:
  - docs/commandsuninstall
  - docs/commands-uninstall
---

<!-- This file is automatically generated based on output from https://github.com/chocolatey/choco/blob/master/src/chocolatey/infrastructure.app/commands/ChocolateyUninstallCommand.cs using https://github.com/chocolatey/choco/blob/master/GenerateDocs.ps1. Contributions are welcome at the original location(s). If the file is not found, it is not part of the open source edition of Chocolatey or the name of the file is different. --> 

> :memo: **WARNING** SHIM DEPRECATION
>
> With the release of Chocolatey CLI v1.0.0 we have deprecated the following shims/shortcuts:
>
> - chocolatey (Alias for choco)
> - cinst (Shortcut for choco install)
> - cpush (Shortcut for choco push)
> - cuninst (Shortcut for cuninst)
> - cup (Shortcut for choco upgrade)
>
> We recommend that any scripts calling these shims be updated to use the full command, as
> these shims will be removed in Chocolatey CLI v2.0.0.

# Uninstall Command (choco uninstall)

Uninstalls a package or a list of packages.

> :memo: **NOTE** 100% compatible with older chocolatey client (0.9.8.32 and below)
 with options and switches. Add `-y` for previous behavior with no
 prompt. In most cases you can still pass options and switches with one
 dash (`-`). For more details, see [how to pass arguments](xref:choco-commands#how-to-pass-options-switches) (`choco -?`).

Choco 0.9.9+ automatically tracks registry changes for "Programs and
 Features" of the underlying software's native installers when
 installing packages. The "Automatic Uninstaller" (auto uninstaller)
 service is a feature that can use that information to automatically
 determine how to uninstall these natively installed applications. This
 means that a package may not need an explicit chocolateyUninstall.ps1
 to reverse the installation done in the install script.

Chocolatey tracks packages, which are the files in
 `$env:ChocolateyInstall\lib\packagename`. These packages may or may not
 contain the software (applications/tools) that each package represents.
 The software may actually be installed in Program Files (most native
 installers will install the software there) or elsewhere on the
 machine.

With auto uninstaller turned off, a chocolateyUninstall.ps1 is required
 to perform uninstall from the system. In the absence of
 chocolateyUninstall.ps1, choco uninstall only removes the package from
 Chocolatey but does not remove the software from your system (unless
 in the package directory).

> :memo: **NOTE** A package with a failing uninstall can be removed with the
`-n --skipautouninstaller` flags. This will remove the package from
chocolatey without attempting to uninstall the program.

> :memo: **NOTE** Starting in 0.9.10+, the Automatic Uninstaller (AutoUninstaller)
 is turned on by default. To turn it off, run the following command:

    choco feature disable -n autoUninstaller

> :memo: **NOTE** [Chocolatey Pro](https://chocolatey.org/compare) / Business automatically synchronizes with
 Programs and Features, ensuring manually removed apps are
 automatically removed from Chocolatey's repository.

> :memo: **NOTE** Synchronizer and AutoUninstaller enhancements in licensed
 versions of Chocolatey ensure that Autouninstaller is up to 95%
 effective at removing software without an uninstall script. This is
 because synchronizer ensures the registry snapshot stays up to date
 and licensed enhancements have the ability to inspect more locations
 to determine how to automatically uninstall software.

## DEPRECATION NOTICE

Starting in v2.0.0 the shortcut `cuninst` will be removed and can not be used
to uninstall packages anymore. We recommend you make sure that you always
use the full command going forward ([`choco uninstall`](xref:choco-command-uninstall)).

## Usage

    choco uninstall <pkg|all> [pkg2 pkgN] [options/switches]
    cuninst <pkg|all> [pkg2 pkgN] [options/switches] (DEPRECATED, will be removed in v2.0.0)

> :memo: **NOTE** `all` is a special package keyword that will allow you to
 uninstall all packages.


## See It In Action

![choco uninstall](/assets/images/gifs/choco_uninstall.gif)


## Examples

    choco uninstall git
    choco uninstall notepadplusplus googlechrome atom 7zip
    choco uninstall notepadplusplus googlechrome atom 7zip -dv
    choco uninstall ruby --version 1.8.7.37402
    choco uninstall nodejs.install --all-versions

> :memo: **NOTE** See scripting in [how to pass arguments](xref:choco-commands#how-to-pass-options-switches) (`choco -?`) for how to 
 write proper scripts and integrations.


## Exit Codes

Exit codes that normally result from running this command.

Normal:
 - 0: operation was successful, no issues detected
 - -1 or 1: an error has occurred

Package Exit Codes:
 - 1605: software is not installed
 - 1614: product is uninstalled
 - 1641: success, reboot initiated
 - 3010: success, reboot required
 - other (not listed): likely an error has occurred

In addition to normal exit codes, packages are allowed to exit
 with their own codes when the feature 'usePackageExitCodes' is
 turned on. Available in v0.9.10+.

Reboot Exit Codes:
 - 350: pending reboot detected, no action has occurred
 - 1604: install suspended, incomplete

In addition to the above exit codes, you may also see reboot exit codes
 when the feature 'exitOnRebootDetected' is turned on. It typically requires
 the feature 'usePackageExitCodes' to also be turned on to work properly.
 Available in v0.10.12+.

## Options and Switches

> :memo: **NOTE** Options and switches apply to all items passed, so if you are
 running a command like install that allows installing multiple
 packages, and you use `--version=1.0.0`, it is going to look for and
 try to install version 1.0.0 of every package passed. So please split
 out multiple package calls when wanting to pass specific options.

Includes [default options/switches](xref:choco-commands#default-options-and-switches) (included below for completeness).

~~~



 -?, --help, -h
     Prints out the help menu.

 -d, --debug
     Debug - Show debug messaging.

 -v, --verbose
     Verbose - Show verbose messaging. Very verbose messaging, avoid using 
       under normal circumstances.

     --trace
     Trace - Show trace messaging. Very, very verbose trace messaging. Avoid 
       except when needing super low-level .NET Framework debugging. Available 
       in 0.10.4+.

     --nocolor, --no-color
     No Color - Do not show colorization in logging output. This overrides 
       the feature 'logWithoutColor', set to 'False'. Available in 0.10.9+.

     --acceptlicense, --accept-license
     AcceptLicense - Accept license dialogs automatically. Reserved for 
       future use.

 -y, --yes, --confirm
     Confirm all prompts - Chooses affirmative answer instead of prompting. 
       Implies --accept-license

 -f, --force
     Force - force the behavior. Do not use force during normal operation - 
       it subverts some of the smart behavior for commands.

     --noop, --whatif, --what-if
     NoOp / WhatIf - Don't actually do anything.

 -r, --limitoutput, --limit-output
     LimitOutput - Limit the output to essential information

     --timeout, --execution-timeout=VALUE
     CommandExecutionTimeout (in seconds) - The time to allow a command to 
       finish before timing out. Overrides the default execution timeout in the 
       configuration of 2700 seconds. '0' for infinite starting in 0.10.4.

 -c, --cache, --cachelocation, --cache-location=VALUE
     CacheLocation - Location for download cache, defaults to %TEMP% or value 
       in chocolatey.config file.

     --allowunofficial, --allow-unofficial, --allowunofficialbuild, --allow-unofficial-build
     AllowUnofficialBuild - When not using the official build you must set 
       this flag for choco to continue.

     --failstderr, --failonstderr, --fail-on-stderr, --fail-on-standard-error, --fail-on-error-output
     FailOnStandardError - Fail on standard error output (stderr), typically 
       received when running external commands during install providers. This 
       overrides the feature failOnStandardError.

     --use-system-powershell
     UseSystemPowerShell - Execute PowerShell using an external process 
       instead of the built-in PowerShell host. Should only be used when 
       internal host is failing. Available in 0.9.10+.

     --no-progress
     Do Not Show Progress - Do not show download progress percentages. 
       Available in 0.10.4+.

     --proxy=VALUE
     Proxy Location - Explicit proxy location. Overrides the default proxy 
       location of ''. Available for config settings in 0.9.9.9+, this CLI 
       option available in 0.10.4+.

     --proxy-user=VALUE
     Proxy User Name - Explicit proxy user (optional). Requires explicit 
       proxy (`--proxy` or config setting). Overrides the default proxy user of 
       ''. Available for config settings in 0.9.9.9+, this CLI option available 
       in 0.10.4+.

     --proxy-password=VALUE
     Proxy Password - Explicit proxy password (optional) to be used with 
       username. Requires explicit proxy (`--proxy` or config setting) and user 
       name.  Overrides the default proxy password (encrypted in settings if 
       set). Available for config settings in 0.9.9.9+, this CLI option 
       available in 0.10.4+.

     --proxy-bypass-list=VALUE
     ProxyBypassList - Comma separated list of regex locations to bypass on 
       proxy. Requires explicit proxy (`--proxy` or config setting). Overrides 
       the default proxy bypass list of ''. Available in 0.10.4+.

     --proxy-bypass-on-local
     Proxy Bypass On Local - Bypass proxy for local connections. Requires 
       explicit proxy (`--proxy` or config setting). Overrides the default 
       proxy bypass on local setting of 'True'. Available in 0.10.4+.

     --log-file=VALUE
     Log File to output to in addition to regular loggers. Available in 0.1-
       0.8+.

     --skipcompatibilitychecks, --skip-compatibility-checks
     SkipCompatibilityChecks - Prevent warnings being shown before and after 
       command execution when a runtime compatibility problem is found between 
       the version of Chocolatey and the Chocolatey Licensed Extension. 
       Available in 1.1.0+

 -s, --source=VALUE
     Source - The source to find the package(s) to install. Special sources 
       include: ruby, webpi, cygwin, windowsfeatures, and python. Defaults to 
       default feeds.

     --version=VALUE
     Version - A specific version to uninstall. Defaults to unspecified.

 -a, --allversions, --all-versions
     AllVersions - Uninstall all versions? Defaults to false.

     --ua, --uninstallargs, --uninstallarguments, --uninstall-arguments=VALUE
     UninstallArguments - Uninstall Arguments to pass to the native installer 
       in the package. Defaults to unspecified.

 -o, --override, --overrideargs, --overridearguments, --override-arguments
     OverrideArguments - Should uninstall arguments be used exclusively 
       without appending to current package passed arguments? Defaults to false.

     --notsilent, --not-silent
     NotSilent - Do not uninstall this silently. Defaults to false.

     --params, --parameters, --pkgparameters, --packageparameters, --package-parameters=VALUE
     PackageParameters - Parameters to pass to the package. Defaults to 
       unspecified.

     --argsglobal, --args-global, --installargsglobal, --install-args-global, --applyargstodependencies, --apply-args-to-dependencies, --apply-install-arguments-to-dependencies
     Apply Install Arguments To Dependencies  - Should install arguments be 
       applied to dependent packages? Defaults to false.

     --paramsglobal, --params-global, --packageparametersglobal, --package-parameters-global, --applyparamstodependencies, --apply-params-to-dependencies, --apply-package-parameters-to-dependencies
     Apply Package Parameters To Dependencies  - Should package parameters be 
       applied to dependent packages? Defaults to false.

 -m, --sxs, --sidebyside, --side-by-side, --allowmultiple, --allow-multiple, --allowmultipleversions, --allow-multiple-versions
     AllowMultipleVersions - Should multiple versions of a package be 
       installed? Defaults to false.

 -x, --forcedependencies, --force-dependencies, --removedependencies, --remove-dependencies
     RemoveDependencies - Uninstall dependencies when uninstalling package(s-
       ). Defaults to false.

 -n, --skippowershell, --skip-powershell, --skipscripts, --skip-scripts, --skip-automation-scripts
     Skip PowerShell - Do not run chocolateyUninstall.ps1. Defaults to false.

     --ignorepackagecodes, --ignorepackageexitcodes, --ignore-package-codes, --ignore-package-exit-codes
     IgnorePackageExitCodes - Exit with a 0 for success and 1 for non-succes-
       s, no matter what package scripts provide for exit codes. Overrides the 
       default feature 'usePackageExitCodes' set to 'True'. Available in 0.-
       9.10+.

     --usepackagecodes, --usepackageexitcodes, --use-package-codes, --use-package-exit-codes
     UsePackageExitCodes - Package scripts can provide exit codes. Use those 
       for choco's exit code when non-zero (this value can come from a 
       dependency package). Chocolatey defines valid exit codes as 0, 1605, 
       1614, 1641, 3010. Overrides the default feature 'usePackageExitCodes' 
       set to 'True'. Available in 0.9.10+.

     --autouninstaller, --use-autouninstaller
     UseAutoUninstaller - Use auto uninstaller service when uninstalling. 
       Overrides the default feature 'autoUninstaller' set to 'True'. Available 
       in 0.9.10+.

     --skipautouninstaller, --skip-autouninstaller
     SkipAutoUninstaller - Skip auto uninstaller service when uninstalling. 
       Overrides the default feature 'autoUninstaller' set to 'True'. Available 
       in 0.9.10+.

     --failonautouninstaller, --fail-on-autouninstaller
     FailOnAutoUninstaller - Fail the package uninstall if the auto 
       uninstaller reports and error. Overrides the default feature 
       'failOnAutoUninstaller' set to 'False'. Available in 0.9.10+.

     --ignoreautouninstallerfailure, --ignore-autouninstaller-failure
     Ignore Auto Uninstaller Failure - Do not fail the package if auto 
       uninstaller reports an error. Overrides the default feature 
       'failOnAutoUninstaller' set to 'False'. Available in 0.9.10+.

     --stoponfirstfailure, --stop-on-first-failure, --stop-on-first-package-failure
     Stop On First Package Failure - stop running install, upgrade or 
       uninstall on first package failure instead of continuing with others. 
       Overrides the default feature 'stopOnFirstPackageFailure' set to 'False-
       '. Available in 0.10.4+.

     --exitwhenrebootdetected, --exit-when-reboot-detected
     Exit When Reboot Detected - Stop running install, upgrade, or uninstall 
       when a reboot request is detected. Requires 'usePackageExitCodes' 
       feature to be turned on. Will exit with either 350 or 1604.  Overrides 
       the default feature 'exitOnRebootDetected' set to 'False'.  Available in 
       0.10.12+.

     --ignoredetectedreboot, --ignore-detected-reboot
     Ignore Detected Reboot - Ignore any detected reboots if found. Overrides 
       the default feature 'exitOnRebootDetected' set to 'False'.  Available in 
       0.10.12+.

     --fromprograms, --from-programs, --fromprogramsandfeatures, --from-programs-and-features
     From Programs and Features - Uninstalls a program from programs and 
       features. Name used for id must be a match or a wildcard (*) to Display 
       Name in Programs and Features. Available in [licensed editions](https://chocolatey.org/compare) only 
       (licensed version 1.8.0+) and requires v0.10.4+.

~~~

[Command Reference](xref:choco-commands)


> :memo: **NOTE** This documentation has been automatically generated from `choco uninstall -h`. 

