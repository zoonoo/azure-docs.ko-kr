---
title: '빠른 시작: NAT 게이트웨이 만들기 - Azure Portal'
titlesuffix: Azure Virtual Network NAT
description: 이 빠른 시작에서는 Azure Portal을 사용하여 NAT 게이트웨이를 만드는 방법을 보여줍니다.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
Customer intent: I want to create a NAT gateway for outbound connectivity for my virtual network.
ms.service: virtual-network
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2020
ms.author: allensu
ms.openlocfilehash: c6da4b54dbc982c69e9d3004a5da8f63deffa3e9
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246025"
---
# <a name="quickstart-create-a-nat-gateway-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 NAT 게이트웨이 만들기

이 빠른 시작에서는 Azure Virtual Network NAT 서비스를 사용하는 방법을 보여줍니다. Azure에서 가상 머신에 대한 아웃바운드 연결을 제공하는 NAT 게이트웨이를 만듭니다. 

>[!NOTE] 
>Azure Virtual Network NAT는 현재 공개 미리 보기로 제공되며, 제한된 [지역](./nat-overview.md#region-availability) 세트에서 사용할 수 있습니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms)을 참조하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

VM을 배포하고 NAT 게이트웨이를 사용하려면 먼저 리소스 그룹과 가상 네트워크를 만들어야 합니다.

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꾸어야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupNAT |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 미국 동부 2      |
| **\<IPv4-address-space>**   | 192.168.0.0\16          |
| **\<subnet-name>**          | mySubnet        |
| **\<subnet-address-range>** | 192.168.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

## <a name="create-a-vm-to-use-the-nat-gateway"></a>NAT 게이트웨이를 사용할 VM 만들기

이제 NAT 서비스를 사용할 VM을 만듭니다. 이 VM에는 VM에 액세스할 수 있도록 인스턴스 수준 공용 IP로 사용할 공용 IP가 있습니다. NAT 서비스는 흐름 방향을 인식하며 서브넷의 기본 인터넷 대상을 대체합니다. VM의 공용 IP 주소는 아웃바운드 연결에 사용되지 않습니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** > **Compute** > **Ubuntu Server 18.04 LTS**를 차례로 선택하거나, Marketplace 검색에서 **Ubuntu Server 18.04 LTS**를 검색합니다.

2. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**를 선택합니다.
   - **인스턴스 정보** > **가상 머신 이름**: **myVM**을 입력합니다.
   - **인스턴스 세부 정보** > **지역** > **미국 동부 2**를 선택합니다.
   - **관리자 계정** > **인증 유형**: **암호**를 선택합니다.
   - **관리자 계정** > **사용자 이름**, **암호** 및 **암호 확인** 정보를 입력합니다.
   - **인바운드 포트 규칙** > **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 규칙** > **인바운드 포트 선택**: **SSH(22)** 를 선택합니다.
   - **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**을 차례로 선택합니다.

3. **네트워킹** 탭에서 다음 항목이 선택되어 있는지 확인합니다.
   - **가상 네트워크**: **myVnet**
   - **서브넷**: **mySubnet**
   - **공용 IP** > **새로 만들기**를 선택합니다.  **공용 IP 주소 만들기** 창에서 **이름** 필드에 **myPublicIPVM**을 입력하고 **SKU**에 대해 **표준**을 선택합니다.  **확인**을 클릭합니다.
   - **NIC 네트워크 보안 그룹**: **기본**을 선택합니다.
   - **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 선택**: **SSH**가 선택되었는지 확인합니다.

4. **관리** 탭의 **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.

5. **검토 + 만들기**를 선택합니다. 

6. 설정을 검토하고 **만들기**를 클릭합니다.

## <a name="create-the-nat-gateway"></a>NAT 게이트웨이 만들기

하나 이상의 공용 IP 주소 리소스, 공용 IP 접두사 또는 둘 다를 사용할 수 있습니다. 공용 IP 리소스, 공용 IP 접두사 및 NAT 게이트웨이 리소스를 추가합니다.

이 섹션에서는 NAT 게이트웨이 리소스를 사용하여 NAT 서비스의 다음 구성 요소를 만들고 구성하는 방법에 대해 자세히 설명합니다.
  - NAT 게이트웨이 리소스에서 변환하는 아웃바운드 흐름에 사용할 공용 IP 풀 및 공용 IP 접두사입니다.
  - 유휴 시간 제한을 4분(기본값)에서 10분으로 변경합니다.

### <a name="create-a-public-ip-address"></a>공용 IP 주소 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **공용 IP 주소**를 차례로 선택하거나, Marketplace 검색에서 **공용 IP 주소**를 검색합니다.

2. **공용 IP 주소 만들기**에서 다음 정보를 입력하거나 선택합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | IP 버전 | **IPv4**를 선택합니다.
    | SKU | **표준**을 선택합니다.
    | 속성 | **myPublicIP**를 입력합니다. |
    | Subscription | 구독을 선택합니다.|
    | Resource group | **myResourceGroupNAT**를 선택합니다. |
    | 위치 | **미국 동부 2**를 선택합니다.|

3. 나머지는 기본값으로 두고, **만들기**를 선택합니다.

### <a name="create-a-public-ip-prefix"></a>공용 IP 접두사 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **공용 IP 접두사**를 차례로 선택하거나, Marketplace 검색에서 **공용 IP 접두사**를 검색합니다. 

2. **공용 IP 접두사 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**>를 선택합니다.
   - **인스턴스 세부 정보** > **이름**: **myPublicIPprefix**를 입력합니다.
   - **인스턴스 세부 정보** > **지역**: **미국 동부 2**를 선택합니다.
   - **인스턴스 세부 정보** > **접두사 크기**: **/31(2개 주소)** 을 선택합니다.

3. 나머지는 기본값으로 두고 **검토 + 만들기**를 선택합니다.

4. 설정을 검토한 다음, **만들기**를 선택합니다.
   

### <a name="create-a-nat-gateway-resource"></a>NAT 게이트웨이 리소스 만들기

1. 포털의 왼쪽 위에서 **리소스 만들기** > **네트워킹** > **NAT 게이트웨이**를 차례로 선택하거나, Marketplace 검색에서 **NAT 게이트웨이**를 검색합니다.

2. **NAT(Network Address Translation) 게이트웨이 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: **myResourceGroupNAT**를 선택합니다.
   - **인스턴스 세부 정보** > **NAT 게이트웨이 이름**: **myNATgateway**를 입력합니다.
   - **인스턴스 세부 정보** > **지역**: **미국 동부 2**를 선택합니다.
   - **인스턴스 세부 정보** > **유휴 시간 제한(분)** : **10**을 입력합니다.
   - **공용 IP** 탭을 선택하거나 **다음: 공용 IP**를 선택합니다.

3. **공용 IP** 탭에서 다음 값을 입력하거나 선택합니다.
   - **공용 IP 주소**: **myPublicIP**를 선택합니다.
   - **공용 IP 접두사**: **myPublicIPprefix**를 선택합니다.
   - **서브넷** 탭을 선택하거나 **다음: 서브넷**을 선택합니다.

4. **서브넷** 탭에서 다음 값을 입력하거나 선택합니다.
   - **Virtual Network**: **myResourceGroupNAT** > **myVnet**을 선택합니다.
   - **서브넷 이름**: **mySubnet** 옆의 상자를 선택합니다.

5. **검토 + 만들기**를 선택합니다.

6. 설정을 검토한 다음, **만들기**를 선택합니다.

## <a name="discover-the-ip-address-of-the-vm"></a>VM의 IP 주소 검색

1. 포털의 왼쪽에서 **리소스 그룹**을 선택합니다.
2. **myResourceGroupNAT**를 선택합니다.
3. **myVM**을 선택합니다.
4. **개요**에서 **공용 IP 주소** 값을 복사하여 VM에 액세스하는 데 사용할 수 있도록 메모장에 붙여넣습니다.

>[!IMPORTANT]
>공용 IP 주소를 복사한 다음, 이를 사용하여 VM에 액세스할 수 있도록 메모장에 붙여넣습니다.

## <a name="sign-in-to-vm"></a>VM에 로그인

브라우저에서 [Azure Cloud Shell](https://shell.azure.com)을 엽니다. 이전 단계에서 검색된 IP 주소를 사용하여 가상 머신에 SSH로 연결합니다.

```azurecli-interactive
ssh <username>@<ip-address-destination>
```

이제 NAT 서비스를 사용할 준비가 되었습니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 리소스 그룹, NAT 게이트웨이 및 모든 관련 리소스를 삭제합니다. NAT 게이트웨이가 포함된**myResourceGroupNAT** 리소스 그룹을 선택한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 NAT 게이트웨이 및 이를 사용하는 VM을 만들었습니다. 

Azure Monitor에서 메트릭을 검토하여 NAT 서비스의 작동 상태를 확인합니다. 사용 가능한 SNAT 포트의 리소스 소모와 같은 문제를 진단합니다.  이러한 SNAT 포트의 리소스 소모는 공용 IP 주소 리소스, 공용 IP 접두사 리소스 또는 둘 모두를 추가하여 해결할 수 있습니다.


- [Azure Virtual Network NAT](./nat-overview.md)에 대한 자세한 정보
- [NAT 게이트웨이 리소스](./nat-gateway-resource.md)에 대해 알아봅니다.
- [Azure CLI를 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-cli.md)를 배포하기 위한 빠른 시작.
- [Azure PowerShell을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-powershell.md)를 배포하기 위한 빠른 시작.
- [Azure Portal을 사용하여 NAT 게이트웨이 리소스](./quickstart-create-nat-gateway-portal.md)를 배포하기 위한 빠른 시작.
- [공개 미리 보기에 대한 피드백을 제공합니다](https://aka.ms/natfeedback).
> [!div class="nextstepaction"]

