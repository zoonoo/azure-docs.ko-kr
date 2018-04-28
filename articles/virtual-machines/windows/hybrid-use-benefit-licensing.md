---
title: Windows Server용 Azure Hybrid Benefit | Microsoft Docs
description: Azure에 온-프레미스 라이선스를 가져오기 위해 Windows Software Assurance 혜택을 최대화하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: kmouss
manager: jeconnoc
editor: ''
ms.assetid: 332583b6-15a3-4efb-80c3-9082587828b0
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2017
ms.author: kmouss
ms.openlocfilehash: bfad3ff71be07026cf4a7dd6ad75df399349f844
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit
Software Assurance 고객은 Windows Server용 Azure Hybrid Benefit을 통해 온-프레미스 Windows Server 라이선스를 사용하고 Azure에서 Windows 가상 머신을 실행하여 비용을 절감할 수 있습니다. Windows Server용 Azure Hybrid Benefit을 사용하여 모든 Azure 지원 플랫폼 Windows Server 이미지나 Windows 사용자 지정 이미지에서 새로운 가상 머신을 배포할 수 있습니다. 이 문서에서는 Windows Server용 Azure 하이브리드 혜택을 통해 새 VM을 배포하는 방법과 기존 실행 VM을 업데이트하는 방법에 대한 단계를 살펴봅니다. Windows Server용 Azure Hybrid Benefit 라이선스 및 비용 절감에 대한 자세한 내용은 [Windows Server용 Azure Hybrid Benefit 라이선스 페이지](https://azure.microsoft.com/pricing/hybrid-use-benefit/)를 참조하세요.

> [!IMPORTANT]
> Azure Marketplace의 기업 계약으로 고객을 위해 게시된 기존 '[허브]' Windows Server 이미지는 2017년 9월 11일을 기준으로 사용 중지되었습니다. Windows Server용 Azure Hybrid Benefit에 대한 "비용 절감" 옵션으로 표준 Windows Server를 사용하세요. 자세한 내용은 이 [문서](https://support.microsoft.com/en-us/help/4036360/retirement-azure-hybrid-use-benefit-images-for-ea-subscriptions)를 참조하세요.
>

> [!NOTE]
> SQL Server 또는 타사 마켓플레이스 이미지 등과 같은 추가 소프트웨어에 대해 청구되는 VM에서 Windows Server용 Azure 하이브리드 혜택을 사용하는 기능이 배포되고 있습니다. ‘LicenseType’ 속성 변경 허용 안 됨 등의 409 오류가 발생하는 것은 해당 지역에서 지원되지 않을 수 있는, 추가 소프트웨어 비용이 있는 새 Windows Server VM을 변환하거나 배포하고 있는 것입니다. 변환을 수행하기 위해 포털 구성 옵션을 찾으려고 하지만 해당 VM에 대한 구성 옵션을 찾을 수 없는 경우에도 동일합니다.
>


> [!NOTE]
> 클래식 VM의 경우 온-프레미스 사용자 지정 이미지로부터의 새 VM 배포만 지원됩니다. 이 문서에서 지원되는 기능을 활용하려면 먼저 클래식 VM을 리소스 관리자 모델로 마이그레이션해야 합니다.
>


## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit 사용 방법
몇 가지 방법으로 Windows Server용 Azure Hybrid Benefit을 사용할 수 있습니다.

1. 제공된 [Azure Marketplace의 Windows Server 이미지](#https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.WindowsServer?tab=Overview) 중 하나를 통해 VM을 배포할 수 있습니다.
2. [사용자 지정 VM을 업로드](#upload-a-windows-vhd)하거나 [Resource Manager 템플릿](#deploy-a-vm-via-resource-manager) 또는 [Azure PowerShell을 사용하여 배포](#detailed-powershell-deployment-walkthrough)할 수 있습니다.
3. Windows Server에 대해 Azure 하이브리드 혜택 또는 종량제를 통해 실행하는 방법 간에 기존 VM을 토글 및 변환할 수 있습니다.
4. Windows Server용 Azure Hybrid Benefit을 통해서도 새 가상 머신 확장 집합을 배포할 수 있습니다.

> [!NOTE]
> 기존 가상 머신 확장 집합을 변환하여 Windows Server용 Azure 하이브리드 혜택을 사용하는 것은 지원되지 않습니다.
>

## <a name="deploy-a-vm-from-a-windows-server-marketplace-image"></a>Windows Server Marketplace 이미지에서 VM 배포
Azure Marketplace에서 사용 가능한 모든 Windows Server 이미지는 Windows Server용 Azure Hybrid Benefit에서 활성화됩니다. 예를 들어 Windows Server 2016, Windows Server 2012R2, Windows Server 2012 및Windows Server 2008SP1 등이 있습니다. 이러한 이미지는 Azure Portal, Resource Manager 템플릿 또는 Azure PowerShell 및 기타 SDK 등에서 직접 배포할 수 있습니다.

Azure Portal에서 직접 이러한 이미지를 배포할 수 있습니다. Resource Manager 템플릿 및 Azure PowerShell에서 사용하려면 다음과 같은 이미지 목록을 봅니다.

### <a name="powershell"></a>PowerShell
```powershell
Get-AzureRmVMImagesku -Location westus -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```
[PowerShell을 사용하여 Windows 가상 머신 만들기](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json) 단계에 따르고 LicenseType = "Windows_Server"를 전달할 수 있습니다. 이 옵션을 사용하면 Azure에서 기존 Windows Server 라이선스를 사용할 수 있습니다.

### <a name="portal"></a>포털
[Azure Portal을 사용하여 Windows 가상 컴퓨터를 만들기](#https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal) 단계에 따르고 기존 Windows Server 라이선스를 사용하는 옵션을 선택할 수 있습니다.

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Windows Server 용 Azure 하이브리드 혜택을 사용하여 기존 VM 변환
Windows Server용 Azure 하이브리드 혜택을 활용하기 위해 변환할 기존 VM이 있는 경우 다음과 같이 VM의 라이선스 유형을 업데이트할 수 있습니다.

### <a name="convert-to-using-azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure 하이브리드 혜택 사용으로 변환
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "Windows_Server"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="convert-back-to-pay-as-you-go"></a>다시 종량제로 변환
```powershell
$vm = Get-AzureRmVM -ResourceGroup "rg-name" -Name "vm-name"
$vm.LicenseType = "None"
Update-AzureRmVM -ResourceGroupName rg-name -VM $vm
```

### <a name="portal"></a>포털
Portal VM 블레이드에서 “구성” 옵션을 선택하여 Azure 하이브리드 혜택을 사용하고 “Azure 하이브리드 혜택”을 토글하도록 VM을 업데이트할 수 있습니다.

> [!NOTE]
> "구성" 아래 "Azure 하이브리드 혜택"을 토글하는 옵션이 보이지 않으면 선택한 VM 유형(예: SQL Sever 또는 Azure Marketplace 타사 소프트웨어와 같이 추가적인 유료 소프트웨어가 있는 이미지 또는 사용자 지정 이미지에서 빌드한 VM)에 대해 아직 지원되지 않기 때문입니다.
>

## <a name="upload-a-windows-server-vhd"></a>Windows Server VHD 업로드
Azure에서 Windows Server VM을 배포하려면 먼저 기본 Windows 빌드를 포함하는 VHD를 만들어야 합니다. 이 VHD는 Azure에 업로드하기 전에 Sysprep을 통해 적절하게 준비되어야 합니다. 자세한 내용은 [VHD 요구 사항 및 Sysprep 프로세스](upload-generalized-managed.md) 및 [서버 역할에 대한 Sysprep 지원](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)을 참조하세요. Sysprep를 실행하기 전에 VM을 백업합니다. 

VHD를 준비했으면 다음과 같이 Azure Storage 계정에 VHD를 업로드합니다.

```powershell
Add-AzureRmVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```

> [!NOTE]
> Microsoft SQL Server, SharePoint 서버 및 Dynamics는 또한 Software Assurance 라이선스를 활용할 수 있습니다. Windows Server 이미지를 준비하려면 응용 프로그램 구성 요소를 설치하고 라이선스 키를 적절하게 제공한 다음, 디스크 이미지를 Azure에 업로드합니다. 응용 프로그램과 함께 [Sysprep를 사용하여 SQL Server 설치에 대한 고려 사항](https://msdn.microsoft.com/library/ee210754.aspx) 또는 [SharePoint Server 2016 참조 이미지 빌드(Sysprep)](http://social.technet.microsoft.com/wiki/contents/articles/33789.build-a-sharepoint-server-2016-reference-image-sysprep.aspx)와 같은 Sysprep 실행에 대한 적합한 설명서를 검토합니다.
>
>

자세한 내용은 [Azure에 VHD 업로드 프로세스](upload-generalized-managed.md#upload-the-vhd-to-your-storage-account)를 참조하세요.

## <a name="deploy-a-vm-via-resource-manager-template"></a>Resource Manager 템플릿을 통해 VM 배포
Resource Manager 템플릿 내에서 `licenseType` 추가 매개 변수를 지정해야 합니다. [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)에 대해 자세히 알아볼 수 있습니다. Azure에 VHD를 업로드하고 나면 Resource Manager 템플릿을 편집하여 계산 공급자의 일부로 라이선스 유형을 포함하고 정상적으로 템플릿을 배포합니다.

```json
"properties": {  
   "licenseType": "Windows_Server",
   "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
   }
```

## <a name="deploy-a-vm-via-powershell-quickstart"></a>PowerShell 빠른 시작을 통해 VM 배포
PowerShell을 통해 Windows Server VM을 배포할 때는 `-LicenseType` 추가 매개 변수가 있습니다. Azure에 VHD를 업로드하고 나면 다음과 같이 `New-AzureRmVM`을 사용하여 VM을 만들고 라이선싱 형식을 지정합니다.

Windows Server:
```powershell
New-AzureRmVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Server"
```

다른 [리소스 관리자 및 PowerShell을 사용하여 Windows VM 만들기](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 단계에 대한 더 상세한 가이드를 확인할 수 있습니다.

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>VM이 라이선싱 혜택을 사용하고 있는지 확인
PowerShell 또는 Resource Manager 템플릿 또는 포털을 통해 VM을 배포한 후 다음과 같이 `Get-AzureRmVM`을 사용하여 라이선스 형식을 확인합니다.

```powershell
Get-AzureRmVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Windows Server의 경우 다음 예제와 유사하게 출력됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

이 출력은 Windows Server용 Azure Hybrid Benefit 라이선스 없이 배포된 다음 VM과 대비됩니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="list-all-azure-hybrid-benefit-for-windows-server-vms-in-a-subscription"></a>구독의 모든 Windows Server용 Azure Hybrid Benefit 나열

Windows Server용 Azure Hybrid Benefit으로 배포된 모든 가상 머신을 확인 및 산출하려면 구독에서 다음 명령을 실행할 수 있습니다.

```powershell
$vms = Get-AzureRMVM 
foreach ($vm in $vms) {"VM Name: " + $vm.Name, "   Azure Hybrid Benefit for Windows Server: "+ $vm.LicenseType}
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Windows Server용 Azure Hybrid Benefit을 통해 새 가상 머신 확장 집합을 배포
가상 머신 확장 집합 Resource Manager 템플릿 내에서 `licenseType` 추가 매개 변수를 지정해야 합니다. [Azure Resource Manager 템플릿 작성](../../resource-group-authoring-templates.md)에 대해 자세히 알아볼 수 있습니다. 확장 집합 virtualMachineProfile의 일부로 licenseType 속성을 포함하고 일반적인 방법으로 템플릿을 배포하도록 Resource Manager 템플릿을 편집합니다. 2016 Windows Server 이미지를 사용하는 다음 예제를 참조하세요.


```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
[가상 머신 확장 집합을 만들어 배포하고](#https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-create) LicenseType 속성을 설정할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure 하이브리드 혜택을 사용하여 비용을 절감하는 방법](https://azure.microsoft.com/pricing/hybrid-use-benefit/)에 대해 자세히 알아봅니다.

[Windows Server용 Azure 하이브리드 혜택 라이선스 세부 지침](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)에 대해 자세히 알아봅니다.

[Resource Manager 템플릿 사용](../../azure-resource-manager/resource-group-overview.md)에 대해 자세히 알아봅니다.

[Windows Server용 Azure 하이브리드 혜택 및 Azure Site Recovery를 사용하여 응용 프로그램을 Azure로 훨씬 간편하게 마이그레이션하는 방법](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)에 대해 자세히 알아봅니다.

[다중 테넌트 호스팅 권한으로 Azure에서 Windows 10](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)에 대해 자세히 알아봅니다.

[질문과 대답](#https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)을 자세히 알아봅니다.
