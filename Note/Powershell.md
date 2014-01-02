# Powershell

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