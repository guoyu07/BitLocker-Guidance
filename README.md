# BitLocker Guidance

## About Microsoft BitLocker 
[Microsoft BitLocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview) is a full volume encryption feature built into Windows. BitLocker is intended to protect data on devices that have been lost or stolen. BitLocker is available in the Ultimate and Enterprise editions of Windows Vista and Windows 7, in the Professional and Enterprise editions of Windows 8/8.1, and in the Pro, Enterprise, and Education editions of Windows 10. BitLocker is also included in the Windows Server releases of Windows since Window Server 2008.

The Windows 10 BitLocker modules have been [validated](https://csrc.nist.gov/projects/cryptographic-module-validation-program/validated-modules) against [NIST](http://www.nist.gov/) [FIPS 140-2](https://csrc.nist.gov/projects/cryptographic-module-validation-program) program multiple times:

* June 2, 2016 certificate numbers [2601](https://csrc.nist.gov/projects/cryptographic-module-validation-program/certificate/2601), [2602](https://csrc.nist.gov/projects/cryptographic-module-validation-program/certificate/2602), and [2603](https://csrc.nist.gov/projects/cryptographic-module-validation-program/certificate/2603).
* August 26, 2016 certificate numbers [2701](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/2701), [2702](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/2702), [2703](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/2703).
* January 26, 2017 certificate numbers [2932](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/2932), [2933](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/2933), and [2934](https://csrc.nist.gov/projects/cryptographic-module-validation-program/Certificate/2934).

## About this repository
This repository hosts [Group Policy Objects](./Group%20Policy%20Objects/Computer/), compliance checks, and [configuration tools](./Scripts)  in support of implementing BitLocker.

A [BitLocker PowerShell module] has been provided to aid in provisioning BitLocker on standalone systems. [Microsoft BitLocker Administration and Monitoring](https://technet.microsoft.com/en-us/windows/hh826072.aspx) (MBAM) can be used for [provisioning BitLocker](https://docs.microsoft.com/en-us/microsoft-desktop-optimization-pack/mbam-v25/) on domain joined systems.

## BitLocker settings
NSA Information Assurance recommends using BitLocker settings from the Microsoft [Windows Security Baseline](https://docs.microsoft.com/en-us/windows/security/threat-protection/windows-security-baselines), available in the [Security Compliance Toolkit](https://docs.microsoft.com/en-us/windows/security/threat-protection/security-compliance-toolkit-10), with the following exceptions:

* '''Deny write access to removable drives not protected by BitLocker''' can be set to '''Not Configured''' instead of Enabled.
* '''Configure minimum PIN length for startup''' can be set to 6 characters or more instead of 7.

Some environments may desire additional, but optional, protection provided by a BitLocker startup PIN. The following settings may be configured when this scenario is desired:


Administrators may need to configure [BitLocker Network Unlock](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-how-to-enable-network-unlock) ensure systems apply updates without requiring a user be physically present to enter a PIN at system boot.

## BitLocker Group Policy 

The Microsoft Security Compliance Toolkit contains BitLocker Group Policy Objects (GPO) for each Windows 10 operating system release's Windows Security Baseline. The GPOs can be used to configure and manage domain joined as well as standalone systems.


If using MBAM to configure and manage BitLocker on domain joined systems, then download the [Microsoft Desktop Optimization Pack (MDOP) Group Policy templates](https://www.microsoft.com/en-us/download/confirmation.aspx?id=55531) since they contain the [MBAM Group Policy settings](https://docs.microsoft.com/en-us/microsoft-desktop-optimization-pack/mbam-v25/planning-for-mbam-25-group-policy-requirements). 


### Importing the BitLocker domain Group Policy
Use the PowerShell Group Policy commands to import the BitLocker Group Policy into a domain. Run the following command on a domain controller from a PowerShell prompt running as a domain administrator. 

```
Invoke-ApplySecureHostBaseline -Path '.\Secure-Host-Baseline' -PolicyNames 'BitLocker'
```

### Importing the AppLocker local Group Policy
Use Microsoft's LGPO tool to apply the BitLocker Group Policy to a standalone system. Run the following command from a command prompt running as a local administrator.

```
Invoke-ApplySecureHostBaseline -Path '.\Secure-Host-Baseline' -PolicyNames 'BitLocker' -ToolPath '.\LGPO\lgpo.exe'
```

## Common issues

### Conflicting BitLocker startup options
* **Issue**: Error message: *The Group Policy settings for BitLocker startup options are in conflict and cannot be applied*. Error code: 0x8031005B
* **Explanation**: The 'Require additional authentication at startup' policy description text can be misleading on how to correctly configure it.
* **Resolution**: 
    1. Go to **Computer Configuration** > **Administrative Templates** > **Windows Components** > **BitLocker Drive Encryption** > **Operating System Drives**
    1. Change the **Require additional authentication at startup** policy to configure all 4 dropdown menu options to **Allow** *OR* set 1 option to **Require** and the other 3 options to **Do not allow**.
    1. Run **gpupdate /force** from the command line.

### Support for pre-boot PIN entry on tablets

* **Issue**: Error message: *No pre-boot keyboard detected. The user may not be able to provide required input to unlock the volume*. Error code: 0x803100B5
* **Explanation**: BitLocker checks if the system is a tablet. If it is a tablet, then BitLocker displays the above error message when trying to use a PIN protector. BitLocker doesn't check if the system supports a pre-boot keyboard. Some tablets may have a BIOS that supports a software keyboard. For example, the [Dell Venue 11 Pro](http://www.dell.com/support/Article/us/en/19/SLN293013/EN), [Surface Pro 3, and Surface Pro 4](https://blogs.technet.microsoft.com/askpfeplat/2014/07/13/bitlocker-pin-on-surface-pro-3-and-other-tablets/) support entering a BitLocker PIN at pre-boot with a BIOS software keyboard. Some tablets may have detachable keyboard that works during pre-boot. For example, the Surface Pro 2 with [firmware update from March 2014](https://www.microsoft.com/surface/en-us/support/install-update-activate/pro-2-history), Surface Pro 3, and Surface Pro 4 support entering a BitLocker PIN at pre-boot with their detachable keyboards. If the tablet does not support a BIOS software keyboard or a detachable keyboard that works during pre-boot, then configuring the below policy will require a USB keyboard be plugged into the tablet to enter a BitLocker PIN at pre-boot. Contact the OEM to inquire about tablet support for this specific scenario.
* **Resolution**: 
    1. Go to **Computer Configuration** > **Administrative Templates** > **Windows Components** > **BitLocker Drive Encryption**
    1. Set the **Enable use of BitLocker authentication requiring preboot keyboard input on slates** policy to **Enabled**.
    1. Run **gpupdate /force** from the command line.

## License
See [LICENSE](./LICENSE.md).

## Contributing
See [CONTRIBUTING](./CONTRIBUTING.md).

## Disclaimer
See [DISCLAIMER](./DISCLAIMER.md).