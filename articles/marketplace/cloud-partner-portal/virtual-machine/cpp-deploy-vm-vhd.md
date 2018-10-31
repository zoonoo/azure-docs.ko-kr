---
title: VHD에서 Azure Marketplace용 VM 배포 | Microsoft Docs
description: Azure에서 배포한 VHD에서 VM을 등록하는 방법을 설명합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639377"
---
# <a name="deploy-a-vm-from-your-vhds"></a>VHD에서 VM 배포

이 문서에서는 Azure에서 배포한 VHD(가상 하드 디스크)에서 VM(가상 머신)을 등록하는 방법에 대해 설명합니다.  필요한 도구 및 이 도구를 사용하여 사용자 VM 이미지를 만든 다음, [Microsoft Azure Portal](https://ms.portal.azure.com/) 또는 PowerShell 스크립트 중 하나를 사용하여 Azure에 이를 배포하는 방법을 나열합니다. 

VHD(가상 하드 디스크), 즉 일반화된 운영 체제 VHD 및 0개 이상의 데이터 디스크 VHD가 Azure 저장소 계정에 업로드되면 사용자 VM 이미지로 등록할 수 있습니다. 그런 다음 해당 이미지를 테스트할 수 있습니다. 운영 체제 VHD는 일반화되므로 VHD URL을 제공하여 VM을 직접 배포할 수 없습니다.

VM 이미지에 대해 자세히 알아보려면 다음 블로그 게시물을 참조하세요.

- [VM 이미지](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM 이미지 PowerShell '방법'](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>필요한 도구 설정

아직 수행하지 않은 경우 다음 지침에 따라 Azure PowerShell 및 Azure CLI를 설치합니다.

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [PowerShellGet으로 Azure PowerShell을 설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>사용자 VM 이미지 만들기

다음으로, 일반화된 VHD에서 관리되지 않는 이미지를 만듭니다.

#### <a name="capture-the-vm-image"></a>VM 이미지 캡처

액세스 방법에 해당하는 VM 캡처에 대한 다음 문서의 지침을 사용합니다.

-  PowerShell: [Azure VM에서 관리되지 않는 VM 이미지를 만드는 방법](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI: [가상 머신 또는 VHD의 이미지를 만드는 방법](../../../virtual-machines/linux/capture-image.md)
-  API: [Virtual Machines - 캡처](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>VM 이미지 일반화

이전에 일반화된 VHD에서 사용자 이미지를 생성했으므로 이를 일반화해야 합니다.  다시 한 번 액세스 메커니즘에 해당하는 다음 문서를 선택합니다.  (디스크를 캡처할 때 이미 디스크를 일반화했을 수 있습니다.)

-  PowerShell: [VM 일반화](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI: [2단계: VM 이미지 만들기](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [Virtual Machines - 일반화](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>사용자 VM 이미지에서 VM 배포

다음으로, Azure Portal 또는 PowerShell을 사용하여 사용자 VM 이미지에서 VM을 배포합니다.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Azure Portal에서 VM 배포

다음 프로세스를 사용하여 Azure Portal에서 사용자 VM을 배포합니다.

1.  [Azure Portal](https://portal.azure.com)에 로그인합니다.

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

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>다음 단계

VM이 배포되면 [VM을 구성](./cpp-configure-vm.md)할 준비가 됩니다.
