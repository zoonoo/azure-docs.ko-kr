---
title: '빠른 시작: 개인 IP 주소를 사용 하 여 가상 머신에 연결: Azure 방호'
description: 이 문서에서는 가상 머신에서 Azure 방호 호스트를 만들고 개인 IP 주소를 사용 하 여 안전 하 게 연결 하는 방법에 대해 알아봅니다.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: charwen
ms.openlocfilehash: a420a3253040fff8b767a81f298ede283c1d214b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80619263"
---
# <a name="quickstart-connect-to-a-virtual-machine-using-a-private-ip-address-and-azure-bastion"></a>빠른 시작: 개인 IP 주소 및 Azure 방호를 사용 하 여 가상 머신에 연결

이 빠른 시작 문서에서는 개인 IP 주소를 사용 하 여 가상 머신에 연결 하는 방법을 보여 줍니다. 요새를 통해 연결 하는 경우 가상 머신에 공용 IP 주소가 필요 하지 않습니다. 이 문서의 단계는 포털에서 가상 머신을 통해 가상 네트워크에 방호를 배포 하는 데 도움이 됩니다. 서비스가 프로 비전 되 면 동일한 가상 네트워크의 모든 가상 머신에서 RDP/SSH 환경을 사용할 수 있습니다.

## <a name="prerequisites"></a><a name="prereq"></a>필수 조건

* Azure 가상 네트워크
* 3389 포트를 사용 하 여 가상 네트워크에 있는 Azure 가상 머신을 엽니다.

### <a name="example-values"></a>예제 값

|**이름** | **값** |
| --- | --- |
| 속성 |  VNet1Bastion |
| 지역 | eastus |
| 가상 네트워크 |  VNet1 |
| + 서브넷 이름 | AzureBastionSubnet |
| AzureBastionSubnet 주소 |  10.1.254.0/27 |
| 공용 IP 주소 |  새로 만들기 |
| 공용 IP 주소 이름 | VNet1BastionPIP  |
| 공용 IP 주소 SKU |  Standard  |
| 할당  | 정적 |

## <a name="create-a-bastion-host"></a><a name="createvmset"></a>요새 호스트 만들기

기존 가상 컴퓨터를 사용 하 여 포털에서 요새 호스트를 만들 때 다양 한 설정이 자동으로 가상 컴퓨터 및/또는 가상 네트워크에 해당 하는 기본 설정으로 설정 됩니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 가상 머신으로 이동한 다음 **연결**을 클릭 합니다.

   ![가상 컴퓨터 설정](./media/quickstart-host-portal/vm-settings.png)
1. 드롭다운에서 **방호**를 선택 합니다.
1. 연결 페이지에서 **방호 사용**을 선택 합니다.

   ![요새 선택](./media/quickstart-host-portal/select-bastion.png)

1. 요새 페이지에서 다음 설정 필드를 입력 합니다.

   * **이름**: 요새 호스트 이름
   * **서브넷**: 가상 네트워크 내에서, 요새 리소스가 배포 되는 서브넷입니다. **AzureBastionSubnet**이름으로 서브넷을 만들어야 합니다. 이 이름을 사용 하면 Azure에서 방호 리소스를 배포할 서브넷을 알 수 있습니다. 이는 게이트웨이 서브넷과는 다릅니다. 최소/27 이상 (/27,/26,/25 등)의 서브넷을 사용 합니다.
   
      * **서브넷 구성 관리**를 선택한 다음, **+ 서브넷**을 선택 합니다.
      * 서브넷 추가 페이지에서 **AzureBastionSubnet**을 입력 합니다.
      * CIDR 표기법으로 주소 범위를 지정 합니다. 예를 들면 10.1.254.0/27입니다.
      * **확인** 을 선택 하 여 서브넷을 만듭니다. 페이지 맨 위에 있는 요새로 다시 이동 하 여 나머지 설정을 완료 합니다.

         ![요새 설정으로 이동](./media/quickstart-host-portal/navigate-bastion.png)
   * **공용 ip 주소**: RDP/SSH를 액세스할 수 있는 방호 리소스 (443 포트를 통해)의 공용 ip입니다. 새 공용 IP를 만들거나 기존 공용 IP를 사용 합니다. 공용 IP 주소는 만들려는 방호 리소스와 동일한 지역에 있어야 합니다.
   * **공용 ip 주소 이름**: 공용 ip 주소 리소스의 이름입니다.
1. 유효성 검사 화면에서 **만들기**를 클릭 합니다. 요새 리소스를 만들고 배포 하는 데 약 5 분 정도 기다립니다.

   ![요새 호스트 만들기](./media/quickstart-host-portal/bastion-settings.png)

## <a name="connect"></a><a name="connect"></a>연결

가상 네트워크에 배포 된 경우 화면은 연결 페이지로 바뀝니다.

1. 가상 컴퓨터의 사용자 이름 및 암호를 입력 합니다. 그런 다음, **연결**을 선택합니다.

   ![connect](./media/quickstart-host-portal/connect.png)
1. 이 가상 머신에 대 한 RDP 연결은 포트 443 및 요새 서비스를 사용 하 여 Azure Portal (HTML5 이상)에서 직접 열립니다.

   ![RDP 연결](./media/quickstart-host-portal/443-rdp.png)

## <a name="clean-up-resources"></a>리소스 정리

가상 네트워크 및 가상 컴퓨터를 사용 하 여 작업을 완료 한 후에는 리소스 그룹과 해당 리소스 그룹에 포함 된 모든 리소스를 삭제 합니다.

1. 포털 맨 위에 있는 **검색** 상자에 *TestRG1* 를 입력 하 고 검색 결과에서 **TestRG1** 를 선택 합니다.

2. **리소스 그룹 삭제**를 선택합니다.

3. **리소스 그룹 이름** 에 *TestRG1* 을 입력 하 고 **삭제**를 선택 합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 가상 네트워크에 대 한 요새 호스트를 만든 다음, 요새 호스트를 통해 안전 하 게 가상 머신에 연결 했습니다.

* Azure 방호에 대 한 자세한 내용은 [요새 개요](bastion-overview.md) 및 [방호 FAQ](bastion-faq.md)를 읽어 보세요.
* Azure 방호 서브넷에서 네트워크 보안 그룹을 사용 하려면 [NSGs 작업](bastion-nsg.md)을 참조 하세요.
* Azure 방호 호스트 설정에 대 한 설명이 포함 된 지침은이 [자습서](bastion-create-host-portal.md)를 참조 하세요.
* 가상 머신 확장 집합에 연결 하려면 [Azure 방호를 사용 하 여 가상 머신 확장 집합에 연결](bastion-connect-vm-scale-set.md)을 참조 하세요.