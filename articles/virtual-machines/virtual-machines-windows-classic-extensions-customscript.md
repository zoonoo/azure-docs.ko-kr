---
title: "Windows VM의 사용자 지정 스크립트 확장 | Microsoft Docs"
description: "사용자 지정 스크립트 확장을 통해 원격 Windows VM에서 PowerShell 스크립트를 실행하여 Azure VM 구성 작업을 자동화합니다."
services: virtual-machines-windows
documentationcenter: 
author: kundanap
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/06/2015
ms.author: kundanap
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 5a66b7a5454ae84c656e5e38e6e7072a5de49ef0


---
# <a name="custom-script-extension-for-windows-virtual-machines"></a>Windows 가상 컴퓨터용 사용자 지정 스크립트 확장
이 문서에서는 Azure Service Management API와 함께 Azure PowerShell cmdlet을 사용하여 Windows VM에서 사용자 지정 스크립트 확장을 사용하는 방법을 간략하게 설명합니다.

VM(가상 컴퓨터) 확장은 VM의 기능을 확장하기 위해 Microsoft 및 신뢰할 수 있는 타사 게시자가 작성하는 기능입니다. VM 확장의 개요는 [Azure VM 확장 프로그램 및 기능](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[Resource Manager 모델을 사용하여 이러한 단계를 수행](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)하는 방법을 알아봅니다.

## <a name="custom-script-extension-overview"></a>사용자 지정 스크립트 확장 개요
Windows용 사용자 지정 스크립트 확장을 사용하면 로그인하지 않고도 원격 VM에서 PowerShell 스크립트를 실행할 수 있습니다. 스크립트는 VM을 프로비전한 이후에 실행할 수도 있고 추가 포트를 열지 않고도 VM 수명 주기 중에 언제든지 실행할 수 있습니다. 사용자 지정 스크립트 확장은 VM 프로비전된 후에 작업에서 추가 소프트웨어를 실행, 설치 및 구성할 때 가장 흔히 사용됩니다.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>사용자 지정 스크립트 확장을 실행하기 위한 필수 조건
1. <a href="http://azure.microsoft.com/downloads" target="_blank">Azure PowerShell cmdlet</a> 버전 0.8.0 이상을 설치합니다.
2. 기존 VM에서 스크립트를 실행하는 경우 VM에서 VM 에이전트를 사용할 수 있는지 확인합니다. 설치되지 않은 경우 다음 [단계](virtual-machines-windows-classic-agents-and-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)를 따릅니다. Azure 포털에서 VM을 만든 경우 VM 에이전트가 기본적으로 설치됩니다.
3. VM에서 실행할 스크립트를 Azure 저장소에 업로드합니다. 단일 컨테이너 또는 여러 저장소 컨테이너의 스크립트를 업로드할 수 있습니다.
4. 확장을 통해 시작되는 엔트리 스크립트가 다른 스크립트를 시작하는 방식으로 스크립트를 작성해야 합니다.

## <a name="custom-script-extension-scenarios"></a>사용자 지정 스크립트 확장 시나리오
### <a name="upload-files-to-the-default-container"></a>기본 컨테이너에 파일 업로드
다음 예제에서는 구독 기본 계정의 저장소 컨테이너에 스크립트가 있는 경우 VM에서 해당 스크립트를 실행하는 방법을 보여 줍니다. ContainerName에 스크립트를 업로드합니다. **Get-AzureSubscription –Default** 명령을 사용하여 기본 저장소 계정을 확인할 수 있습니다.

다음 예제에서는 VM을 만들지만 기존 VM에서도 동일한 시나리오를 실행할 수 있습니다.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>기본 저장소 컨테이너가 아닌 컨테이너에 파일 업로드
이 시나리오에서는 스크립트 및 파일 업로드용으로 같은 구독이나 다른 구독 내의 기본 저장소가 아닌 저장소 컨테이너를 사용하는 방법을 보여 줍니다. 이 예제에서는 기존 VM을 사용하지만 새 VM을 만들 때도 같은 작업을 수행할 수 있습니다.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>다른 저장소 계정의 여러 컨테이너에 스크립트 업로드
  스크립트 파일이 여러 컨테이너에 저장되어 있는 경우 해당 스크립트를 실행하려면 파일의 전체 SAS(공유 액세스 서명) URL을 제공해야 합니다.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Azure 포털에서 사용자 지정 스크립트 확장 추가
<a href="https://portal.azure.com/ " target="_blank">Azure 포털</a>에서 VM으로 이동한 다음 실행할 스크립트 파일을 지정하여 확장을 추가합니다.

  ![스크립트 파일 지정][5]

### <a name="uninstall-the-custom-script-extension"></a>사용자 지정 스크립트 확장 제거
다음 명령을 사용하여 VM에서 사용자 지정 스크립트 확장을 제거할 수 있습니다.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>템플릿과 함께 사용자 지정 스크립트 확장 사용
Azure Resource Manager 템플릿에서 사용자 지정 스크립트 확장을 사용하는 방법에 대해 알아보려면 [Azure Resource Manager 템플릿을 사용하여 Windows VM에 대한 사용자 지정 스크립트 확장 사용](virtual-machines-windows-extensions-customscript.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png



<!--HONumber=Nov16_HO3-->


