---
title: '빠른 시작: VM에서 Bastion 호스트를 만들고 개인 IP 주소를 통해 연결'
titleSuffix: Azure Bastion
description: 이 빠른 시작 문서에서는 가상 머신에서 Azure Bastion 호스트를 만들고 개인 IP 주소를 사용하여 안전하게 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 6f451e7b115c00bc7b2cf350e00b9f704ab1d29f
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92019055"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>빠른 시작: 개인 IP 주소 및 Azure Bastion을 사용하여 가상 머신에 연결

이 빠른 시작 문서에서는 Azure Bastion 및 Azure Portal을 사용하여 브라우저를 통해 가상 머신에 연결하는 방법을 보여 줍니다. Azure Portal의 Azure VM에서 Bastion을 가상 네트워크에 배포할 수 있습니다. Bastion이 배포되면 Azure Portal을 사용하여 개인 IP 주소를 통해 VM에 연결할 수 있습니다. VM에는 공용 IP 주소 또는 특정 소프트웨어가 필요하지 않습니다. VM에서 직접 VNet용 Bastion 호스트를 만들 때의 한 가지 이점은 많은 설정이 미리 채워져 있다는 것입니다.

서비스가 프로비저닝되면 동일한 가상 네트워크의 모든 가상 머신에서 RDP/SSH 환경을 사용할 수 있습니다. Azure Bastion에 대한 자세한 내용은 [Azure Bastion이란?](bastion-overview.md)을 참조하세요.

## <a name="prerequisites"></a><a name="prereq"></a>필수 조건

* 가상 네트워크
* 가상 네트워크의 Windows 가상 머신
* 필요한 역할은 다음과 같습니다.
  * 가상 머신에 대한 읽기 권한자 역할
  * 가상 머신의 개인 IP를 사용하는 NIC에 대한 읽기 권한자 역할

* 포트: VM에 연결하려면 VM에서 다음 포트가 열려 있어야 합니다.
  * 인바운드 포트: RDP(3389)

### <a name="example-values"></a>예제 값

|**이름** | **값** |
| --- | --- |
| Name |  TestVNet1-bastion |
| 가상 네트워크 |  TestVNet1(VM 기반) |
| + 서브넷 이름 | AzureBastionSubnet |
| AzureBastionSubnet 주소 |  10.1.254.0/27 |
| 공용 IP 주소 |  새로 만들기 |
| 공용 IP 주소 이름 | VNet1BastionPIP  |
| 공용 IP 주소 SKU |  Standard  |
| 할당  | 정적 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>Bastion 호스트 만들기

기존 가상 머신을 사용하여 Azure Portal에서 베스천 호스트를 만드는 경우 다양한 설정이 자동으로 가상 머신 및/또는 가상 네트워크에 해당하는 기본값으로 설정됩니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 가상 머신으로 이동한 다음, **연결**을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/vm-settings.png" alt-text="가상 머신 설정" lightbox="./media/quickstart-host-portal/vm-settings.png":::
1. 드롭다운에서 **Bastion**을 선택합니다.
1. **TestVM | 연결 페이지**에서 **Bastion 사용**을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/select-bastion.png" alt-text="가상 머신 설정" border="false":::

1. **Bastion** 페이지에서 다음 설정 필드를 작성합니다.

   * **이름**: 베스천 호스트의 이름을 지정합니다.
   * **서브넷**: Bastion 리소스가 배포될 가상 네트워크의 서브넷입니다. **AzureBastionSubnet**이름을 사용하여 서브넷을 만들어야 합니다. 이 이름을 통해 Azure에서 Bastion 리소스를 배포할 서브넷을 인식할 수 있습니다. 이는 게이트웨이 서브넷과 다릅니다. 최소 /27 이상(/27, /26, /25 등)의 서브넷을 사용합니다.
   
      * **서브넷 구성 관리**를 선택합니다.
      * **AzureBastionSubnet**을 선택합니다.
      * 필요한 경우 주소 범위를 CIDR 표기법으로 조정합니다. 예: 10.1.254.0/27
      * 다른 설정은 조정하지 않습니다. 서브넷 변경 내용을 적용하고 저장하려면 **확인**을 선택하고, 변경하지 않으려면 페이지 위쪽에서 **x**를 선택합니다.
1. 브라우저에서 뒤로 단추를 클릭하여 **Bastion** 페이지로 다시 이동하여 값을 계속 지정합니다.
   * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다.
   * **공용 IP 주소**: 이는 포트 443을 통해 RDP/SSH를 액세스할 수 있는 Bastion 리소스의 공용 IP입니다. 새 공용 IP를 만듭니다.
1. **만들기**를 선택하여 베스천 호스트를 만듭니다. Azure에서 설정의 유효성을 검사한 다음, 호스트를 만듭니다. 호스트와 해당 리소스를 만들고 및 배포하는 데 약 5분 정도 걸립니다.

   :::image type="content" source="./media/quickstart-host-portal/validate.png" alt-text="가상 머신 설정":::

## <a name="connect"></a><a name="connect"></a>연결

Bastion이 가상 네트워크에 배포되면 화면이 연결 페이지로 바뀝니다.

1. 가상 머신의 사용자 이름과 암호를 입력합니다. 그런 다음, **연결**을 선택합니다.

   :::image type="content" source="./media/quickstart-host-portal/connect-vm.png" alt-text="가상 머신 설정":::
1. 이 가상 머신에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure Portal에서 직접 열립니다(HTML5를 통해).

   :::image type="content" source="./media/quickstart-host-portal/connected.png" alt-text="가상 머신 설정":::

## <a name="clean-up-resources"></a>리소스 정리

가상 네트워크 및 가상 머신 사용을 마쳤으면 리소스 그룹과 리소스 그룹에 포함된 모든 리소스를 삭제합니다.

1. 포털 위쪽의 **검색** 상자에서 리소스 그룹 이름을 입력하고, 검색 결과에서 이를 선택합니다.

1. **리소스 그룹 삭제**를 선택합니다.

1. **리소스 그룹 이름 입력**에 대해 리소스 그룹을 입력하고, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 가상 네트워크에 대한 Bastion 호스트를 만든 다음, Bastion 호스트를 통해 안전하게 가상 머신에 연결했습니다. 다음으로, 가상 머신 확장 집합에 연결하려는 경우 다음 단계를 계속 진행할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Bastion을 사용하여 가상 머신 확장 집합에 연결](bastion-connect-vm-scale-set.md)
