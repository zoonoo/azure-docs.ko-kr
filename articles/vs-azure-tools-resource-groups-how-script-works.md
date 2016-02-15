<properties
	pageTitle="Azure 리소스 그룹 프로젝트 배포 스크립트 개요 | Microsoft Azure"
	description="Azure 리소스 그룹 배포 프로젝트에서 PowerShell 스크립트가 작동하는 방식을 설명합니다."
	services="visual-studio-online"
	documentationCenter="na"
	authors="TomArcher"
	manager="douge"
	editor="tlee" />

 <tags
	ms.service="azure-resource-manager"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="na"
	ms.date="02/03/2016"
	ms.author="tarcher" />

# Azure 리소스 그룹 프로젝트 배포 스크립트 개요

Azure 리소스 그룹 배포 프로젝트는 Azure에 파일 및 기타 아티팩트를 스테이징하고 배포하는 데 도움이 됩니다. Visual Studio에서 Azure 리소스 관리자 배포 프로젝트를 만드는 경우, **Deploy-AzureResourceGroup.ps1**이라고 하는 PowerShell 스크립트가 프로젝트에 추가됩니다. 이 문서는 이 스크립트가 수행하는 작업 및 Visual Studio 내부 및 외부에서 스크립트를 실행하는 방법을 자세히 설명합니다.

## 스크립트가 수행하는 작업

Deploy-AzureResourceGroup.ps1 스크립트는 배포 워크플로에 중요한 두 가지 작업을 수행합니다.

- 템플릿 배포에 필요한 파일 또는 아티팩트 업로드
- 템플릿 배포

스크립트의 첫 번째 부분은 배포를 위해 파일 및 아티팩트를 업로드하고 스크립트의 마지막 cmdlet은 템플릿을 실제로 배포합니다. 예를 들어, 스크립트를 통해 가상 컴퓨터를 구성해야 하는 경우 배포 스크립트는 우선 구성 스크립트를 Azure 저장소 계정으로 안전하게 업로드합니다. 이렇게 하면 Azure 리소스 관리자는 프로비전 중에 가상 컴퓨터를 구성할 수 있습니다.

모든 템플릿 배포에 대해 업로드가 필요한 별도의 아티팩트가 있는 것은 아니기 때문에 *uploadArtifacts*라고 하는 스위치 매개 변수가 평가됩니다. 아티팩트를 업로드해야 하는 경우에는 스크립트를 호출할 때 *uploadArtifacts* 스위치를 설정합니다. 기본 템플릿 파일 및 매개 변수 파일은 업로드할 필요가 없습니다. 구성 스크립트, 중첩 배포 템플릿, 응용 프로그램 파일과 같은 기타 파일만 업로드할 필요가 있습니다.

## 스크립트 상세 설명

다음은 Deploy-AzureResourceGroup.ps1 Azure PowerShell 스크립트의 선택 섹션이 수행하는 작업에 대한 설명입니다.

>[AZURE.NOTE] 이것은 Deploy-AzureResourceGroup.ps1 스크립트 1.0 버전에 대한 설명입니다.

1.	Azure 리소스 관리자 배포 프로젝트에 선언 매개 변수가 필요합니다. 일부 매개 변수에는 프로젝트가 만들어질 때 설정된 기본값이 있습니다. 스크립트를 실행하기 전에 다른 매개 변수 값을 추가하거나 스크립트의 기본값을 변경할 수 있습니다.

    ```
    Param(
      [string] [Parameter(Mandatory=$true)] $ResourceGroupLocation,
      [string] $ResourceGroupName = 'AzureResourceGroup1',
      [switch] $UploadArtifacts,
      [string] $StorageAccountName,
      [string] $StorageAccountResourceGroupName,
      [string] $StorageContainerName = $ResourceGroupName.ToLowerInvariant() + '-stageartifacts',
      [string] $TemplateFile = '..\Templates\azuredeploy.json',
      [string] $TemplateParametersFile = '..\Templates\azuredeploy.parameters.json',
      [string] $ArtifactStagingDirectory = '..\bin\Debug\staging',
      [string] $AzCopyPath = '..\Tools\AzCopy.exe',
      [string] $DSCSourceFolder = '..\DSC'
    )
    ```

    |매개 변수|설명|
    |---|---|
    |$ResourceGroupLocation|리소스 그룹의 지역 또는 데이터 센터 위치(예: **미국 서부** 또는 **동아시아**)입니다.|
    |$ResourceGroupName|Azure 리소스 그룹의 이름입니다.|
    |$UploadArtifacts|시스템에서 아티팩트를 Azure에 업로드해야 하는지를 나타내는 이진 값입니다.|
    |$StorageAccountName|아티팩트가 업로드되는 Azure 저장소 계정의 이름입니다.|
    |$StorageAccountResourceGroupName|저장소 계정을 포함하는 Azure 리소스 그룹의 이름입니다.|
    |$StorageContainerName|아티팩트 업로드에 사용되는 저장소 컨테이너의 이름입니다.|
    |$TemplateFile|Azure 리소스 그룹 프로젝트의 배포 파일(`<app name>.json`)에 대한 경로입니다.|
    |$TemplateParametersFile|Azure 리소스 그룹 프로젝트의 매개 변수 파일(`<app name>.parameters.json`)에 대한 경로입니다.|
    |$ArtifactStagingDirectory|PowerShell 스크립트 루트 폴더를 포함하여, 아티팩트가 로컬에 업로드되는 시스템의 경로입니다. 이 경로는 절대 경로이거나 스크립트 위치에 대한 상대 경로일 수 있습니다.|
    |$AzCopyPath|PowerShell 스크립트 루트 폴더를 포함하여, AzCopy.exe 도구가 .zip 파일을 복사하는 경로입니다. 이 경로는 절대 경로이거나 스크립트 위치에 대한 상대 경로일 수 있습니다.|
    |$DSCSourceFolder|PowerShell 스크립트 루트 폴더를 포함하여, DSC(원하는 상태 구성) 원본 폴더의 경로입니다. 이 경로는 절대 경로이거나 스크립트 위치에 대한 상대 경로일 수 있습니다. 해당하는 경우, 자세한 내용은 [Azure PowerShell DSC(원하는 상태 구성) 확장 소개(영문)](http://blogs.msdn.com/b/powershell/archive/2014/08/07/introducing-the-azure-powershell-dsc-desired-state-configuration-extension.aspx)를 참조하세요.|

1.	아티팩트를 Azure에 업로드해야 하는지 확인합니다. 아니면, 11단계로 건너뜁니다. 그렇지 않으면, 다음 단계를 수행합니다.

1.	상대 경로 변수를 절대 경로로 변환합니다. 예를 들어, `..\Tools\AzCopy.exe`와 같은 경로를 `C:\YourFolder\Tools\AzCopy.exe`로 변경합니다. *ArtifactsLocationName* 및*ArtifactsLocationSasTokenName* 변수를 null로 초기화합니다. *ArtifactsLocation* 및 *SaSToken*은 템플릿에 대한 매개 변수가 될 수 있습니다. 매개 변수 파일을 읽은 후에 값이 null이면 스크립트는 그에 해당하는 값을 생성합니다.

    Azure 도구는 템플릿에 *\_artifactsLocation* 및 *\_artifactsLocationSasToken* 매개 변수 값을 사용하여 아티팩트를 관리합니다. PowerShell 스크립트가 이러한 이름의 매개 변수를 발견했지만 매개 변수 값이 제공되어 있지 않으면, 스크립트는 아티팩트를 업로드하고 해당 매개 변수에 적절한 값을 반환합니다. 그 후 `@OptionsParameters`를 통해 cmdlet에 전달합니다.

	|변수|설명|
    |---|---|
    |ArtifactsLocationName|Azure 아티팩트가 위치하는 경로입니다.|
    |ArtifactsLocationSasTokenName|서비스 버스 인증을 위해 스크립트에 의해 사용되는 SAS(공유 액세스 서명) 토큰 이름입니다. 자세한 내용은 [서비스 버스에서 공유 액세스 서명 인증](service-bus-shared-access-signature-authentication.md)을 참조하세요.|

	```
    if ($UploadArtifacts) {
    # Convert relative paths to absolute paths if needed
    $AzCopyPath = [System.IO.Path]::Combine($PSScriptRoot, $AzCopyPath)
    $ArtifactStagingDirectory = [System.IO.Path]::Combine($PSScriptRoot, $ArtifactStagingDirectory)
    $DSCSourceFolder = [System.IO.Path]::Combine($PSScriptRoot, $DSCSourceFolder)

    Set-Variable ArtifactsLocationName '_artifactsLocation' -Option ReadOnly
    Set-Variable ArtifactsLocationSasTokenName '_artifactsLocationSasToken' -Option ReadOnly

    $OptionalParameters.Add($ArtifactsLocationName, $null)
    $OptionalParameters.Add($ArtifactsLocationSasTokenName, $null)
    ```

1.	이 섹션은 <app name>.parameters.json 파일(“매개 변수 파일”이라고 함)에 이름이 **parameters**인 부모 노드가 있는지(`else` 블록 내에)를 확인합니다. 그렇지 않으면, 부모 노드가 없는 것입니다. 두 가지 형식 중 하나가 허용됩니다.
    
	```
    if ($JsonParameters -eq $null) {
            $JsonParameters = $JsonContent
        }
        else {
            $JsonParameters = $JsonContent.parameters
        }
    ```

1.	JSON 매개 변수 컬렉션을 반복합니다. *\_artifactsLocation* or *\_artifactsLocationSasToken*에 매개 변수 값이 할당된 경우에는 해당 값에 *$OptionalParameters* 매개 변수를 설정합니다. 이렇게 하면 사용자가 제공한 매개 변수 값을 스크립트가 의도치 않게 덮어쓰는 것을 방지합니다.

    ```
    $JsonParameters | Get-Member -Type NoteProperty | ForEach-Object {
        $ParameterValue = $JsonParameters | Select-Object -ExpandProperty $_.Name

        if ($_.Name -eq $ArtifactsLocationName -or $_.Name -eq $ArtifactsLocationSasTokenName) {
            $OptionalParameters[$_.Name] = $ParameterValue.value
        }
    }
    ```

1.	배포용 아티팩트를 보관하는 데 사용되는 저장소 계정 리소스에 대한 컨텍스트와 저장소 계정 키를 가져옵니다.

    ```
    $StorageAccountKey = (Get-AzureRMStorageAccountKey -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Key1

    $StorageAccountContext = (Get-AzureRmStorageAccount -ResourceGroupName $StorageAccountResourceGroupName -Name $StorageAccountName).Context
    ```

1.	PowerShell DSC를 사용하여 가상 컴퓨터를 구성하는 경우, 단일 zip 파일 내에 포함된 아티팩트가 DSC 확장에 필요합니다. 따라서 DSC 구성을 위해 .zip 보관 파일을 만듭니다. 이를 위해, $DSCSourceFolder가 존재하는지 확인합니다. DSC 구성이 있으면, 제거한 후 dsc.zip이라는 새 압축 파일을 만듭니다.

    ```
    # Create DSC configuration archive
    if (Test-Path $DSCSourceFolder) {
    Add-Type -Assembly System.IO.Compression.FileSystem
        $ArchiveFile = Join-Path $ArtifactStagingDirectory "dsc.zip"
        Remove-Item -Path $ArchiveFile -ErrorAction SilentlyContinue
        [System.IO.Compression.ZipFile]::CreateFromDirectory($DSCSourceFolder, $ArchiveFile)
    }
    ```

1.	매개 변수 파일에 Azure 아티팩트에 대한 경로가 제공되지 않으면, 아티팩트를 업로드할 때 사용할 PowerShell 스크립트 경로를 설정합니다. 이를 위해, 저장소 계정의 끝점 경로와 저장소 컨테이너 이름의 조합을 사용하여 경로를 만듭니다. 그 후 새 경로로 매개 변수 파일을 업데이트합니다.

    ```
    # Generate the value for artifacts location if it is not provided in the parameter file
    $ArtifactsLocation = $OptionalParameters[$ArtifactsLocationName]
    if ($ArtifactsLocation -eq $null) {
        $ArtifactsLocation = $StorageAccountContext.BlobEndPoint + $StorageContainerName
        $OptionalParameters[$ArtifactsLocationName] = $ArtifactsLocation
    }
    ```

1.	**AzCopy** 유틸리티(Azure 리소스 그룹 배포 프로젝트의 **Tools** 폴더에 포함된)를 사용하여 로컬 저장소 계정의 모든 파일을 온라인 Azure 저장소 계정으로 복사합니다. 이 단계에 실패하면 필요한 아티팩트 없이 배포에 성공할 가능성이 낮으므로 스크립트를 종료합니다.

    ```
    # Use AzCopy to copy files from the local storage drop path to the storage account container
    & $AzCopyPath """$ArtifactStagingDirectory""", $ArtifactsLocation, "/DestKey:$StorageAccountKey", "/S", "/Y", "/Z:$env:LocalAppData\Microsoft\Azure\AzCopy\$ResourceGroupName"
    if ($LASTEXITCODE -ne 0) { return }
    ```

1.	매개 변수 파일에 아티팩트 위치에 대한 SAS 토큰이 제공되지 않으면, 해당 토큰을 만들어서 온라인 저장소 컨테이너에 임시 읽기 전용 액세스를 제공합니다. 그 후 SAS 토큰을 명령줄에 “optionalParameter”로 전달합니다. 명령줄로 전달되는 모든 매개 변수는 매개 변수 파일에 제공되는 값보다 우선적으로 적용됩니다.

    ```
    # Generate the value for artifacts location SAS token if it is not provided in the parameter file
    $ArtifactsLocationSasToken = $OptionalParameters[$ArtifactsLocationSasTokenName]
    if ($ArtifactsLocationSasToken -eq $null) {
       # Create a SAS token for the storage container - this gives temporary read-only access to the container
       $ArtifactsLocationSasToken = New-AzureStorageContainerSASToken -Container $StorageContainerName -Context $StorageAccountContext -Permission r -ExpiryTime (Get-Date).AddHours(4)
       $ArtifactsLocationSasToken = ConvertTo-SecureString $ArtifactsLocationSasToken -AsPlainText -Force
       $OptionalParameters[$ArtifactsLocationSasTokenName] = $ArtifactsLocationSasToken
    }
    ```

1.  리소스 그룹이 없으면 만들고 템플릿과 매개 변수 파일에 배포를 성공하는 데 방해가 될만한 유효성 검사 오류가 있는지 확인합니다.

    ```
	# Create or update the resource group using the specified template file and template parameters file
    New-AzureRMResourceGroup -Name $ResourceGroupName -Location $ResourceGroupLocation -Verbose -Force -ErrorAction Stop

	Test-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterFile $TemplateParametersFile @OptionalParameters -ErrorAction Stop
    ```

1. 마지막으로 템플릿을 배포합니다. 이 코드는 타임스탬프를 사용하여 배포용 고유 이름을 만듭니다.

    ```
    New-AzureRMResourceGroupDeployment -Name ((Get-ChildItem $TemplateFile).BaseName + '-' + ((Get-Date).ToUniversalTime()).ToString('MMdd-HHmm')) `
        -ResourceGroupName $ResourceGroupName `
        -TemplateFile $TemplateFile `
        -TemplateParameterFile $TemplateParametersFile `
        @OptionalParameters `
        -Force -Verbose
    ```

## 리소스 그룹 배포

### Visual Studio에서 리소스 그룹 배포

1. Azure 리소스 그룹 프로젝트의 바로 가기 메뉴에서 **배포** > **새 배포**를 선택합니다.

    ![][0]

1. **리소스 그룹에 배포** 대화 상자의 드롭다운 목록 상자에서 배포할 기존 리소스 그룹을 선택하거나 **&lt;새로 만들기…&gt;**를 선택하여 새 리소스 그룹을 만듭니다.

    ![][1]

1. 메시지가 표시되면 **리소스 그룹 만들기** 대화 상자에 리소스 그룹 이름 및 위치를 입력한 후 **만들기** 단추를 선택합니다.

    ![][2]

1. **매개 변수 편집** 단추를 선택하여 **매개 변수 편집** 대화 상자를 열고 누락된 매개 변수 값이 있으면 입력합니다.

    ![][3]

	>[AZURE.NOTE] 필수 매개 변수에 값이 필요하면, 배포 시 이 대화 상자에 자동으로 표시됩니다.

    ![][4]

1. 매개 변수 값 입력을 완료하면 **저장** 단추를 선택한 후 **배포** 단추를 선택합니다.

    배포 스크립트(Deploy-AzureResourceGroup.ps1)가 실행되고 템플릿이 아티팩트와 함께 Azure에 배포됩니다.

### PowerShell을 사용하여 리소스 그룹 배포

Visual Studio 배포 명령 및 UI를 사용하지 않고 스크립트를 실행하려면 스크립트의 바로 가기 메뉴에서 **PowerShell ISE로 열기**를 선택합니다.

![][5]


## 명령 배포 예제

### 기본값을 사용하여 배포

이 예제는 기본 매개 변수 값을 사용하여 스크립트를 실행하는 방법을 보여줍니다. (위치 매개 변수에는 기본값이 없으므로, 사용자가 값을 제공해야 합니다.)

`.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus`

### 기본값을 재정의하는 배포

이 예제는 기본값과 다른 템플릿 및 매개 변수 파일을 배포하는 스크립트를 실행하는 방법을 보여줍니다.

```
.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation eastus –TemplateFile ..\templates\AnotherTemplate.json –TemplateParametersFile ..\templates\AnotherTemplate.parameters.json
```

### 스테이징을 위해 UploadArtifacts를 사용하여 배포

이 예제는 릴리스 폴더의 아티팩트를 업로드하고 기본값이 아닌 템플릿을 배포하는 스크립트를 실행하는 방법을 보여줍니다.

```
.\Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory ..\bin\release\staging
```

이 예제는 Visual Studio Online의 Azure PowerShell 작업에서 스크립트를 실행하는 방법을 보여줍니다.

```
$(Build.StagingDirectory)/AzureResourceGroup1/Scripts/Deploy-AzureResourceGroup.ps1 -StorageAccountName 'mystorage' -StorageAccountResourceGroupName 'Default-Storage-EastUS' -ResourceGroupName 'myResourceGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\windowsvirtualmachine.json' -TemplateParametersFile '..\templates\windowsvirtualmachine.parameters.json' -UploadArtifacts -ArtifactStagingDirectory $(Build.StagingDirectory)
```

## 다음 단계
[Azure 리소스 관리자 개요](resource-group-overview.md)를 참조하여 Azure 리소스 관리자에 대해 자세히 알아봅니다.

[0]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy1c.png
[1]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy2bc.png
[2]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy3bc.png
[3]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy4bc.png
[4]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy5c.png
[5]: ./media/vs-azure-tools-resource-groups-how-script-works/deploy6c.png

<!---HONumber=AcomDC_0204_2016-->