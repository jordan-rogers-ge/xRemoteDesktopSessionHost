[![Build status](https://ci.appveyor.com/api/projects/status/ly6w6vaavkshrpg8/branch/master?svg=true)](https://ci.appveyor.com/project/PowerShell/xremotedesktopsessionhost/branch/master)

# xRemoteDesktopSessionHost

The **xRemoteDesktopSessionHost** module contains the **xRDSessionDeployment**, **xRDSessionCollection**, **xRDSessionCollectionConfiguration**, and **xRDRemoteApp** resources, allowing creation and configuration of a Remote Desktop Session Host (RDSH) instance.


This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Contributing
Please check out common DSC Resources [contributing guidelines](https://github.com/PowerShell/DscResource.Kit/blob/master/CONTRIBUTING.md).


## Resources

* **xRDSessionDeployment** creates and configures a deployment in RDSH. 
* **xRDSessionCollection** creates an RDSH collection.
* **xRDSessionCollectionConfiguration** configures an RDSH collection.
* **xRDRemoteApp** publishes applications for your RDSH collection.

### xRDSessionDeployment

* **SessionHost**: Specifies the FQDN of a servers to host the RD Session Host role service.
* **ConnectionBroker**: The FQDN of a server to host the RD Connection Broker role service.
* **WebAccessServer**: The FQDN of a server to host the RD Web Access role service.

### xRDSessionCollection

* **CollectionName**: Specifies a name for the session collection
* **SessionHost**: Specifies a RD Session Host server to include in the session collection.
* **CollectionDescription**: A description for the collection.
* **ConnectionBroker**: The Remote Desktop Connection Broker (RD Connection Broker) server for a Remote Desktop deployment.

### xRDSessionCollectionConfiguration

* **CollectionName**: Specifies the name for the session collection.
* **ActiveSessionLimitMin**: Specifies the maximum time, in minutes, an active session runs.
After this period, the RD Session Host server ends the session.
* **AuthenticateUsingNLA**: Indicates whether to use Network Level Authentication (NLA).
If this value is $True, Remote Desktop uses NLA to authenticate a user before the user sees a logon screen.
* **AutomaticReconnectionEnabled**: Indicates whether the Remote Desktop client attempts to reconnect after a connection interruption.
* **BrokenConnectionAction**: Specifies an action for an RD Session Host server to take after a connection interruption.
* **ClientDeviceRedirectionOptions**: Specifies a type of client device to be redirected to an RD Session Host server in this session collection.
* **ClientPrinterAsDefault**: Indicates whether to use the client printer or server printer as the default printer.
If this value is $True, use the client printer as default.
If this value is $False, use the server as default.
* **ClientPrinterRedirected**: Indicates whether to use client printer redirection, which routes print jobs from the Remote Desktop session to a printer attached to the client computer.
* **CollectionDescription**: Specifies a description of the session collection.
* **ConnectionBroker**: Specifies the Remote Desktop Connection Broker (RD Connection Broker) server for a Remote Desktop deployment.
* **CustomRdpProperty**: Specifies Remote Desktop Protocol (RDP) settings to include in the .rdp files for all Windows Server 2012 RemoteApp programs and remote desktops published in this collection.
* **DisconnectedSessionLimitMin**: Specifies a length of time, in minutes.
After client disconnection from a session for this period, the RD Session Host ends the session.
* **EncryptionLevel**: Specifies the level of data encryption used for a Remote Desktop session.
* **IdleSessionLimitMin**: Specifies the length of time, in minutes, to wait before an RD Session Host logs off or disconnects an idle session.
* The BrokenConnectionAction parameter determines whether to log off or disconnect.
* **MaxRedirectedMonitors**: Specifies the maximum number of client monitors that an RD Session Host server can redirect to a remote session.
The maximum value for this parameter is 16.
* **RDEasyPrintDriverEnabled**: Specifies whether to enable the Remote Desktop Easy Print driver.
* **SecurityLayer**: Specifies which security protocol to use.
* **TemporaryFoldersDeletedOnExit**: Whether to delete temporary folders from the RD Session Host server for a disconnected session.
* **UserGroup**: Specifies a domain group authorized to connect to the RD Session Host servers in a session collection.

### xRDRemoteApp 

* **Alias**: Specifies an alias for the RemoteApp program.
* **CollectionName**: Specifies the name of the personal virtual desktop collection or session collection.
The cmdlet publishes the RemoteApp program to this collection.
* **DisplayName**: Specifies a name to display to users for the RemoteApp program.
* **FilePath**: Specifies a path for the executable file for the application.
Note: Do not include any environment variables.
* **FileVirtualPath**: Specifies a path for the application executable file.
This path resolves to the same location as the value of the FilePath parameter, but it can include environment variables.
* **FolderName**: Specifies the name of the folder that the RemoteApp program appears in on the Remote Desktop Web Access (RD Web Access) webpage and in the Start menu for subscribed RemoteApp and Desktop Connections.
* **CommandLineSetting**: Specifies whether the RemoteApp program accepts command-line arguments from the client at connection time.
* **RequiredCommandLine**: Specifies a string that contains command-line arguments that the client can use at connection time with the RemoteApp program.
* **IconIndex**: Specifies the index within the icon file (specified by the IconPath parameter) where the RemoteApp program’s icon can be found.
* **IconPath**: Specifies the path to a file containing the icon to display for the RemoteApp program identified by the Alias parameter.
* **UserGroups**: Specifies a domain group that can view the RemoteApp in RD Web Access, and in RemoteApp and Desktop Connections.
To allow all users to see a RemoteApp program, provide a value of Null.
* **ShowInWebAccess**: Specifies whether to show the RemoteApp program in the RD Web Access server, and in RemoteApp and Desktop Connections that the user subscribes to.

## Versions

### Unreleased

* Added support for assigning multiple session hosts to the deployment at the same time

* Changed the **SessionHost** parameter to **SessionHosts**

*  Added the following resources
    * **xRDGatewayConfiguration**
    * **xRDLicenseConfiguration**
    * **xRDServer**

### 1.2.0.0

*  Fixed an issue with version checks where OS version greater than 9 would fail (Windows 10/Server 2016)

### 1.1.0.0

* Fixed encoding

### 1.0.1

### 1.0.0.0

* Initial release with the following resources 
    * **xRDSessionDeployment**
    * **xRDSessionCollection**
    * **xRDSessionCollectionConfiguration**
    * **xRDRemoteApp**


	
## Examples

### End to End  
 
```powershell
param (
[string]$brokerFQDN,
[string]$webFQDN,
[string]$collectionName,
[string]$collectionDescription
)

configuration RDSDeployment
{
   param 
    ( 
        [Parameter(Mandatory)]
        [String]$domainName,

        [Parameter(Mandatory)]
        [PSCredential]$adminCreds,

        # Connection Broker Node name
        [String]$connectionBroker,
        
        # Web Access Node name
        [String]$webAccessServer,

        # Gateway external FQDN
        [String]$externalFqdn,
        
        # RD Session Host count and naming prefix
        [Int]$numberOfRdshInstances = 1,
        [String]$sessionHostNamingPrefix = "SessionHost-",

        # Collection Name
        [String]$collectionName,

        # Connection Description
        [String]$collectionDescription

    ) 

    Import-DscResource -ModuleName PSDesiredStateConfiguration -ModuleVersion 1.1
    Import-DscResource -ModuleName xRemoteDesktopSessionHost
   
    $localhost = [System.Net.Dns]::GetHostByName((hostname)).HostName

    $username = $adminCreds.UserName -split '\\' | select -last 1
    $domainCreds = New-Object System.Management.Automation.PSCredential ("$domainName\$username", $adminCreds.Password)

    if (-not $connectionBroker)   { $connectionBroker = $localhost }
    if (-not $webAccessServer)    { $webAccessServer  = $localhost }

    if ($sessionHostNamingPrefix)
    { 
        $sessionHosts = @( 0..($numberOfRdshInstances-1) | % { "$sessionHostNamingPrefix$_.$domainname"} )
    }
    else
    {
        $sessionHosts = @( $localhost )
    }

    if (-not $collectionName)         { $collectionName = "Desktop Collection" }
    if (-not $collectionDescription)  { $collectionDescription = "A sample RD Session collection up in cloud." }


    Node localhost
    {

        WindowsFeature RSAT-RDS-Tools
        {
            Ensure = "Present"
            Name = "RSAT-RDS-Tools"
            IncludeAllSubFeature = $true
        }

        Registry RdmsEnableUILog
        {
            Ensure = "Present"
            Key = "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDMS"
            ValueName = "EnableUILog"
            ValueType = "Dword"
            ValueData = "1"
        }
 
        Registry EnableDeploymentUILog
        {
            Ensure = "Present"
            Key = "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDMS"
            ValueName = "EnableDeploymentUILog"
            ValueType = "Dword"
            ValueData = "1"
        }
 
        Registry EnableTraceLog
        {
            Ensure = "Present"
            Key = "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDMS"
            ValueName = "EnableTraceLog"
            ValueType = "Dword"
            ValueData = "1"
        }
 
        Registry EnableTraceToFile
        {
            Ensure = "Present"
            Key = "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\RDMS"
            ValueName = "EnableTraceToFile"
            ValueType = "Dword"
            ValueData = "1"
        }

        WindowsFeature RDS-Licensing
        {
            Ensure = "Present"
            Name = "RDS-Licensing"
        }

        xRDSessionDeployment Deployment
        {

            ConnectionBroker = $connectionBroker
            WebAccessServer  = $webAccessServer

            SessionHosts     = $sessionHosts

            PsDscRunAsCredential = $domainCreds
        }


        xRDServer AddLicenseServer
        {
            DependsOn = "[xRDSessionDeployment]Deployment"
            
            Role    = 'RDS-Licensing'
            Server  = $connectionBroker

            PsDscRunAsCredential = $domainCreds
        }

        xRDLicenseConfiguration LicenseConfiguration
        {
            DependsOn = "[xRDServer]AddLicenseServer"

            ConnectionBroker = $connectionBroker
            LicenseServers   = @( $connectionBroker )

            LicenseMode = 'PerUser'

            PsDscRunAsCredential = $domainCreds
        }


        xRDServer AddGatewayServer
        {
            DependsOn = "[xRDLicenseConfiguration]LicenseConfiguration"
            
            Role    = 'RDS-Gateway'
            Server  = $webAccessServer

            GatewayExternalFqdn = $externalFqdn

            PsDscRunAsCredential = $domainCreds
        }

        xRDGatewayConfiguration GatewayConfiguration
        {
            DependsOn = "[xRDServer]AddGatewayServer"

            ConnectionBroker = $connectionBroker
            GatewayServer    = $webAccessServer

            ExternalFqdn = $externalFqdn

            GatewayMode = 'Custom'
            LogonMethod = 'AllowUserToSelectDuringConnection'

            UseCachedCredentials = $true
            BypassLocal = $false

            PsDscRunAsCredential = $domainCreds
        } 
        

        xRDSessionCollection Collection
        {
            DependsOn = "[xRDGatewayConfiguration]GatewayConfiguration"

            ConnectionBroker = $connectionBroker

            CollectionName = $collectionName
            CollectionDescription = $collectionDescription
            
            SessionHosts = $sessionHosts

            PsDscRunAsCredential = $domainCreds
        }

    }
}

write-verbose "Creating configuration with parameter values:"
write-verbose "Collection Name: $collectionName"
write-verbose "Collection Description: $collectionDescription"
write-verbose "Connection Broker: $brokerFQDN"
write-verbose "Web Access Server: $webFQDN"

RDSDeployment -collectionName $collectionName -collectionDescription $collectionDescription -connectionBroker $brokerFQDN -webAccessServer $webFQDN -OutputPath .\RDSDSC\

Set-DscLocalConfigurationManager -verbose -path .\RDSDSC\

Start-DscConfiguration -wait -force -verbose -path .\RDSDSC\
```
