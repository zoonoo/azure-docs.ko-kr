---
title: 가상 네트워크에서 Azure Databricks 작업 영역 만들기
description: 이 문서에서는 Azure Databricks를 가상 네트워크에 배포 하는 방법을 설명 합니다.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: 295b64b10f9f78ca6224d60fb84c6d1310aaa42e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60770720"
---
# <a name="quickstart-create-an-azure-databricks-workspace-in-a-virtual-network"></a>빠른 시작: 가상 네트워크에서 Azure Databricks 작업 영역 만들기

이 빠른 시작에는 가상 네트워크에서 Azure Databricks 작업 영역을 만드는 방법을 보여 줍니다. 또한 해당 작업 영역 내에서 Apache Spark 클러스터를 생성합니다.

Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure Portal](https://portal.azure.com/)에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure portal에서 **리소스 만들기** > **네트워킹** > **가상 네트워크**를 선택합니다.

2. **가상 네트워크 만들기** 아래에서, 다음 설정을 적용합니다. 

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |이름|databricks-quickstart|가상 네트워크에 대한 이름을 선택합니다.|
    |주소 공간|10.1.0.0/16|CIDR 표기법으로 된 가상 네트워크의 주소 범위입니다.|
    |구독|\<구독\>|사용할 Azure 구독을 선택합니다.|
    |리소스 그룹|databricks-quickstart|선택 **새로 만들기** 계정의 새 리소스 그룹 이름을 입력 합니다.|
    |Location|\<사용자와 가장 가까운 지역 선택\>|가상 네트워크를 호스트할 수 있는 지리적 위치를 선택 합니다. 사용자와 가장 가까운 위치를 사용합니다.|
    |서브넷 이름|기본값|가상 네트워크의 기본 서브넷의 이름을 선택 합니다.|
    |서브넷 주소 범위|10.1.0.0/24|CIDR 표기법의 서브넷의 주소 범위입니다. 가상 네트워크의 주소 공간에 포함 되어야 합니다. 사용 중인 서브넷의 주소 범위를 편집할 수 없습니다.|

    ![Azure portal에서 가상 네트워크 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-virtual-network.png)

3. 배포가 완료되면 가상 네트워크로 이동하고 **설정** 아래에서 **주소 공간**을 선택합니다. *주소 범위 더 추가* 라고 표시된 상자에  `10.179.0.0/16`을 추가하고 **저장**을 선택합니다.

    ![Azure virtual network 주소 공간](./media/quickstart-create-databricks-workspace-vnet-injection/add-address-space.png)

## <a name="create-an-azure-databricks-workspace"></a>Azure Databricks 작업 영역 만들기

1. Azure portal에서 **리소스 만들기** > **분석** > **Azure Databricks**를 선택합니다.

2. **Azure Databricks Service** 아래에서, 다음 설정을 적용합니다.

    |설정|제안 값|설명|
    |-------|---------------|-----------|
    |작업 영역 이름|databricks-quickstart|Azure Databricks 작업 영역에 대 한 이름을 선택 합니다.|
    |구독|\<구독\>|사용할 Azure 구독을 선택합니다.|
    |리소스 그룹|databricks-quickstart|가상 네트워크에 사용한 동일한 리소스 그룹을 선택 합니다.|
    |Location|\<사용자와 가장 가까운 지역 선택\>|가상 네트워크와 동일한 위치를 선택 합니다.|
    |가격 책정 계층|표준 또는 프리미엄 중에서 선택.|가격 책정 계층에 대한 자세한 내용은 [Databricks 가격 책정 페이지](https://azure.microsoft.com/pricing/details/databricks/)를 참조합니다.|
    |가상 네트워크에서 Azure Databricks 작업 영역 배포|예|이 설정을 통해 가상 네트워크에서 Azure Databricks 작업 영역을 배포할 수 있습니다.|
    |Virtual Network|databricks-quickstart|이전 섹션에서 만든 가상 네트워크를 선택 합니다.|
    |공용 서브넷 이름|public-subnet|기본 공용 서브넷 이름을 사용 합니다.|
    |공용 서브넷 CIDR 범위|10.179.64.0/18|이 서브넷에 대한 CIDR 범위 /18과 26 사이여야 합니다.|
    |개인 서브넷 이름|private-subnet|기본 개인 서브넷 이름을 사용 합니다.|
    |개인 서브넷 CIDR 범위|10.179.0.0/18|이 서브넷에 대한 CIDR 범위 /18과 26 사이여야 합니다.|

    ![Azure portal에서 Azure Databricks 작업 영역 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-databricks-workspace.png)

3. 배포가 완료되면 Azure Databricks 리소스로 이동합니다. 가상 네트워크 피어링이 해제된 것을 확인합니다. 또한 리소스 그룹 및 관리되는 리소스 그룹은 개요 페이지에서 확인할 수 있습니다. 

    ![Azure portal에서 azure Databricks 개요](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-overview-portal.png)

    저장소 계정 DBFS (), 작업자 sg (네트워크 보안 그룹), 작업자-vnet (가상 네트워크)의 실제 위치를 포함 하는 관리 되는 리소스 그룹. 가상 컴퓨터, 디스크, IP 주소 및 네트워크 인터페이스 만들어지는 위치 이기도 합니다. 기본적으로이 리소스 그룹이 잠겨 그러나 클러스터 가상 네트워크에서 시작 되 면 네트워크 인터페이스 관리 되는 리소스 그룹의 작업자 vnet와 "허브" 가상 네트워크 간에 만들어집니다.

    ![Azure Databricks 관리 되는 리소스 그룹](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group.png)

## <a name="create-a-cluster"></a>클러스터 만들기

> [!NOTE]
> 무료 계정을 사용하여 Azure Databricks 클러스터를 만들려면 클러스터를 만들기 전에 프로필로 이동하고 구독을 **종량제**로 변경합니다. 자세한 내용은 [Azure 체험 계정](https://azure.microsoft.com/free/)을 참조하세요.

1. Azure Databricks 서비스로 돌아와서 **개요** 페이지에서 **작업 영역 시작**을 선택합니다.

2. 선택 **클러스터** > **클러스터 만들기 +** 합니다. 같은 클러스터 이름, 만든 *databricks-빠른 시작-클러스터*, 나머지 기본 설정을 수락 합니다. **클러스터 만들기**를 선택합니다.

    ![Azure Databricks 클러스터 만들기](./media/quickstart-create-databricks-workspace-vnet-injection/create-cluster.png)

3. 클러스터가 실행되면 Azure portal에서 관리되는 리소스 그룹으로 돌아갑니다. 새 가상 컴퓨터, 디스크, IP 주소 및 네트워크 인터페이스를 확인합니다. 네트워크 인터페이스는 각 IP 주소를 사용하여 공용 및 개인 서브넷에서 생성됩니다.  

    ![클러스터를 만든 후 azure Databricks 관리 되는 리소스 그룹](./media/quickstart-create-databricks-workspace-vnet-injection/managed-resource-group2.png)

4. Azure Databricks 작업 영역으로 돌아가서 생성한 클러스터를 선택합니다. 그리고 **Spark UI** 페이지의 **Executors** 탭으로 이동합니다. 드라이버와 실행기에 대한 주소는 개인 서브넷 범위에 있음을 알 수 있습니다. 이 예제에서 드라이버는 10.179.0.6 이며 실행기는 10.179.0.4 및 10.179.0.5입니다. 사용자의 IP 주소는 다를 수 있습니다.

    ![Azure Databricks Spark UI 실행 기](./media/quickstart-create-databricks-workspace-vnet-injection/databricks-sparkui-executors.png)

## <a name="clean-up-resources"></a>리소스 정리

이 문서가 완료되면 클러스터를 종료할 수 있습니다. 이렇게 하려면 왼쪽 창의 Azure Databricks 작업 영역에서 **클러스터**를 선택합니다. 종료하려는 클러스터에서 **작업** 열 아래의 줄임표 위로 커서를 이동한 다음, **종료** 아이콘을 선택합니다. 이 클러스터를 중지합니다.

클러스터를 수동으로 종료하지 않은 경우 클러스터를 만드는 중에 **비활성 \_\_분 후 종료** 확인란을 선택하면 자동으로 중지됩니다. 이 경우 지정한 시간 동안 클러스터가 비활성 상태이면 클러스터가 자동으로 중지됩니다.

클러스터를 다시 사용하지 않으려면 Azure portal에서 만든 리소스 그룹을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 가상 네트워크에 배포된 Azure Databricks에서 Spark 클러스터를 만들었습니다. Azure Databricks notebook에서 JDBC를 사용하여 가상 네트워크의 SQL Server Linux Docker 컨테이너를 쿼리하는 방법을 알아보려면 다음 문서로 이동합니다.  

> [!div class="nextstepaction"]
>[Azure Databricks notebook에서 가상 네트워크의 SQL Server Linux Docker 컨테이너 쿼리](vnet-injection-sql-server.md)
