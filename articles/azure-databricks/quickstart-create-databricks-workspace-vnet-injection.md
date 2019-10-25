---
title: Virtual Network에서 Azure Databricks 작업 영역 만들기
description: 이 문서에서는 가상 네트워크에 Azure Databricks를 배포 하는 방법을 설명 합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 69afe2aab3c10707f7160d727b970ad73d59a952
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791552"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>빠른 시작: Virtual Network에서 Azure Databricks 작업 영역 만들기

이 빠른 시작에서는 가상 네트워크에서 Azure Databricks 작업 영역을 만드는 방법을 보여 줍니다. 또한 해당 작업 영역 내에 Apache Spark 클러스터를 만듭니다.

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure portal](https://portal.azure.com/)에 로그인합니다.

> [!Note]
> 이 자습서는 **Azure 평가판 구독**을 사용하여 수행할 수 없습니다.
> 무료 계정이 있는 경우 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요. 그런 다음 [지출 한도를 제거](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit)하고 해당 지역의 vCPU에 대한 [할당량 증가를 요청](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)합니다. Azure Databricks 작업 영역을 만드는 경우 **평가판(프리미엄-14일 무료 DBU)** 가격 책정 계층을 선택하여 14일간 무료 프리미엄 Azure Databricks DBU를 위한 작업 영역 액세스 권한을 부여할 수 있습니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 선택 합니다.

2. **가상 네트워크 만들기**에서 다음 설정을 적용 합니다. 

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |name|databricks-빠른 시작|가상 네트워크의 이름을 선택 합니다.|
    |주소 공간|10.1.0.0/16|CIDR 표기법으로 된 가상 네트워크의 주소 범위입니다.|
    |Subscription|\<구독\>|사용할 Azure 구독을 선택합니다.|
    |Resource group|databricks-빠른 시작|**새로 만들기** 를 선택 하 고 계정에 대 한 새 리소스 그룹 이름을 입력 합니다.|
    |위치|\<사용자와 가장 가까운 지역 선택\>|가상 네트워크를 호스트할 수 있는 지리적 위치를 선택 합니다. 사용자와 가장 가까운 위치를 사용합니다.|
    |서브넷 이름|기본값|가상 네트워크에서 기본 서브넷의 이름을 선택 합니다.|
    |서브넷 주소 범위|10.1.0.0/24|CIDR 표기법의 서브넷의 주소 범위입니다. 가상 네트워크의 주소 공간에 포함 되어야 합니다. 사용 중인 서브넷의 주소 범위는 편집할 수 없습니다.|

    ![Azure Portal에서 가상 네트워크 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 배포가 완료 되 면 가상 네트워크로 이동 하 고 **설정**아래에서 **주소 공간** 을 선택 합니다. *추가 주소 범위 추가*상자에 `10.179.0.0/16` 삽입 하 고 **저장**을 선택 합니다.

    ![Azure 가상 네트워크 주소 공간](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

1. Azure Portal에서 **리소스 만들기** > **분석** > **Databricks**를 선택 합니다.

2. **Azure Databricks 서비스**에서 다음 설정을 적용 합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |작업 영역 이름|databricks-빠른 시작|Azure Databricks 작업 영역에 대 한 이름을 선택 합니다.|
    |Subscription|\<구독\>|사용할 Azure 구독을 선택합니다.|
    |Resource group|databricks-빠른 시작|가상 네트워크에 사용한 것과 동일한 리소스 그룹을 선택 합니다.|
    |위치|\<사용자와 가장 가까운 지역 선택\>|가상 네트워크와 동일한 위치를 선택 합니다.|
    |가격 책정 계층|표준 또는 프리미엄 중에서 선택 합니다.|가격 책정 계층에 대 한 자세한 내용은 [Databricks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/databricks/)를 참조 하세요.|
    |Virtual Network에서 Azure Databricks 작업 영역 배포|yes|이 설정을 사용 하면 가상 네트워크에 Azure Databricks 작업 영역을 배포할 수 있습니다.|
    |Virtual Network|databricks-빠른 시작|이전 섹션에서 만든 가상 네트워크를 선택 합니다.|
    |공용 서브넷 이름|공용 서브넷|기본 공용 서브넷 이름을 사용 합니다.|
    |공용 서브넷 CIDR 범위|10.179.64.0/18|이 서브넷의 CIDR 범위는/18에서/26 사이 여야 합니다.|
    |개인 서브넷 이름|개인-서브넷|기본 개인 서브넷 이름을 사용 합니다.|
    |개인 서브넷 CIDR 범위|10.179.0.0/18|이 서브넷의 CIDR 범위는/18에서/26 사이 여야 합니다.|

    ![Azure Portal에서 Azure Databricks 작업 영역 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 배포가 완료 되 면 Azure Databricks 리소스로 이동 합니다. 가상 네트워크 피어 링이 사용 하지 않도록 설정 되어 있는지 확인 합니다. 또한 개요 페이지에서 리소스 그룹 및 관리 되는 리소스 그룹을 확인 합니다. 

    ![Azure Databricks 개요 Azure Portal](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    관리 되는 리소스 그룹에는 저장소 계정 (DBFS), 작업자-sg (네트워크 보안 그룹), 작업자-vnet (가상 네트워크)의 실제 위치가 포함 되어 있습니다. 가상 컴퓨터, 디스크, IP 주소 및 네트워크 인터페이스가 생성 되는 위치 이기도 합니다. 이 리소스 그룹은 기본적으로 잠겨 있습니다. 그러나 클러스터가 가상 네트워크에서 시작 되 면 관리 되는 리소스 그룹의 작업자-vnet과 "허브" 가상 네트워크 사이에 네트워크 인터페이스가 생성 됩니다.

    ![Azure Databricks 관리 되는 리소스 그룹](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>클러스터 만들기

> [!NOTE]
> 무료 계정을 사용하여 Azure Databricks 클러스터를 만들려면 클러스터를 만들기 전에 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요.

1. Azure Databricks 서비스로 돌아가서 **개요** 페이지에서 **작업 영역 시작** 을 선택 합니다.

2. 클러스터 ** > ** **+ 클러스터 만들기**를 선택 합니다. 그런 다음 *databricks-cluster*와 같은 클러스터 이름을 만들고 나머지 기본 설정을 적용 합니다. **클러스터 만들기**를 선택합니다.

    ![Azure Databricks 클러스터 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 클러스터가 실행 되 면 Azure Portal에서 관리 되는 리소스 그룹으로 돌아갑니다. 새 가상 머신, 디스크, IP 주소 및 네트워크 인터페이스를 확인 합니다. IP 주소를 사용 하 여 각 공용 및 개인 서브넷에 네트워크 인터페이스가 생성 됩니다.  

    ![클러스터를 만든 후 관리 되는 리소스 그룹 Azure Databricks](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Azure Databricks 작업 영역으로 돌아가서 만든 클러스터를 선택 합니다. 그런 다음 **SPARK UI** 페이지의 **실행자** 탭으로 이동 합니다. 드라이버 및 실행 기의 주소가 개인 서브넷 범위에 있는지 확인 합니다. 이 예제에서 드라이버는 10.179.0.6이 고 실행자는 10.179.0.4 및 10.179.0.5입니다. IP 주소는 다를 수 있습니다.

    ![Azure Databricks Spark UI 실행자](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서가 완료되면 클러스터를 종료할 수 있습니다. 이렇게 하려면 왼쪽 창의 Azure Databricks 작업 영역에서 **클러스터**를 선택합니다. 종료하려는 클러스터에서 **작업** 열 아래의 줄임표 위로 커서를 이동한 다음, **종료** 아이콘을 선택합니다. 클러스터를 중지 합니다.

클러스터를 수동으로 종료하지 않은 경우 클러스터를 만드는 중에 **비활성 \_\_분 후 종료** 확인란을 선택하면 자동으로 중지됩니다. 이 경우 지정한 시간 동안 클러스터가 비활성 상태이면 클러스터가 자동으로 중지됩니다.

클러스터를 다시 사용 하지 않으려는 경우 Azure Portal에서 만든 리소스 그룹을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크에 배포 된 Azure Databricks에서 Spark 클러스터를 만들었습니다. Azure Databricks 노트북에서 JDBC를 사용 하 여 가상 네트워크에서 SQL Server Linux Docker 컨테이너를 쿼리 하는 방법에 대해 알아보려면 다음 문서로 이동 합니다.  

> [!div class="nextstepaction"]
>[Azure Databricks 노트북에서 가상 네트워크의 SQL Server Linux Docker 컨테이너 쿼리](vnet-injection-sql-server.md)
