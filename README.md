### Node.js - Watching changes (created, deleted, changed )  in Windows folder on network with chokidar.

#### 1. Installation

Install Node.js
https://nodejs.org/en/download/

Install chokidar:
https://github.com/paulmillr/chokidar

Opens terminal windows and execute:

``
$npm install chokidar --save
``

#### 2. Map a network drive in Windows

Specify the drive letter for the connection and the folder that you want to connect.
Example:

``
Shared (\\W-LNIELSEN\Users\leslie.nielsen\Desktop\Temp\2018)(Z:)
``

#### 3. watcher.js

**Add the usePolling: true option to watch network folders.**

```javascript
var chokidar = require('chokidar');

var fs = require('fs');

var log_file = fs.createWriteStream(__dirname + '/watch.log', {flags : 'w'});

try
  {
  		//Format of local path: D:\\folderToWatch
        var watcher = chokidar.watch('Z:',
        {
                    ignored: /^\./,
                    persistent: true,
                    usePolling: true,
        });
        watcher.on('add', function(path) 
         {
                log_file.write(path+'\n');
          })
          .on('change', function(path) 
          {
                      console.log('File', path, 'has been changed');
          })
          .on('unlink', function(path) {
                  console.log('File', path, 'has been removed');
          })
          .on('error', function(error) {
                  console.error('Error happened', error);
         })
} 
catch (err)
{
        console.error('Error happened', error);
}
```

#### 4. Running the watcher:

``
$node watcher.js
``

Add files or folders to remote path:
``Shared (\\W-LNIELSEN\Users\leslie.nielsen\Desktop\Temp\2018)(Z:)`` and check if log watch.log stored the changes.

<img src="https://image.ibb.co/hcqQ6J/watcher_node02.jpg" alt="watcher_node02" border="0"></a>



----

### PowerShell Script - Watching changes (created, deleted, changed,renamed )  in a folder.

#### 1. watcher.ps1


    $watcher = New-Object System.IO.FileSystemWatcher
    $watcher.Path = "D:\source"
    $watcher.Filter = "*.txt*"
    $watcher.IncludeSubdirectories = $true
    $watcher.EnableRaisingEvents = $true  


    $action = { $path = $Event.SourceEventArgs.FullPath
                $changeType = $Event.SourceEventArgs.ChangeType
                $logline = "$(Get-Date), $changeType, $path"
                Add-content "D:\log.txt" -value $logline
              }    

    Register-ObjectEvent $watcher "Created" -Action $action
    Register-ObjectEvent $watcher "Changed" -Action $action
    Register-ObjectEvent $watcher "Deleted" -Action $action
    Register-ObjectEvent $watcher "Renamed" -Action $action
    while ($true) {sleep 5}

#### 2. Enabling Execution of PowerShell PS1 Scripts

``File C:\Temp\watcher.ps1 cannot be loaded because the execution of scripts is disabled on this system. Please see "get-help about_signing" for more details.``

To run PowerShell scripts (files that end with .ps1), you must first set the execution policy to Unrestricted (As an Administrator).

``
PS> Set-ExecutionPolicy Unrestricted  
``

[A] Yes to All


Launch Windows PowerShell, and wait a moment for the PS command prompt to appear
Navigate to the directory where the script lives.

``
PS> cd C:\tmp\watcher.ps1\  (enter)
``

Execute the script:

``
PS> .\watcher.ps1 (enter)
``

#### 3. Check the log file

Add files or folders to the path: ``D:\source`` and check if  ``D:\log.txt`` stored the changes.

<img src="https://image.ibb.co/gBu4Yy/watcher_powershell.jpg" alt="watcher_powershell" border="0"></a>
