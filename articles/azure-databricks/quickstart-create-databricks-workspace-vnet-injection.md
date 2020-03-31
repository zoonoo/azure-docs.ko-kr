---
title: 자체 가상 네트워크 퀵스타트에서 Azure Databricks 작업 영역 만들기
description: 이 문서에서는 가상 네트워크에 Azure Databricks를 배포하는 방법을 설명합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 03/23/2020
ms.openlocfilehash: 63dd1b4d9396d340dd17a7afb92ff9c38a2b38b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132671"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-your-own-virtual-network"></a>빠른 시작: 자체 가상 네트워크에서 Azure Databricks 작업 영역 만들기

Azure Databricks의 기본 배포는 Databricks에서 관리하는 새 가상 네트워크를 만듭니다. 이 빠른 시작은 대신 자신의 가상 네트워크에서 Azure Databricks 작업 영역을 만드는 방법을 보여 줍니다. 또한 해당 작업 영역 내에서 아파치 스파크 클러스터를 작성합니다. 

자체 가상 네트워크에서 Azure Databricks 작업 영역을 만들도록 선택할 수 있는 이유에 대한 자세한 내용은 [Azure 가상 네트워크(VNet 주입)에서 Azure Databricks 배포를](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)참조하십시오.

Azure 구독이 없는 경우 [무료 계정을](https://azure.microsoft.com/free/databricks/)만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털에](https://portal.azure.com/)로그인합니다.

> [!Note]
> 이 자습서는 **Azure 평가판 구독**을 사용하여 수행할 수 없습니다.
> 무료 계정이 있는 경우 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요. 그런 다음 [지출 한도를 제거](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)하고 해당 지역의 vCPU에 대한 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)합니다. Azure Databricks 작업 영역을 만드는 경우 **평가판(프리미엄-14일 무료 DBU)** 가격 책정 계층을 선택하여 14일간 무료 프리미엄 Azure Databricks DBU를 위한 작업 영역 액세스 권한을 부여할 수 있습니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal 메뉴에서 **리소스 만들기**를 선택합니다. 그런 다음 **네트워킹 > 가상 네트워크를**선택합니다.

    ![Azure 포털에서 가상 네트워크 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-portal.png)

2. **가상 네트워크 만들기에서**다음 설정을 적용합니다. 

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |Subscription|\<구독\>|사용할 Azure 구독을 선택합니다.|
    |Resource group|데이터 브릭 빠른 시작|**새 만들기를** 선택하고 계정에 새 리소스 그룹 이름을 입력합니다.|
    |이름|데이터 브릭 빠른 시작|가상 네트워크의 이름을 선택합니다.|
    |지역|\<사용자와 가장 가까운 지역 선택\>|가상 네트워크를 호스팅할 수 있는 지리적 위치를 선택합니다. 사용자와 가장 가까운 위치를 사용합니다.|

    ![Azure 포털의 가상 네트워크에 대한 기본 사항](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. **다음 을 선택합니다: IP 주소>** 다음 설정을 적용합니다. 그런 다음 **검토 + 만들기를**선택합니다.
    
    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |IPv4 주소 공간|10.2.0.0/16|CIDR 표기형의 가상 네트워크의 주소 범위입니다. CIDR 범위는 /16에서 /24 사이여야 합니다.|
    |서브넷 이름|default|가상 네트워크에서 기본 서브넷의 이름을 선택합니다.|
    |서브넷 주소 범위|10.2.0.0/24|CIDR 표기법의 서브넷의 주소 범위입니다. 가상 네트워크의 주소 공간에 포함되어야 합니다. 사용 중인 서브넷의 주소 범위는 편집할 수 없습니다.|

    ![Azure 포털에서 가상 네트워크에 대한 IP 구성 설정](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network-ip-config.png)

4. 검토 **+ 만들기** 탭에서 가상 네트워크를 배포하려면 **만들기를** 선택합니다. 배포가 완료되면 가상 네트워크로 이동하여 **설정**에서 **주소 공간을** 선택합니다. 주소 범위를 추가하라는 상자에 `10.179.0.0/16` *삽입하고* **저장을**선택합니다.

    ![Azure 가상 네트워크 주소 공간](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

1. Azure Portal 메뉴에서 **리소스 만들기**를 선택합니다. 그런 다음 **데이터 브릭에 > 분석을 선택합니다.**

    ![Azure 포털에서 Azure Databricks 작업 영역 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-portal.png)

2. **Azure Databricks 서비스에서**다음 설정을 적용합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |작업 영역 이름|데이터 브릭 빠른 시작|Azure Databricks 작업 영역의 이름을 선택합니다.|
    |Subscription|\<구독\>|사용할 Azure 구독을 선택합니다.|
    |Resource group|데이터 브릭 빠른 시작|가상 네트워크에 사용한 것과 동일한 리소스 그룹을 선택합니다.|
    |위치|\<사용자와 가장 가까운 지역 선택\>|가상 네트워크와 동일한 위치를 선택합니다.|
    |가격 책정 계층|표준 또는 프리미엄 중에서 선택합니다.|가격 책정 계층에 대한 자세한 내용은 [Databricks 가격 책정 페이지를](https://azure.microsoft.com/pricing/details/databricks/)참조하십시오.|

    ![Azure 데이터 브릭 작업 영역 기본 사항 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. **기본** 페이지에서 설정 입력을 완료한 후 **다음: 네트워킹 >** 선택하고 다음 설정을 적용합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |가상 네트워크(VNet)에 Azure Databricks 작업 영역 배포|yes|이 설정을 사용하면 가상 네트워크에 Azure Databricks 작업 영역을 배포할 수 있습니다.|
    |Virtual Network|데이터 브릭 빠른 시작|이전 섹션에서 만든 가상 네트워크를 선택합니다.|
    |공용 서브넷 이름|공용 서브넷|기본 공용 서브넷 이름을 사용합니다.|
    |퍼블릭 서브넷 CIDR 범위|10.179.64.0/18|/26까지의 CIDR 범위를 사용합니다.|
    |개인 서브넷 이름|프라이빗 서브넷|기본 개인 서브넷 이름을 사용합니다.|
    |프라이빗 서브넷 CIDR 범위|10.179.0.0/18|/26까지의 CIDR 범위를 사용합니다.|

    ![Azure 포털의 Azure Databricks 작업 영역에 VNet 정보 추가](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace-vnet-config.png)

3. 배포가 완료되면 Azure Databricks 리소스로 이동합니다. 가상 네트워크 피어링이 비활성화되어 있습니다. 또한 개요 페이지에서 리소스 그룹 및 관리되는 리소스 그룹을 확인합니다. 

    ![Azure 포털의 Azure 데이터브릭 개요](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    관리되는 리소스 그룹은 수정할 수 없으며 가상 컴퓨터를 만드는 데 사용되지 않습니다. 관리하는 리소스 그룹에서만 가상 컴퓨터를 만들 수 있습니다.

    ![Azure Databricks 관리 리소스 그룹](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

    작업 영역 배포에 실패해도 작업 영역은 여전히 실패한 상태로 만들어집니다. 실패한 작업 영역을 삭제하고 배포 오류를 해결하는 새 작업 영역을 만듭니다. 실패한 작업 영역을 삭제하면 관리되는 리소스 그룹 및 성공적으로 배포된 리소스도 삭제됩니다.

## <a name="create-a-cluster"></a>클러스터 만들기

> [!NOTE]
> 무료 계정을 사용하여 Azure Databricks 클러스터를 만들려면 클러스터를 만들기 전에 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요.

1. Azure Databricks 서비스로 돌아가서 **개요** 페이지에서 **작업 영역 시작을** 선택합니다.

2. **클러스터** > 선택 **+ 클러스터 만들기**. 그런 다음 *databricks-quickstart-cluster와*같은 클러스터 이름을 만들고 나머지 기본 설정을 수락합니다. **클러스터 만들기**를 선택합니다.

    ![Azure 데이터브릭 클러스터 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 클러스터가 실행되면 Azure 포털에서 관리되는 리소스 그룹으로 돌아갑니다. 새 가상 컴퓨터, 디스크, IP 주소 및 네트워크 인터페이스를 확인합니다. 네트워크 인터페이스는 IP 주소가 있는 각 공용 및 개인 서브넷에 만들어집니다.  

    ![클러스터 생성 후 Azure Databricks 관리 리소스 그룹](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Azure Databricks 작업 영역으로 돌아가서 만든 클러스터를 선택합니다. 그런 다음 **스파크 UI** 페이지의 **실행기** 탭으로 이동합니다. 드라이버와 실행기의 주소는 개인 서브넷 범위에 있습니다. 이 예제에서 드라이버는 10.179.0.6이고 실행기는 10.179.0.4 및 10.179.0.5입니다. IP 주소가 다를 수 있습니다.

    ![Azure 데이터 브릭 스파크 UI 실행기](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서가 완료되면 클러스터를 종료할 수 있습니다. 이렇게 하려면 왼쪽 창의 Azure Databricks 작업 영역에서 **클러스터**를 선택합니다. 종료하려는 클러스터에서 **작업** 열 아래의 줄임표 위로 커서를 이동한 다음, **종료** 아이콘을 선택합니다. 이렇게 하면 클러스터가 중지됩니다.

클러스터를 수동으로 종료하지 않으면 클러스터를 만드는 동안 몇 **분 \_ \_ 후에 종료** 를 선택한 경우 클러스터가 자동으로 중지됩니다. 이 경우 지정한 시간 동안 클러스터가 비활성 상태이면 클러스터가 자동으로 중지됩니다.

클러스터를 다시 사용하지 않으려면 Azure Portal에서 만든 리소스 그룹을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크에 배포한 Azure Databricks에서 Spark 클러스터를 만들었습니다. Azure Databricks 노트북에서 JDBC를 사용하여 가상 네트워크에서 SQL Server Linux Docker 컨테이너를 쿼리하는 방법을 알아보려면 다음 문서로 이동합니다.  

> [!div class="nextstepaction"]
>[Azure Databricks 노트북에서 가상 네트워크에서 SQL Server Linux Docker 컨테이너 쿼리](vnet-injection-sql-server.md)
