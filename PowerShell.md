## What Version of PowerShell?
https://adamtheautomator.com/powershell-version/  
TLDR: `$PSVersionTable.PSVersion`

## run powershell as SYSTEM
- First run Windows Terminal or cmd.exe as administrator (elevated)
- therein: `c:\bin\psexec.exe -s powershell.exe`
- Note, PSReadline colorization doesn’t work over the pipes involved.  Instead of slaving the current console to the system powershell over pipes, you can launch the system powershell with its own console into an interactive WTS session with `-i <session #>`.  Also `-d` to not wait for the new process to exit.  
`c:\bin\psexec.exe -i 1 -d -s powershell.exe`

## Color, completion, extended history
Live syntax-based coloration of the command-line, command tab-completion, and persistent command history are provided by the `PSReadline` module.  There are some commands for configuring it.  
https://docs.microsoft.com/en-us/powershell/module/psreadline

## Profile(s)
user-specific:  
`C:\Users\dcox\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1`  
(there are several others)

## grep
use `select-string`:
- `get-content $file | select-string "sought string"`
- `select-string -Path d:\app\data\* -Include *.log "sought string"`
- `select-string -Path $file "sought string"`
- `select-string -Path $file -Encoding unicode "sought string"`
- `> select-string -path D:\docs\Linode\LinodeServerHowTo.txt -pattern "/bin/.*sh"`
  `D:\docs\Linode\LinodeServerHowTo.txt:94:     (sudo /bin/bash)`
  `D:\docs\Linode\LinodeServerHowTo.txt:115:#! /bin/bash`
- may need to convert objects to strings before select-string:  
`dir env: | out-string -stream | select-string -pattern ssh`

## Initialize array
- `$A = 22,5,10,8,12,9,80`
- `$B = ,7`
- `$C = 5..8`
- typed: `[int32[]]$ia = 1500,2230,3350,4000`, `[Diagnostics.Process[]]$zz = Get-Process`
- The array sub-expression operator creates an array from the statements inside it. Whatever the statement inside the operator produces, the operator will place it in an array. Even if there is zero or one object.  The array operator is useful in scripts when you are getting objects, but do not know how many objects you get.  
`$p = @(Get-Process Notepad)` (may be none, or one, or several)
- thus, this form of multi-line should have no commas, so it’ll evaluate each statement:  
```
$dlls = @(
    Join-Path $basePath '\bin\debug\dll1.dll'
    Join-Path $basePath '\bin\debug\dll2.dll'
    Join-Path $basePath '\bin\debug\dll3.dll'
)
```

#Dictionary / Associative Array
Use braces instead of parentheses:  
```
$headers = @{
    "Authorization" = "Bearer $gcpToken"
}
```  
For multiple pairs on the same line, separate with semicolon.  Not needed if separated by line break.

Above creates a hash table.  Optionally prefix the `@` with `[ordered]` for an ordered dictionary instead.

## Send Data to the Clipboard
Pipe data to `Set-Clipboard` (or use the `-Value` option to pass data as an argument).  
`C:\bin\AtAcctCodec.exe :lEWFIwTYgnU1 | set-clipboard -append`  
Note also `Get-Clipboard`.

## Display Commands As They Are Executed
`Set-PSDebug -Trace 1`  
https://stackoverflow.com/a/49647445  
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/set-psdebug?view=powershell-7.2&viewFallbackFrom=powershell-6 

## Remote WMI Authorization
Use an account that’s a non-filtered administrator on the remote host, such as hostname\Administrator (may have to enable it on the remote host first) or (untested) a domain administrator.  
```
> $pass = convertto-securestring "...password..." -asplaintext -force
> $credential = new-object System.Management.Automation.PSCredential "hostname\Administrator", $pass
> get-wmiobject Win32_Product -ComputerName hostname -credential $credential

IdentifyingNumber : {90160000-008C-0000-1000-0000000FF1CE}
Name              : Office 16 Click-to-Run Extensibility Component
Vendor            : Microsoft Corporation
Version           : 16.0.15225.20150
Caption           : Office 16 Click-to-Run Extensibility Component
...
```

## Unset an Environment Variable
Use the environment ‘drive’ provider:  
`del env:VARNAME`

## uuidgen
`function uuidgen { [Guid]::NewGuid().ToString() }`

## Misc
`$LastExitCode`

`$ErrorActionPreference = 'Stop'`  
```
function execNativeTool(
        [string] $nativeTool,
        [string] $nativeToolDisplayName,
        [object[]] $passThruArgs)
{
    & ${nativeTool} @passThruArgs
    if ($LastExitCode -ne 0)
    {
        throw "${nativeToolDisplayName} error $LastExitCode"
    }
}

function maven() { execNativeTool maven.exe conan $args } # actually need to run cmd.exe maven.bat?
function docker() { execNativeTool docker.exe cmake $args }
function msbuild() { execNativeTool msbuild.exe msbuild $args }
(etc)
```
