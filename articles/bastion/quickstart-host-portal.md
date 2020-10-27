---
title: '빠른 시작: Azure Bastion을 구성하고 개인 IP 주소 및 브라우저를 통해 VM에 연결'
titleSuffix: Azure Bastion
description: 이 빠른 시작 문서에서는 가상 머신에서 Azure Bastion 호스트를 만들고 브라우저와 개인 IP 주소를 통해 안전하게 VM에 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/15/2020
ms.author: cherylmc
ms.openlocfilehash: 325f39b695d80c14ed7097d071380b937458546c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150441"
---
# <a name="quickstart-connect-to-a-vm-securely-through-a-browser-via-private-ip-address"></a>빠른 시작: 브라우저와 개인 IP 주소를 통해 안전하게 VM에 연결

Azure Portal 및 Azure Bastion을 사용하여 브라우저를 통해 VM(가상 머신)에 연결할 수 있습니다. 이 빠른 시작 문서에서는 VM 설정에 따라 Azure Bastion을 구성한 다음, 포털을 통해 VM에 연결하는 방법을 보여줍니다. VM에는 공용 IP 주소, 클라이언트 소프트웨어, 에이전트 또는 특별한 구성이 필요 없습니다. 서비스가 프로비저닝되면 동일한 가상 네트워크의 모든 가상 머신에서 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

## <a name="prerequisites"></a><a name="prereq"></a>필수 조건

* 활성 구독이 있는 Azure 계정. 계정이 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Bastion을 사용하여 브라우저를 통해 VM에 연결하려면 Azure Portal에 로그인할 수 있어야 합니다.

* 가상 네트워크의 Windows 가상 머신. VM이 없는 경우 [빠른 시작: VM 만들기](../virtual-machines/windows/quick-create-portal.md)를 참조하여 하나 만듭니다.

  * 예제 값이 필요한 경우 제공된 [예제 값](#values)을 참조하세요.
  * 가상 네트워크가 이미 있는 경우 VM을 만들 때 [네트워킹] 탭에서 해당 가상 네트워크를 선택해야 합니다.
  * 가상 네트워크가 아직 없는 경우 VM을 만들 때 함께 만들 수 있습니다.
  * 이 VM의 공용 IP 주소가 없어도 Azure Bastion을 통해 연결할 수 있습니다.

* 필요한 VM 역할:
  * 가상 머신에 대한 읽기 권한자 역할
  * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할
  
* 필요한 VM 포트:
  * 인바운드 포트: RDP(3389)

### <a name="example-values"></a><a name="values"></a>예제 값

이 구성을 만들 때 다음 예제 값을 사용해도 되고 자체적인 값으로 바꿔도 됩니다.

**기본 VNet 및 VM 값:**

|**이름** | **값** |
| --- | --- |
| 가상 머신| TestVM |
| 리소스 그룹 | TestRG |
| Azure 지역 | 미국 동부 |
| 가상 네트워크 | TestVNet1 |
| 주소 공간 | 10.0.0.0/16 |
| 서브넷 | 프런트 엔드: 10.0.0.0/24 |

**Azure Bastion 값:**

|**이름** | **값** |
| --- | --- |
| Name | TestVNet1-bastion |
| + 서브넷 이름 | AzureBastionSubnet |
| AzureBastionSubnet 주소 | VNet 주소 공간 내에서 /27 서브넷 마스크를 사용하는 서브넷입니다. 예: 10.0.1.0/27.  |
| 공용 IP 주소 |  새로 만들기 |
| 공용 IP 주소 이름 | VNet1BastionPIP  |
| 공용 IP 주소 SKU |  Standard  |
| 할당  | 정적 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion 호스트 만들기

몇 가지 방법으로 Bastion 호스트를 구성할 수 있습니다. 다음 단계에서는 VM에서 직접 Azure Portal에 Bastion 호스트를 만듭니다. VM에서 호스트를 만들 때 다양한 설정이 가상 머신 및/또는 가상 네트워크의 해당 설정을 자동으로 채웁니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 연결하려는 VM으로 이동한 다음, **연결** 을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="가상 머신 설정" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. 드롭다운에서 **Bastion** 을 선택합니다.
1. **TestVM | 연결 페이지** 에서 **Bastion 사용** 을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="가상 머신 설정" border="false":::

1. **Bastion** 페이지에서 다음 설정 필드를 작성합니다.

   * **이름** : 베스천 호스트의 이름을 지정합니다.
   * **서브넷** : Bastion 리소스가 배포될 가상 네트워크 주소 공간입니다. **AzureBastionSubnet** 이름을 사용하여 서브넷을 만들어야 합니다. 최소 /27 이상(/27, /26, /25 등)의 서브넷을 사용합니다.
   * **서브넷 구성 관리** 를 선택합니다.
1. **서브넷** 페이지에서 **+서브넷** 을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/subnet.png" alt-text="가상 머신 설정":::
    
1. **서브넷 추가** 페이지에서 **이름** 으로 **AzureBastionSubnet** 을 입력합니다.
   * 서브넷 주소 범위로는 가상 네트워크 주소 공간 내에 있는 서브넷 주소를 선택합니다.
   * 다른 설정은 조정하지 않습니다. **확인** 을 선택하여 서브넷 변경 내용을 수락하고 저장합니다.

   :::image type="content" source="./media/quickstart-host-portal/add-subnet.png" alt-text="가상 머신 설정":::
1. 브라우저에서 [뒤로] 단추를 클릭하여 **Bastion** 페이지로 돌아간 다음, 값을 계속 지정합니다.
   * **공용 IP 주소** : **새로 만들기** 를 유지합니다.
   * **공용 IP 주소 이름** : 공용 IP 주소 리소스의 이름입니다.
   * **할당** : 기본값은 [정적]입니다. Azure Bastion에는 동적 할당을 사용할 수 없습니다.
   * **리소스 그룹** : VM과 동일한 리소스 그룹입니다.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="가상 머신 설정":::
1. **만들기** 를 선택하여 베스천 호스트를 만듭니다. Azure에서 설정의 유효성을 검사한 다음, 호스트를 만듭니다. 호스트와 해당 리소스를 만들고 및 배포하는 데 약 5분 정도 걸립니다.

## <a name="connect"></a><a name="connect"></a>연결

Bastion이 가상 네트워크에 배포되면 화면이 연결 페이지로 바뀝니다.

1. 가상 머신의 사용자 이름과 암호를 입력합니다. 그런 다음, **연결** 을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="가상 머신 설정":::
1. 이 가상 머신에 대한 RDP 연결은 HTML5를 통해 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다.

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="가상 머신 설정":::

## <a name="clean-up-resources"></a>리소스 정리

가상 네트워크 및 가상 머신 사용을 마쳤으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 위쪽의 **검색** 상자에서 리소스 그룹 이름을 입력하고, 검색 결과에서 이를 선택합니다.

1. **리소스 그룹 삭제** 를 선택합니다.

1. **리소스 그룹 이름 입력** 에 대해 리소스 그룹을 입력하고, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 가상 네트워크에 대한 Bastion 호스트를 만든 다음, Bastion을 통해 안전하게 가상 머신에 연결했습니다. 다음으로, 가상 머신 확장 집합에 연결하려는 경우 다음 단계를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Bastion을 사용하여 가상 머신 확장 집합에 연결](bastion-connect-vm-scale-set.md)
