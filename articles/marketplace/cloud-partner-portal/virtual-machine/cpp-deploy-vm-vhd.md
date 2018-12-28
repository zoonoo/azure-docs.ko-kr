---
title: VHD에서 Azure Marketplace용 VM 배포 | Microsoft Docs
description: Azure에서 배포한 VHD에서 VM을 등록하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 9157ce7f8f16bc60a6d5c16fa992a5402cf2d7ad
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190733"
---
# <a name="deploy-a-vm-from-your-vhds"></a>VHD에서 VM 배포

이 섹션에서는 Azure에서 배포한 VHD(가상 하드 디스크)를 통해 VM(가상 머신)을 배포하는 방법을 설명합니다.  필요한 도구 및 이 도구를 사용하여 사용자 VM 이미지를 만든 다음, PowerShell 스크립트를 사용하여 Azure에 배포하는 방법을 나열합니다.

VHD(가상 하드 디스크), 즉 일반화된 운영 체제 VHD 및 0개 이상의 데이터 디스크 VHD가 Azure 저장소 계정에 업로드되면 사용자 VM 이미지로 등록할 수 있습니다. 그런 다음 해당 이미지를 테스트할 수 있습니다. 운영 체제 VHD는 일반화되므로 VHD URL을 제공하여 VM을 직접 배포할 수 없습니다.

VM 이미지에 대해 자세히 알아보려면 다음 블로그 게시물을 참조하세요.

- [VM 이미지](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM 이미지 PowerShell '방법'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="prerequisite-install-the-necessary-tools"></a>필수 조건: 필요한 도구 설치

아직 수행하지 않은 경우 다음 지침에 따라 Azure PowerShell 및 Azure CLI를 설치합니다.

- [PowerShellGet으로 Azure PowerShell을 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>배포 단계

사용자 VM 이미지를 만들고 배포하려면 다음 단계를 사용합니다.

1. 사용자 VM 이미지를 만듭니다. 이미지를 캡처하고 일반화하는 작업을 포함합니다. 
2. 인증서를 만들어 새 Azure Key Vault에 저장합니다. VM에 대한 보안 WinRM 연결을 설정하려면 인증서가 필요합니다.  Azure Resource Manager 템플릿 및 Azure PowerShell 스크립트가 제공됩니다. 
3. 제공된 템플릿 및 스크립트를 사용하여 사용자 VM 이미지에서 VM을 배포합니다.

VM이 배포되면 [VM 이미지를 인증](./cpp-certify-vm.md)할 준비가 되었습니다.

2.  **새로 만들기**를 클릭하고, **템플릿 배포**를 검색한 다음, **편집기에서 사용자 고유의 템플릿 작성**을 선택합니다.  <br/>
  ![Azure Portal에서 VHD 배포 템플릿 작성](./media/publishvm_021.png)

3. 이 [JSON 템플릿](./cpp-deploy-json-template.md)을 복사하여 편집기에 붙여넣고, **저장**을 클릭합니다. <br/>
  ![Azure Portal에서 VHD 배포 템플릿 저장](./media/publishvm_022.png)

4. 표시된 **사용자 지정 배포** 속성 페이지에 대한 매개 변수 값을 제공합니다.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **매개 변수**              |   **설명**                                                            |
   |  -------------              |   ---------------                                                            |
   | 사용자 저장소 계정 이름   | 일반화된 VHD가 있는 저장소 계정 이름                    |
   | 사용자 저장소 컨테이너 이름 | 일반화된 VHD가 있는 컨테이너 이름                          |
   | 공용 IP에 대한 DNS 이름      | 공용 IP DNS 이름                                                           |
   | 관리자 사용자 이름             | 새 VM에 대한 관리자 계정의 사용자 이름                                  |
   | 관리자 암호              | 새 VM에 대한 관리자 계정의 암호                                  |
   | OS 종류                     | VM 운영 체제: `Windows` \| `Linux`                                    |
   | 구독 ID             | 선택한 구독의 식별자                                      |
   | 위치                    | 배포의 지리적 위치                                        |
   | VM 크기                     | [Azure VM 크기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)(예: `Standard_A2`) |
   | 공용 IP 주소 이름      | 공용 IP 주소의 이름                                               |
   | VM 이름                     | 새 VM의 이름                                                           |
   | Virtual Network 이름        | VM에서 사용하는 가상 네트워크의 이름                                   |
   | NIC 이름                    | 가상 네트워크를 실행하는 네트워크 인터페이스 카드의 이름               |
   | VHD URL                     | 완전한 OS 디스크 VHD URL                                                     |
   |  |  |
            
5. 이러한 값을 제공한 후 **구매**를 클릭합니다. 

Azure에서 배포를 시작합니다. 지정된 관리되지 않는 VHD가 있는 새 VM을 지정된 저장소 계정 경로에 만듭니다.  포털의 왼쪽에 있는 **Virtual Machines**를 클릭하여 Azure Portal에서 진행 상황을 추적할 수 있습니다.  VM이 만들어지면 상태가 `Starting`에서 `Running`으로 변경됩니다. 


### <a name="deploy-a-vm-from-powershell"></a>PowerShell에서 VM 배포

방금 만든 일반화된 VM 이미지에서 큰 VM을 배포하려면 다음 cmdlet을 사용합니다.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>다음 단계

다음으로, 솔루션에 대한 [사용자 VM 이미지를 만듭니다](cpp-create-user-image.md).

