---
title: "가상 컴퓨터 크기 집합에 앱 배포"
description: "확장을 사용하여 Azure 가상 컴퓨터 확장 집합에 앱을 배포합니다."
services: virtual-machine-scale-sets
documentationcenter: 
author: thraka
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: adegeo
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: e6a5e3a378a5661c09f770a202c10d270f324447
ms.contentlocale: ko-kr
ms.lasthandoff: 06/01/2017

---

# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>가상 컴퓨터 확장 집합에 응용 프로그램 배포

이 문서에서는 확장 집합을 프로비전할 때 소프트웨어를 설치하는 여러 방법을 설명합니다.

가상 컴퓨터 확장 집합에 적용되는 일부 제한에 대해 설명하는 [확장 집합 디자인 개요](virtual-machine-scale-sets-design-overview.md)도 읽어 보시기 바랍니다.

## <a name="capture-and-reuse-an-image"></a>이미지 캡처 및 재사용

Azure에 갖고 있는 가상 컴퓨터를 사용하여 확장 집합에 사용할 기본 이미지를 준비할 수 있습니다. 이 프로세스는 저장소 계정에 관리되는 디스크를 만듭니다. 만들어진 디스크는 확장 집합의 기본 이미지로 참조할 수 있습니다. 

다음 단계를 수행합니다.

1. Azure 가상 컴퓨터 만들기
   * [Linux][linux-vm-create]
   * [Windows][windows-vm-create]

2. 가상 컴퓨터에 원격으로 연결하고 시스템을 연결에 맞게 사용자 지정합니다.

   원한다면 지금 응용 프로그램을 설치할 수 있습니다. 그러나 지금 응용 프로그램을 설치하면 응용 프로그램 업그레이드가 더 복잡해질 수 있습니다. 가장 먼저 응용 프로그램을 제거해야 할 수도 있기 때문입니다. 그 대신 이 단계에 따라 특정 런타임이나 운영 체제 기능처럼 응용 프로그램에 꼭 필요한 필수 구성 요소를 설치할 수 있습니다.

3. [Linux][linux-vm-capture] 또는 [Windows][windows-vm-capture]에 대한 "컴퓨터 캡처" 자습서의 내용을 따르세요.

4. 이전 단계에서 캡처한 이미지 URI를 사용하여 [가상 컴퓨터 확장 집합][vmss-create]을 만듭니다.

디스크에 대한 자세한 내용은 [Managed Disks 개요](../storage/storage-managed-disks-overview.md) 및 [연결된 데이터 디스크 사용](virtual-machine-scale-sets-attached-disks.md)을 참조하세요.

## <a name="install-when-the-scale-set-is-provisioned"></a>확장 집합이 프로비전되면 설치

가상 컴퓨터 확장 집합에 가상 컴퓨터 확장을 적용할 수 있습니다. 가상 컴퓨터 확장을 사용하여 확장 집합의 가상 컴퓨터를 하나의 전체 그룹으로 사용자 지정할 수 있습니다. 확장에 대한 자세한 내용은 [가상 컴퓨터 확장](../virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.

운영 체제가 Linux 기반인지 아니면 Windows 기반인지에 따라 세 가지 주요 확장을 사용할 수 있습니다.

### <a name="windows"></a>Windows

Windows 기반 운영 체제의 경우 **사용자 지정 스크립트 v1.8** 확장 또는 **PowerShell DSC** 확장을 사용할 수 있습니다.

#### <a name="custom-script"></a>Custom Script

사용자 지정 스크립트 확장은 확장 집합의 각 가상 컴퓨터 인스턴스에서 스크립트를 실행합니다. 구성 파일 또는 변수는 어떤 파일이 가상 컴퓨터에 다운로드된 후 어떤 명령이 실행되는지를 나타냅니다. 예를 들어 이것을 사용하여 설치 프로그램, 스크립트, 일괄 처리 파일 및 모든 실행 파일을 실행할 수 있습니다.

PowerShell은 설정에 해시 테이블을 사용합니다. 이 예제에서는 IIS를 설치하는 PowerShell 스크립트를 실행하는 사용자 지정 스크립트 확장을 구성합니다.

```powershell
# Setup extension configuration hashtable variable
$customConfig = @{
  "fileUris" = @("https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1");
  "commandToExecute" = "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> `"%TEMP%\StartupLog.txt`" 2>&1";
};

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Compute -Type CustomScriptExtension -TypeHandlerVersion 1.8 -Name "customscript1" -Setting $customConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "MyVmssTest143"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>민감한 정보가 포함될 수 있는 설정에는 `-ProtectedSetting` 스위치를 사용합니다.

---------


Azure CLI는 설정에 json 파일을 사용합니다. 이 예제에서는 IIS를 설치하는 PowerShell 스크립트를 실행하는 사용자 지정 스크립트 확장을 구성합니다. 다음 json 파일을 _settings.json_으로 저장합니다.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/temp/install-iis.ps1"
  ],
  "commandToExecute": "PowerShell -ExecutionPolicy Unrestricted .\install-iis.ps1 >> \"%TEMP%\StartupLog.txt\" 2>&1"
}
```

그런 다음 이 Azure CLI 명령을 실행합니다.

```azurecli
az vmss extension set --publisher Microsoft.Compute --version 1.8 --name CustomScriptExtension --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>민감한 정보가 포함될 수 있는 설정에는 `--protected-settings` 스위치를 사용합니다.

### <a name="powershell-dsc"></a>PowerShell DSC

PowerShell DSC를 사용하여 확장 집합 vm 인스턴스를 사용자 지정할 수 있습니다. **Microsoft.Powershell**을 통해 게시된 **DSC**는 제공된 DSC 구성을 각 가상 컴퓨터 인스턴스에서 실행합니다. 구성 파일 또는 변수는 확장에 *.zip* 패키지의 위치 및 실행할 _스크립트-함수_ 조합을 알려줍니다.

PowerShell은 설정에 해시 테이블을 사용합니다. 이 예제에서는 IIS를 설치하는 DSC 패키지를 배포합니다.

```powershell
# Setup extension configuration hashtable variable
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Add the extension to the config
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmssConfig -Publisher Microsoft.Powershell -Type DSC -TypeHandlerVersion 2.24 -Name "dsc1" -Setting $dscConfig

# Send the new config to Azure
Update-AzureRmVmss -ResourceGroupName $rg -Name "myscaleset1"  -VirtualMachineScaleSet $vmssConfig
```

>[!IMPORTANT]
>민감한 정보가 포함될 수 있는 설정에는 `-ProtectedSetting` 스위치를 사용합니다.

-----------

Azure CLI는 설정에 json을 사용합니다. 이 예제에서는 IIS를 설치하는 DSC 패키지를 배포합니다. 다음 json 파일을 _settings.json_으로 저장합니다.

```json
{
  "wmfVersion": "latest",
  "configuration": {
    "url": "https://github.com/MicrosoftDocs/azure-cloud-services-files/raw/temp/dsc.zip",
    "script": "configure-http.ps1",
    "function": "WebsiteTest"
  }
}
```

그런 다음 이 Azure CLI 명령을 실행합니다.

```azurecli
az vmss extension set --publisher Microsoft.Powershell --version 2.24 --name DSC --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>민감한 정보가 포함될 수 있는 설정에는 `--protected-settings` 스위치를 사용합니다.

### <a name="linux"></a>Linux

Linux는 **사용자 지정 스크립트 v2.0**을 사용하거나 만드는 동안 **cloud-init**을 사용할 수 있습니다.

사용자 지정 스크립트는 가상 컴퓨터 인스턴스에 파일을 다운로드하고 명령을 실행하는 간단한 확장입니다.

#### <a name="custom-script"></a>Custom Script

다음 json 파일을 _settings.json_으로 저장합니다.

```json
{
  "fileUris": [
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/installserver.sh",
    "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-bottle-autoscale/workserver.py"
  ],
  "commandToExecute": "bash installserver.sh"
}
```

Azure CLI를 사용하여 기존 가상 컴퓨터 확장 집합에 이 확장을 추가합니다. 확장 집합의 각 가상 컴퓨터는 자동으로 확장을 실행합니다.

```azurecli
az vmss extension set --publisher Microsoft.Azure.Extensions --version 2.0 --name CustomScript --resource-group myResourceGroup --vmss-name myScaleSet --settings @settings.json
```

>[!IMPORTANT]
>민감한 정보가 포함될 수 있는 설정에는 `--protected-settings` 스위치를 사용합니다.

#### <a name="cloud-init"></a>Cloud-Init

Cloud-Init은 확장 집합을 만들 때 사용합니다. 먼저 _cloud-init.txt_라는 로컬 파일을 만들고 이 파일에 구성을 추가합니다. 예제는 [이 gist](https://gist.github.com/Thraka/27bd66b1fb79e11904fb62b7de08a8a6#file-cloud-init-txt)를 참조하세요.

Azure CLI를 사용하여 확장 집합을 만듭니다. `--custom-data` 필드는 cloud-init 스크립트의 파일 이름을 허용합니다.

```azurecli
az vmss create \
  --resource-group myResourceGroupScaleSet \
  --name myScaleSet \
  --image Canonical:UbuntuServer:14.04.4-LTS:latest \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys      
```

## <a name="how-do-i-manage-application-updates"></a>응용 프로그램 업데이트를 관리하려면 어떻게 해야 하나요?

확장을 통해 응용 프로그램을 배포한 경우 어떤 방식으로든 확장 정의를 변경합니다. 이렇게 변경하면 모든 가상 컴퓨터 인스턴스에 확장이 다시 배포됩니다. 확장에 대한 무언가를 **반드시** 변경해야 합니다. 예를 들어 참조 파일의 이름을 변경합니다. 그렇지 않으면 Azure는 확장이 변경된 것으로 인식하지 않습니다.

응용 프로그램을 사용자 고유의 운영 체제 이미지에 직접 포함한 경우 응용 프로그램 업데이트에 자동 배포 파이프라인을 사용합니다. 미리 구성된 확장 집합을 용이하게 프로덕션으로 빠르게 교체할 수 있도록 아키텍처를 디자인합니다. 이 방법의 예로 [Azure Spinnaker 드라이버 작업](https://github.com/spinnaker/deck/tree/master/app/scripts/modules/azure) - [http://www.spinnaker.io/](http://www.spinnaker.io/)이 있습니다.

[Packer](https://www.packer.io/) 및 [Terraform](https://www.terraform.io/)은 Azure Resource Manager를 지원하므로, 이미지를 "코드로" 정의하고 Azure에서 빌드한 다음 해당 VHD를 확장 집합에서 사용할 수 있습니다. 그러나 사용자가 Marketplace에서 직접 조작하지 않으므로 확장/사용자 지정 스크립트가 좀 더 중요한 경우 이렇게 하면 Marketplace 이미지에 문제가 될 수 있습니다.

## <a name="what-happens-when-a-scale-set-scales-out"></a>확장 집합이 확장되면 어떻게 되나요?
확장 집합에 하나 이상의 가상 컴퓨터를 추가하면 응용 프로그램이 자동으로 설치됩니다. 예를 들어 확장 집합에 확장이 정의되면 가상 컴퓨터가 만들어질 때마다 새 가상 컴퓨터에서 해당 확장이 실행됩니다. 확장 집합이 사용자 지정 이미지를 기반으로 하는 경우 새 가상 컴퓨터는 원본 사용자 지정 이미지의 복사본이 됩니다. 확장 집합 가상 컴퓨터가 컨테이너 호스트인 경우 사용자 지정 스크립트 확장의 컨테이너를 로드하는 시작 코드가 있을 수 있습니다. 또는 확장이 클러스터 조정자에 등록하는 Azure Container Service 같은 에이전트를 설치할 수 있습니다.


## <a name="how-do-you-roll-out-an-os-update-across-update-domains"></a>업데이트 도메인 간에 OS 업데이트를 롤아웃하려면 어떻게 해야 할까요?
가상 컴퓨터 확장 집합을 계속 실행하면서 OS 이미지를 업데이트하려 한다고 가정해 봅시다. PowerShell 및 Azure CLI는 한 번에 하나의 가상 컴퓨터에서 가상 컴퓨터 이미지를 업데이트할 수 있습니다. 또한 가상 컴퓨터 확장 집합에서 운영 체제 업그레이드를 수행하는 데 사용할 수 있는 옵션에 대한 자세한 내용은 [가상 컴퓨터 확장 집합 업그레이드](./virtual-machine-scale-sets-upgrade-scale-set.md) 문서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [PowerShell을 사용하여 확장 집합 관리](virtual-machine-scale-sets-windows-manage.md)
* [확장 집합 템플릿 만들기](virtual-machine-scale-sets-mvss-start.md)


[linux-vm-create]: ../virtual-machines/linux/tutorial-manage-vm.md
[windows-vm-create]: ../virtual-machines/windows/tutorial-manage-vm.md
[linux-vm-capture]: ../virtual-machines/linux/capture-image.md
[windows-vm-capture]: ../virtual-machines/windows/capture-image.md 
[vmss-create]: virtual-machine-scale-sets-create.md


