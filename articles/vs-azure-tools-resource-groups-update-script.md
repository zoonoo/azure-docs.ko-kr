<properties
	pageTitle="Azure 리소스 그룹 프로젝트 PowerShell 스크립트 업데이트 | Microsoft Azure"
	description="Azure 리소스 그룹 배포 프로젝트와 함께 제공되는 PowerShell 스크립트를 수동으로 업데이트하는데 필요한 단계를 설명합니다."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="11/10/2015"
	ms.author="kempb" />

# Azure 리소스 그룹 프로젝트 PowerShell 스크립트 업데이트

Visual Studio의 Azure 리소스 그룹 배포 프로젝트는 Azure PowerShell 스크립트를 사용하여 자산을 준비하고 Visual Studio에서 Azure에 배포합니다. 프로젝트의 스크립트에 사용되는 Azure PowerShell 버전은 0.9.8입니다. 하지만 Azure PowerShell의 새 버전인 1.0버전이 릴리스되었습니다.

Azure PowerShell의 새 버전에는 새 기능이 제공되고 Azure 리소스 관리자 사용 환경을 최고 수준으로 만들었으며, 몇 가지 cmdlet 기능을 변경되어 이전 버전의 스크립트와 호환이 되지 않습니다. 하지만 스크립트를 수정하여 이런 문제를 수정하고 Visual Studio 외부의 PowerShell 명령 창에서 스크립트를 실행할 수 있습니다. 이 문서는 수정된 내용과 위치에 대한 정보를 제공합니다. 구체적인 수정 내용과 더불어 완전히 수정된 스크립트 사본이 이 문서의 끝에 나열되어 있습니다.

## 해결 방법 옵션

새로운 Azure PowerShell을 설치한 후에 Azure 리소스 그룹 프로젝트에 두 가지 방식으로 문제가 나타날 수 있습니다.

- Visual Studio의 배포 대화 상자가 새로운 Azure cmdlet을 찾을 수 없기 때문에 cmdlet을 설치하라는 프롬프트 메시지가 표시됩니다. 하지만 새로운 버전의 cmdlet에서 모듈 이름이 변경되었기 때문에 Visual Studio는 새 모듈을 찾을 수 없습니다. 결국 Visual Studio에서 cmdlet을 설치하려고 하면 루프에 빠져버리게 됩니다.

- Visual Studio 밖의 PowerShell 명령 창에서 스크립트를 실행하면 오류가 발생할 수 있습니다.

	*'Switch-AzureMode'이라는 용어가 cmdlet의 이름, 함수, 스크립트 파일 또는 사용이 가능한 프로그램으로 인식되지 않습니다. 이름의 철자를 확인하거나 만약 경로가 포함되어 있으면 경로가 올바른지 확인하고 다시 시도합니다.*

새로운 Azure PowerShell에서 Azure 리소스 그룹 프로젝트를 배포하지 못하도록 막는 경우에는 다음 옵션에 따라 해결합니다.

- [웹 플랫폼 설치 관리자](http://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 Azure PowerShell 최신 버전을 제거하고 이전 버전(0.9.8)을 다시 설치합니다.

- 문제를 해결하도록 배포 프로젝트의 PowerShell 스크립트를 구체적으로 대상에 맞게 변경한 후 PowerShell 명령 창에서 스크립트를 수동으로 실행할 수 있습니다. Visual Studio의 **배포** 메뉴 항목에서는 스크립트를 실행할 수 없습니다. PowerShell 스크립트를 업데이트하는 단계 및 완전히 업데이트된 스크립트 사본이 이 문서의 아래쪽에 나열되어 있습니다.

## Azure PowerShell 스크립트 업데이트
이 지침은 줄 번호를 언급합니다. Visual Studio에서 줄 번호 매기기를 사용하려면 [방법: 편집기에 줄 번호 표시](https://msdn.microsoft.com/library/ms165340.aspx)를 참조하세요.

1. 61번 줄에서 다음 코드를

	```
	if ($StorageAccountResourceGroupName) {
	        Switch-AzureMode AzureResourceManager
	        $StorageAccountKey = (Get-AzureStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	    }
	    else {
	        Switch-AzureMode AzureServiceManagement
	        $StorageAccountKey = (Get-AzureStorageKey -StorageAccountName $StorageAccountName).Primary
	    }
	$StorageAccountContext = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

	다음으로 바꿀 수 있습니다.

	```
	$StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1
	$StorageAccountContext = New-AzureRMStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
	```

1. 87번 줄에서 다음 코드를

	```
	$ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission are
	```

	다음으로 바꿀 수 있습니다.

	```
	$ArtifactsLocationSasToken = New-AzureRMStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
	```
1. 95번 줄에서 이 코드를

	```
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-Force –Verbose
	```

	다음 코드와 바꿉니다.

	```
	New-AzureRMResourceGroup `
		-Name $ResourceGroupName `
		-Location $ResourceGroupLocation `
		-Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment `
		-ResourceGroupName $ResourceGroupName `
		-TemplateFile $TemplateFile `
		-TemplateParameterFile $TemplateParametersFile `
		@OptionalParameters `
		-ErrorAction Stop 	

	New-AzureRMResourceGroupDeployment
		-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
	    -ResourceGroupName $ResourceGroupName `
	    -TemplateFile $TemplateFile `
	    -TemplateParameterFile $TemplateParametersFile `
	    @OptionalParameters `
		-Verbose -Force
	```

## 업데이트된 스크립트
다음은 앞서 언급한 업데이트가 모두 적용된 수정된 스크립트입니다.

```
#Requires -Version 3.0

Param(
  [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
  [string] $ResourceGroupName = '$defaultResourceGroupName$',  
  [switch] $UploadArtifacts,
  [string] $StorageAccountName,
  [string] $StorageAccountResourceGroupName,
  [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
  [string] $TemplateFile = '..\Templates\$deployTemplateFileName$.json',
  [string] $TemplateParametersFile = '..\Templates\$deployTemplateFileName$.parameters.json',
  [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
  [string] $AzCopyPath = '..\Tools\AzCopy.exe',
  [string] $DSCSourceFolder = '..\DSC'
)

Import-Module Azure -ErrorAction SilentlyContinue

try {
  [Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("VSAzureTools-$UI$($host.name)".replace(" ","_"), "2.7.2")
} catch { }

Set-StrictMode -Version 3

$OptionalParameters = New-Object -TypeName Hashtable
$TemplateFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateFile)
$TemplateParametersFile = [System.IO.Path]::Combine($PSScriptRoot, $TemplateParametersFile)

if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)

    # Parse the parameter file and update the values of artifacts location and artifacts location SAS token if they are present
    $JsonContent = Get-Content $TemplateParametersFile -Raw | ConvertFrom-Json
    $JsonParameters = $JsonContent | Get-Member -Type NoteProperty | Where-Object {$_.Name -eq "parameters"}

    if ($JsonParameters -eq $null) {
        $JsonParameters = $JsonContent
    }
    else {
        $JsonParameters = $JsonContent.parameters
    }

    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }

    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context

    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }

    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }

    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }

    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
}

# Create or update the resource group using the specified template file and template parameters file
New-AzureRMResourceGroup `
	-Name $ResourceGroupName `
	-Location $ResourceGroupLocation `
	-Verbose -Force -ErrorAction Stop

Test-AzureRmResourceGroupDeployment `
	-ResourceGroupName $ResourceGroupName `
	-TemplateFile $TemplateFile `
	-TemplateParameterFile $TemplateParametersFile `
	@OptionalParameters `
	-ErrorAction Stop 	

New-AzureRMResourceGroupDeployment `
	-Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
    -ResourceGroupName $ResourceGroupName `
    -TemplateFile $TemplateFile `
    -TemplateParameterFile $TemplateParametersFile `
    @OptionalParameters `
	-Verbose -Force

```

<!---HONumber=Nov15_HO3-->