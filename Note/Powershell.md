# Powershell

## About powershell

### Check version

* [http://stackoverflow.com/questions/1825585/how-to-determine-what-version-of-powershell-is-installed](http://stackoverflow.com/questions/1825585/how-to-determine-what-version-of-powershell-is-installed)

```powershell
$PSVersionTable.psversion
```

## Windows service

### Check if a service is running

```powershell
function IsServiceStarted($serviceName){
    $service =  Get-Service -Name $serviceName -computer $Server -ErrorAction SilentlyContinue
	
    if ($service.Status -ne "Running"){
      return $false
    }
	else {
		return $true
	}
}

```