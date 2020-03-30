---
title: '빠른 시작: 개인 IP 주소를 사용하여 가상 시스템에 연결: Azure Bastion'
description: 이 문서에서는 가상 컴퓨터에서 Azure Bastion 호스트를 만들고 개인 IP 주소를 사용하여 안전하게 연결하는 방법을 알아봅니다.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 0b4f0a1fa4f9cce05aa544bb24e045209a6d24ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137461"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>빠른 시작: 개인 IP 주소와 Azure 요새를 사용하여 가상 시스템에 연결

이 빠른 시작 문서에서는 개인 IP 주소를 사용하여 가상 컴퓨터에 연결하는 방법을 보여 주십니다. Bastion을 통해 연결할 때 가상 시스템에는 공용 IP 주소가 필요하지 않습니다. 이 문서의 단계는 포털의 가상 컴퓨터를 통해 가상 네트워크에 Bastion을 배포하는 데 도움이 됩니다. 서비스가 프로비전되면 동일한 가상 네트워크의 모든 가상 시스템에 RDP/SSH 환경을 사용할 수 있습니다.

## <a name="prerequisites"></a><a name="prereq"></a>사전 요구 사항

* Azure 가상 네트워크
* 포트 3389가 열려 있는 가상 네트워크에 있는 Azure 가상 시스템입니다.

### <a name="example-values"></a>예제 값

|**이름** | **값** |
| --- | --- |
| 이름 |  VNet1바스티션 |
| 지역 | eastus |
| 가상 네트워크 |  VNet1 |
| + 서브넷 이름 | AzureBastionSubnet |
| AzureBastionSubnet 주소 |  10.1.254.0/27 |
| 공용 IP 주소 |  새로 만들기 |
| 공용 IP 주소 이름 | VNet1바스티온핍핍  |
| 공용 IP 주소 SKU |  Standard  |
| 할당  | 정적 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>요새 호스트 만들기

기존 가상 컴퓨터를 사용하여 포털에서 방호 호스트를 만들면 가상 시스템 및/또는 가상 네트워크에 대응하기 위해 다양한 설정이 자동으로 기본값으로 설정됩니다.

1. Azure [포털을](https://portal.azure.com)엽니다. 가상 컴퓨터로 이동한 다음 **연결을**클릭합니다.

   ![가상 컴퓨터 설정](./media/quickstart-host-portal/vm-settings.png)
1. 드롭다운에서 **배스션을**선택합니다.
1. 연결 페이지에서 **대역 사용**을 선택합니다.

   ![요새 선택](./media/quickstart-host-portal/select-bastion.png)

1. 요새 페이지에서 다음 설정 필드를 작성합니다.

   * **이름**: 배스션 호스트 이름 지정
   * **서브넷**: Bastion 리소스가 배포될 가상 네트워크 내부의 서브넷입니다. 서브넷은 **AzureBastionSubnet**이름으로 만들어야 합니다. 이름을 사용하면 Azure에서 Bastion 리소스를 배포할 서브넷을 알 수 있습니다. 이는 게이트웨이 서브넷과 다릅니다. 최소 /27 이상의 서브넷(/27, /26, /25 등)을 사용합니다.
   
      * **서브넷 구성 관리를**선택한 다음 **+ 서브넷**을 선택합니다.
      * 서브넷 추가 페이지에서 **AzureBastionSubnet을**입력합니다.
      * CIDR 표기이에서 주소 범위를 지정합니다. 예를 들어 10.1.254.0/27입니다.
      * 서브넷을 만들려면 **확인을** 선택합니다. 페이지 상단에서 바스티온으로 돌아가 나머지 설정을 완료합니다.

         ![배스션 설정으로 이동](./media/quickstart-host-portal/navigate-bastion.png)
   * **공용 IP 주소**: RDP/SSH가 액세스하는 바스티온 리소스의 공용 IP입니다(포트 443을 통해). 새 공용 IP를 만들거나 기존 IP를 사용합니다. 공용 IP 주소는 만드는 Bastion 리소스와 동일한 지역에 있어야 합니다.
   * **공용 IP 주소 이름**: 공용 IP 주소 리소스의 이름입니다.
1. 유효성 검사 화면에서 **을 만들기를**클릭합니다. Bastion 리소스가 생성되고 배포될 때까지 약 5분 정도 기다립니다.

   ![대위 호스트 만들기](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>연결

Bastion이 가상 네트워크에 배포되면 화면이 연결 페이지로 변경됩니다.

1. 가상 컴퓨터의 사용자 이름과 암호를 입력합니다. 그런 다음, **연결**을 선택합니다.

   ![연결](./media/quickstart-host-portal/connect.png)
1. Bastion을 통해 이 가상 시스템에 대한 RDP 연결은 포트 443 및 Bastion 서비스를 사용하여 Azure 포털(HTML5 이상)에서 직접 열립니다.

   ![RDP 연결](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>리소스 정리

가상 네트워크 및 가상 컴퓨터를 사용하여 완료되면 리소스 그룹과 포함된 모든 리소스를 삭제합니다.

1. 포털 상단의 **검색** 상자에 *TestRG1을* 입력하고 검색 결과에서 **TestRG1을** 선택합니다.

2. **리소스 그룹 삭제**를 선택합니다.

3. 리소스 그룹 이름을 **입력하기** 위해 *TestRG1을* 입력하고 **삭제를**선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 가상 네트워크에 대한 Bastion 호스트를 만든 다음 Bastion 호스트를 통해 가상 시스템에 안전하게 연결했습니다.

* Azure 요새에 대해 자세히 알아보려면 [요새 개요](bastion-overview.md) 및 [바스티온 FAQ를](bastion-faq.md)참조하십시오.
* Azure 요새 서브넷이 있는 네트워크 보안 그룹을 사용하려면 [NSG사용 작업을](bastion-nsg.md)참조하십시오.
* Azure 요새 호스트 설정에 대한 설명이 포함된 지침은 [자습서를](bastion-create-host-portal.md)참조하십시오.
* 가상 시스템 규모 집합에 연결하려면 [Azure Bastion을 사용하여 가상 시스템 규모 집합에 연결 참조하세요.](bastion-connect-vm-scale-set.md)