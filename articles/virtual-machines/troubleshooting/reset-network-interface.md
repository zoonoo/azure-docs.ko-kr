---
title: Windows Azure VM에 대한 네트워크 인터페이스를 다시 설정하는 방법 | Microsoft Docs
description: Windows Azure VM에 대한 네트워크 인터페이스를 다시 설정하는 방법을 보여 줍니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 6c4e507df0f112934979d4e59778b667743cf623
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87005959"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Windows Azure VM에 대한 네트워크 인터페이스를 다시 설정하는 방법 

이 문서에서는 Microsoft Azure Windows VM(Virtual Machine)에 연결할 수 없는 경우 Azure Windows VM의 네트워크 인터페이스를 다시 설정하여 문제를 해결하는 방법을 보여 줍니다.

* 기본 NIC(네트워크 인터페이스)를 사용하지 않도록 설정합니다. 
* NIC의 고정 IP를 수동으로 설정합니다. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>네트워크 인터페이스 다시 설정

### <a name="for-vms-deployed-in-resource-group-model"></a>리소스 그룹 모델에서 배포된 VM의 경우

1.  [Azure 포털](https://ms.portal.azure.com)로 이동합니다.
2.  영향을 받는 Virtual Machine을 선택합니다.
3.  **네트워킹**을 선택하고 VM의 네트워크 인터페이스를 선택합니다.

    ![네트워크 인터페이스 위치](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  **IP 구성**을 선택합니다.
5.  IP를 선택 합니다. 
6.  **프라이빗 IP 할당**이 **정적**이 아닌 경우 **정적**으로 변경합니다.
7.  **IP 주소**를 서브넷에서 사용할 수 있는 다른 IP 주소로 변경합니다.
8. 가상 머신을 다시 시작하여 시스템에 대한 새 NIC를 초기화합니다.
9.  컴퓨터에 RDP를 시도합니다. 성공할 경우 개인 IP 주소를 원래대로 변경할 수 있습니다(원하는 경우). 그렇지 않은 경우 현재 상태를 유지할 수 있습니다. 

#### <a name="use-azure-powershell"></a>Azure PowerShell 사용

1. 먼저 [최신 Azure PowerShell](/powershell/azure/)을 설치했는지 확인합니다.
2. 관리자 권한 Azure PowerShell 세션(관리자 권한으로 실행)을 엽니다. 다음 명령을 실행합니다.

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ResourceGroupName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. 컴퓨터에 RDP를 시도합니다.  성공할 경우 개인 IP 주소를 원래대로 변경할 수 있습니다(원하는 경우). 그렇지 않은 경우 현재 상태를 유지할 수 있습니다.

### <a name="for-classic-vms"></a>클래식 VM

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

네트워크 인터페이스를 다시 설정하려면 다음 단계를 따르세요.

#### <a name="use-azure-portal"></a>Azure Portal 사용

1.  [Azure 포털]( https://ms.portal.azure.com)로 이동합니다.
2.  **Virtual Machines(클래식)** 를 선택합니다.
3.  영향을 받는 Virtual Machine을 선택합니다.
4.  **IP 주소**를 선택합니다.
5.  **프라이빗 IP 할당**이 **정적**이 아닌 경우 **정적**으로 변경합니다.
6.  **IP 주소**를 서브넷에서 사용할 수 있는 다른 IP 주소로 변경합니다.
7.  **저장**을 선택합니다.
8.  가상 머신을 다시 시작하여 시스템에 대한 새 NIC를 초기화합니다.
9.  컴퓨터에 RDP를 시도합니다. 성공할 경우 개인 IP 주소를 원래대로 되돌릴 수 있습니다.  

#### <a name="use-azure-powershell"></a>Azure PowerShell 사용

1. [최신 Azure PowerShell](/powershell/azure/) 설치 되어 있는지 확인 합니다.
2. 관리자 권한 Azure PowerShell 세션(관리자 권한으로 실행)을 엽니다. 다음 명령을 실행합니다.

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ResourceGroupName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. 컴퓨터에 RDP를 시도합니다. 성공할 경우 개인 IP 주소를 원래대로 변경할 수 있습니다(원하는 경우). 그렇지 않은 경우 현재 상태를 유지할 수 있습니다. 

## <a name="delete-the-unavailable-nics"></a>사용할 수 없는 NIC를 삭제합니다.
컴퓨터에 원격 데스크톱을 수행한 후 잠재적인 문제를 방지하기 위해 이전 NIC를 삭제해야 합니다.

1.  디바이스 관리자를 엽니다.
2.  **보기**  >  **숨김 장치 표시**를 선택 합니다.
3.  **네트워크 어댑터**를 선택합니다. 
4.  “Microsoft Hyper-V 네트워크 어댑터”로 명명된 어댑터를 확인합니다.
5.  사용할 수 없는 어댑터가 회색으로 표시 될 수 있습니다. 어댑터를 마우스 오른쪽 단추로 클릭 한 다음 제거를 선택 합니다.

    ![NIC 이미지](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > 이름이 “Microsoft Hyper-V 네트워크 어댑터”이고 사용할 수 없는 어댑터만 제거합니다. 다른 숨겨진 어댑터를 제거하는 경우 추가 문제를 일으킬 수 있습니다.
    >
    >

6.  이제 사용할 수 없는 모든 어댑터가 시스템에서 지워집니다.
