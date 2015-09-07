<properties
   pageTitle="Windows의 사용자 지정 스크립트 확장 | Microsoft Azure"
	description="Windows에서 사용자 지정 스크립트 확장을 사용하여 Azure 가상 컴퓨터 구성 작업 자동화"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/06/2015"
	ms.author="kundanap"/>

# Windows용 사용자 지정 스크립트 확장

이 문서에서는 Azure PowerShell cmdlet을 사용하여 Windows에서 사용자 지정 스크립트 확장을 사용하는 방법을 간략하게 설명합니다.


VM(가상 컴퓨터) 확장은 VM의 기능을 확장하기 위해 Microsoft 및 신뢰할 수 있는 타사 게시자가 작성하는 기능입니다. VM 확장의 개요는 <a href="https://msdn.microsoft.com/library/azure/dn606311.aspx" target="_blank">Azure VM 확장 프로그램 및 기능</a>을 참조하세요.

## 사용자 지정 스크립트 확장 개요

Windows용 사용자 지정 스크립트 확장을 사용하면 원격 VM에 로그인하지 않고도 해당 가상 컴퓨터에서 PowerShell 스크립트를 실행할 수 있습니다. 스크립트는 VM을 프로비전한 이후에 실행할 수도 있고 VM 수명 주기 중에 언제든지 실행할 수 있으며 VM에서 추가 포트를 열 필요가 없습니다. 사용자 지정 스크립트 확장은 VM 프로비전된 후에 작업에서 추가 소프트웨어를 실행, 설치 및 구성할 때 가장 흔히 사용됩니다.

### 사용자 지정 스크립트 확장을 실행하기 위한 필수 조건

1. <a href="http://azure.microsoft.com/downloads" target="_blank">여기</a>에서 Azure PowerShell Cmdlets 버전 0.8.0 이후를 설치합니다.
2. 기존 VM에서 스크립트를 실행하려는 경우, VM에서 VM 에이전트를 사용하도록 설정되어 있는지 확인하고, 사용하도록 설정되어 있지 않으면 이 <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">문서</a>의 지침에 따라 설치합니다.
3. VM에서 실행할 스크립트를 Azure 저장소에 업로드합니다. 단일 컨테이너 또는 여러 저장소 컨테이너의 스크립트를 업로드할 수 있습니다.
4. 확장을 통해 시작되는 엔트리 스크립트가 다른 스크립트를 시작하는 방식으로 스크립트를 작성해야 합니다.

## 사용자 지정 스크립트 확장 시나리오

### 기본 컨테이너에 파일 업로드

구독 기본 계정의 저장소 컨테이너에 스크립트가 있는 경우 다음 예제는 VM에서 해당 스크립트를 실행하는 방법을 보여 줍니다. ContainerName에 스크립트를 업로드합니다. **Get-AzureSubscription –Default** cmdlet를 사용하여 기본 저장소 계정을 확인할 수 있습니다.

다음 예제에서는 새 VM을 만들지만 기존 VM에서도 동일한 시나리오를 실행할 수 있습니다.

    # create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script Extension to the VM. The container name refer to the storage container which contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### 기본 저장소 컨테이너가 아닌 컨테이너에 파일 업로드

이 시나리오에서는 스크립트 및 파일 업로드용으로 같은 구독이나 다른 구독 내의 기본 저장소가 아닌 저장소를 사용하는 방법을 보여 줍니다. 여기서는 기존 VM을 사용하지만 새 VM을 만들 때도 같은 작업을 수행할 수 있습니다.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### 다른 저장소 계정의 여러 컨테이너에 스크립트 업로드

  스크립트 파일이 여러 컨테이너에 저장되어 있는 경우 해당 스크립트를 실행하려면 파일의 전체 SAS URL을 제공해야 합니다.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### 포털에서 사용자 지정 스크립트 확장 추가

<a href="https://portal.azure.com/ " target="_blank">Azure Preview 포털</a>에서 VM으로 이동한 다음 실행할 스크립트 파일을 지정하여 확장을 추가합니다.

  ![][5]


### 사용자 지정 스크립트 확장 제거

다음 명령을 사용하여 VM에서 사용자 지정 스크립트 확장을 제거할 수 있습니다.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### 템플릿에 사용자 지정 스크립트 확장 사용

사용자 지정 스크립트 확장을 템플릿에 사용하는 방법을 확인하려면 [여기] 문서를 참조하세요.(virtual-machines-extensions-customscript -with template.md)

<!--Image references-->
[5]: ./media/virtual-machines-extensions-customscript/addcse.png

<!---HONumber=August15_HO9-->