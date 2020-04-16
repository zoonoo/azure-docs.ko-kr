---
title: 가상 네트워크에 가입하도록 Azure-SSIS 통합 런타임을 구성하는 자습서
description: Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/10/2020
ms.openlocfilehash: 7470af23059ccd07e48050e6dc34521e299a986f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418612"
---
# <a name="configure-an-azure-sql-server-integration-services-ssis-integration-runtime-ir-to-join-a-virtual-network"></a>가상 네트워크에 가입하도록 Azure-SQL 서버 통합 서비스(SSIS) 통합 런타임(IR) 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 자습서에서는 Azure 포털을 사용하여 가상 네트워크에 가입하도록 Azure-SQL 서버 통합 서비스(SSIS) 통합 런타임(IR)을 구성하기 위한 기본 단계를 제공합니다.

단계는 다음과 같습니다.

- 가상 네트워크를 구성합니다.
- Azure-SSIS IR을 Azure 데이터 팩터리 포털의 가상 네트워크에 조인합니다.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure-SSIS 통합 런타임.** Azure-SSIS 통합 런타임이 없는 경우 시작하기 전에 [Azure-SSIS 통합 런타임을 Azure 데이터 팩터리에서 프로비전합니다.](tutorial-deploy-ssis-packages-azure.md)

- **사용자 권한**. Azure-SSIS IR을 만드는 사용자는 아래 옵션 중 하나를 사용하여 Azure Data Factory 리소스에서 역할 [할당을](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-list-portal#list-role-assignments-for-a-user-at-a-scope) 수행해야 합니다.

    - 기본 제공 ‘네트워크 참가자’ 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.
    - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. Azure Resource Manager 가상 네트워크에 가입하는 동안 Azure-SSIS IR에 대한 공용 IP 주소를 가져오려면 역할에 _Microsoft.Network/publicIP주소/*/조인/작업_ 권한도 포함하십시오.

- **가상 네트워크**.

    - 가상 네트워크가 없는 경우 [Azure 포털을 사용하여 가상 네트워크를 만듭니다.](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

    - 가상 네트워크의 리소스 그룹이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인합니다.
    
        Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 이러한 리소스에는 다음이 포함됩니다.
        - * \<Guid>-azurebatch-cloudserviceloadbalancer라는 이름의 Azure 로드 밸런서*
        - 이름 *\<Guid>-azurebatch-cloudservicenetworksecuritygroup을 가진 네트워크 보안 그룹
        - 이름 -azurebatch-cloudservicepublicip와 Azure 공용 IP 주소
    
        이러한 리소스는 Azure-SSIS IR이 시작될 때 만들어집니다. Azure-SSIS IR이 중지되면 삭제됩니다. Azure-SSIS IR이 중지되지 않도록 하려면 다른 리소스에서 이러한 네트워크 리소스를 다시 사용하지 마십시오.

    - 가상 네트워크가 속한 리소스 그룹/구독에 [리소스 잠금이](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) 없는지 확인합니다. 읽기 전용/삭제 잠금을 구성하면 Azure-SSIS IR을 시작하고 중지하면 실패하거나 응답이 중지됩니다.

    - 가상 네트워크가 속한 리소스 그룹/구독에서 다음 리소스를 만들지 못하도록 하는 Azure 정책이 없는지 확인합니다.
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

- 아래 **네트워크 구성** 시나리오는 이 자습서에서 다루지 않습니다.
    - Azure-SSIS IR에 대한 공용 IP 주소를 가져오는 경우
    - 자신의 DNS(도메인 이름 시스템) 서버를 사용하는 경우.
    - 서브넷에서 NSG(네트워크 보안 그룹)를 사용하는 경우
    - Azure ExpressRoute 또는 사용자 정의 경로(UDR)를 사용하는 경우
    - 사용자 지정된 Azure-SSIS IR을 사용하는 경우
    
    자세한 내용은 [가상 네트워크 구성을](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)확인하십시오.

## <a name="configure-a-virtual-network"></a>가상 네트워크 구성

Azure-SSIS IR에 가입하기 전에 Azure 포털을 사용하여 가상 네트워크를 구성합니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 데이터 팩터리 UI를 지원합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **더 많은 서비스를 선택합니다.** **가상 네트워크**를 필터링하여 선택합니다.

1. 목록에서 가상 네트워크를 필터링하고 선택합니다.

1. **가상 네트워크** 페이지에서 **속성**을 선택합니다.

1. **리소스 ID**에 대한 복사 단추를 선택하여 가상 네트워크에 대한 리소스 ID를 클립보드에 복사합니다. 클립 보드의 ID를 OneNote 또는 파일에 저장합니다.

1. 왼쪽 메뉴에서 **서브넷을 선택합니다.**

    - 선택한 서브넷에 Azure-SSIS IR을 사용할 수 있는 충분한 주소 공간이 있는지 확인합니다. IR 노드 번호의 두 배 이상사용 가능한 IP 주소를 남겨 둡니다. Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 이러한 주소는 사용할 수 없습니다. 서브넷의 첫 번째 및 마지막 IP 주소는 프로토콜 준수를 위해 예약되어 있으며 Azure 서비스에 세 개의 주소가 더 사용됩니다. 자세한 내용은 [이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
    - Azure-SSIS IR을 배포할 게이트웨이서브넷을 선택하지 마십시오. 가상 네트워크 게이트웨이 전용입니다.
    - 다른 Azure 서비스(예: SQL Database 관리 인스턴스, 앱 서비스 등)에서 만 사용하는 서브넷을 사용하지 마십시오.

1. 가상 네트워크가 있는 Azure 구독에 Azure Batch 공급자가 등록되었는지 확인합니다. 또는 Azure 일괄 처리 공급자를 등록합니다. 구독에 Azure Batch 계정이 이미 있는 경우 구독이 Azure Batch에 등록됩니다. (Data Factory 포털에 Azure-SSIS IR을 만들면 Azure Batch 공급자가 자동으로 등록됩니다.)

   1. Azure 포털에서 왼쪽 메뉴에서 **구독을 선택합니다.**

   1. 구독을 선택합니다.

   1. 왼쪽에서 리소스 **공급자를**선택하고 **Microsoft.Batch가** 등록된 공급자임을 확인합니다.

   !["등록됨" 상태 확인](media/join-azure-ssis-integration-runtime-virtual-network/batch-registered-confirmation.png)

   목록에서 **Microsoft.Batch**가 보이지 않으면 지금 등록할 수 있도록 구독에서 [빈 Azure Batch 계정을 만듭니다](../batch/batch-account-create-portal.md). 나중에 삭제할 수 있습니다.

## <a name="join-the-azure-ssis-ir-to-a-virtual-network"></a>Azure-SSIS IR을 가상 네트워크에 조인

Azure Resource Manager 가상 네트워크 또는 클래식 가상 네트워크를 구성한 후 Azure-SSIS IR을 가상 네트워크에 조인할 수 있습니다.

1. Microsoft Edge 또는 Google Chrome을 시작합니다. 현재 이러한 웹 브라우저만 데이터 팩터리 UI를 지원합니다.

1. Azure [포털에서](https://portal.azure.com)왼쪽 메뉴에서 **데이터 팩터리를**선택합니다. 메뉴에 **데이터 팩터가** 표시되지 않으면 **더 많은 서비스를**선택한 다음 인텔리전스 + **분석** 섹션에서 **데이터 팩터리를**선택합니다.

   ![데이터 팩터리 목록](media/join-azure-ssis-integration-runtime-virtual-network/data-factories-list.png)

1. 목록에서 Azure-SSIS IR을 사용하여 데이터 팩터리를 선택합니다. 데이터 팩터리의 홈 페이지가 표시됩니다. 모니터 **작성** 자 & 선택합니다. 별도의 탭에 Data Factory UI가 표시됩니다.

   ![데이터 팩터리 홈페이지](media/join-azure-ssis-integration-runtime-virtual-network/data-factory-home-page.png)

1. 데이터 팩터리 UI에서 **편집** 탭으로 전환하고 **연결**을 선택한 다음, **통합 런타임** 탭으로 전환합니다.

   !["통합 런타임" 탭](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtimes-tab.png)

1. Azure-SSIS IR이 실행 중인 경우 **통합 런타임** 목록에서 **작업** 열에서 Azure-SSIS IR에 대한 **중지** 단추를 선택합니다. Azure-SSIS IR을 중지할 때까지 편집할 수 없습니다.

   ![IR 중지](media/join-azure-ssis-integration-runtime-virtual-network/stop-ir-button.png)

1. 통합 **런타임** 목록에서 **작업** 열에서 Azure-SSIS IR에 대한 **편집** 단추를 선택합니다.

   ![통합 런타임 편집](media/join-azure-ssis-integration-runtime-virtual-network/integration-runtime-edit.png)

1. 통합 런타임 설정 패널에서 **다음** 단추를 선택하여 **일반 설정** 및 **SQL 설정** 섹션을 진행합니다.

1. 고급 **설정** 섹션에서:
   1. **Azure-SSIS 통합 런타임에 대한 VNet 선택을 선택하고, ADF가 특정 네트워크 리소스를 만들 수 있도록 허용하고, 선택적으로 정적 공용 IP 주소 확인란을 가져올 수 있습니다.**

   1. **구독**에 대해서는 가상 네트워크가 있는 Azure 구독을 선택합니다.

   1. **위치**에는 통합 런타임의 동일한 위치가 선택됩니다.

   1. **유형의**경우 가상 네트워크 유형(클래식 또는 Azure 리소스 관리자)을 선택합니다. 클래식 가상 네트워크가 곧 더 이상 사용되지 않으므로 Azure 리소스 관리자 가상 네트워크를 선택하는 것이 좋습니다.

   1. **VNet 이름**에서 가상 네트워크의 이름을 선택합니다. SSISDB를 호스트하기 위해 가상 네트워크 서비스 끝점 또는 개인 끝점이 있는 관리되는 인스턴스가 있는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 온-프레미스 네트워크에 연결된 것과 같아야 합니다. 그렇지 않으면 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오는 가상 네트워크가 될 수 있습니다.

   1. **서브넷 이름**에서 가상 네트워크의 서브넷 이름을 선택합니다. SSISDB를 호스트하기 위해 가상 네트워크 서비스 끝점이 있는 Azure SQL Database 서버에 사용되는 것과 동일해야 합니다. 또는 SSISDB를 호스트하기 위해 개인 끝점을 사용하여 관리되는 인스턴스에 사용되는 서브넷과 다른 서브넷이어야 합니다. 그렇지 않으면 Azure-SSIS IR에 대한 정적 공용 IP 주소를 가져오는 모든 서브넷이 될 수 있습니다.

   1. **VNet 유효성 검사**를 선택합니다. 유효성 검사가 성공하면 **계속을**선택합니다.

   ![가상 네트워크 관련 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

1. **요약** 섹션에서 Azure-SSIS IR에 대한 모든 설정을 검토합니다. 그런 다음 **업데이트를**선택합니다.

1. Azure-SSIS IR에 대한 **작업** 열에서 **시작** 단추를 선택하여 Azure-SSIS IR을 시작합니다. 가상 네트워크에 연결되는 Azure-SSIS IR을 시작하는 데 약 20~30분이 걸립니다.

## <a name="next-steps"></a>다음 단계

[Azure-SSIS IR을 가상 네트워크에 가입하는](join-azure-ssis-integration-runtime-virtual-network.md)방법에 대해 자세히 알아봅니다.
