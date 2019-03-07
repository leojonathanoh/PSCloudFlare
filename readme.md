# PSCloudFlare Module

This is a powershell module for interacting more easily with the Cloudflare RESTful API.

## Introduction
Firstly the original script I based this on can be located **[Here](https://github.com/poshsecurity/PowerShell-CloudFlare-Tor-Whitelist)**

This module tears apart the original script and converts it into a proper module. In the process I've added a few improvements:
- Implemented a proper build process
- Added documentation
- Generalized the functions
- Added validation around some of the parameters

This starts with a few functions I needed to add a large number of firewall rules but can be very easily added and expanded upon to suit your needs. The full documentation for the api can be found **[Here](https://api.cloudflare.com/)**

## Install
You can use the installer script included with this project or, if you are using PowerShell 5.0 or greater simply run:

`install-module pscloudflare`

## Example
Here is a small example of some stuff you can do thus far.
```
$Token = 'aaaaaaaaaabbbbbbbbbbccccccccccc1234552'
$Email = 'jdoe@contoso.com'
$Zone = 'contoso.com'

# Connect to the CloudFlare Client API
try {
    Connect-CFClientAPI -APIToken $Token -EmailAddress $Email -ErrorAction Stop
    $Connected = $true
}
catch {
    $Connected = $false
}

if ($Connected) {
    # Add a firewall rule that challenges the visitor with a CAPTCHA
    Add-CFFirewallRule -Item '192.168.1.0/24' -Notes 'Organization Block 1' -Target 'ip_range' -Mode:challenge -Verbose

    # List the firewall rules for the organization
    Get-CFFirewallRule -Verbose -ErrorAction Stop

    # Target the contoso.com zone (You can also simply pass the zone to the functions directly if you prefer)
    Set-CFCurrentZone -Zone $Zone -Verbose

    # Add a firewall rule that challenges the visitor with a CAPTCHA just for the contoso.com zone
    Add-CFFirewallRule -Item '10.0.0.1' -Notes 'Zone Block 1' -Target 'ip' -Mode:challenge -Verbose

    # List the firewall rules for contoso.com
    Get-CFFirewallRule -Verbose
}
```

## Contributing
I developed this module for a fairly specific task of adding a large number of firewall rules to Cloudflare. I also tried to make the module generic enough to easily expand upon. All the real work for invoking REST calls is done by Invoke-CFAPI4Request. Prior to calling this function you should always populate the parameters that will be used for the REST request by using Set-CFRequestData. This will ensure that the most recent call data is always kept in a module variable (exposed with Get-CFRequestData) for troubleshooting and debugging purposes. Note that the parameter data is not converted into JSON until the REST method is called via the Invoke-CFAPI4Request (so you don't need to convert anything yourself in any of the functions you create).

You can look at the existing functions to quickly generate new functions for this module for just about any of the other (numerous) exposed API RESTful methods available. The API documentation is really comprehensive.

## Credits
**Original Script:** [https://github.com/poshsecurity/PowerShell-CloudFlare-Tor-Whitelist](https://github.com/poshsecurity/PowerShell-CloudFlare-Tor-Whitelist)
**Cloudflare API Documentation:** [https://api.cloudflare.com/](https://api.cloudflare.com/)