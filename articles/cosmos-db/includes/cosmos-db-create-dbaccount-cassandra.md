---
title: 포함 파일
description: 포함 파일
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 07/02/2021
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 8f9bf2d231e09c5d2aa636ab8cf03cef42bc58d1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733250"
---
1. Azure Portal 메뉴 또는 **홈** 페이지에서 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **Azure Cosmos DB** 를 검색하여 선택합니다.

1. **Azure Cosmos DB** 페이지에서 **만들기** 를 선택합니다.

1. **Azure Cosmos DB 계정 만들기** 페이지에서 새 Azure Cosmos 계정에 대한 기본 설정을 입력합니다.

   |설정|값|Description |
   |---|---|---|
   | Subscription|사용자의 구독|이 Azure Cosmos DB 계정에 사용하려는 Azure 구독을 선택합니다. |
   | 리소스 그룹|새로 만들기<br><br>그런 다음, 계정 이름과 같은 이름 입력|**새로 만들기** 를 선택합니다. 그런 다음, 계정의 새 리소스 그룹 이름을 입력합니다. 간단히 하기 위해 Azure Cosmos 계정 이름과 동일한 이름을 사용합니다. |
   | 계정 이름|고유한 이름을 입력합니다.|내 Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다. 계정 URI는 고유한 계정 이름에 *cassandra.cosmos.azure.com* 이 추가됩니다.<br><br>계정 이름은 소문자, 숫자 및 하이픈(-) 문자만 포함할 수 있으며, 3-31자여야 합니다.|
   | API|Cassandra|API는 만들 계정의 형식을 결정합니다. Azure Cosmos DB는 문서 데이터베이스용 Core(SQL), 그래프 데이터베이스용 Gremlin, 문서 데이터베이스용 MongoDB, Azure Table 및 Cassandra의 5가지 API를 제공합니다. 각 API에 대한 별도의 계정을 만들어야 합니다. <br><br>이 빠른 시작에서는 Cassandra API와 함께 작동하는 테이블을 만들고 있으므로 **Cassandra** 를 선택합니다. <br><br>[Cassandra API에 대해 자세히 알아보세요](../cassandra/cassandra-introduction.md).|
   |위치|사용자와 가장 가까운 지역|Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빨리 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.|
   |용량 모드|프로비저닝된 처리량 또는 서버리스|**프로비저닝된 처리량** 을 선택하여 [프로비저닝된 처리량](../set-throughput.md) 모드에서 계정을 만듭니다. **서버리스** 를 선택하여 [서버리스](../serverless.md) 모드에서 계정을 만듭니다.|
   |Azure Cosmos DB 체험 계층 할인 적용|**적용** 또는 **적용 안 함**|Azure Cosmos DB 체험 계층을 사용하는 경우 처음에는 1000RU/초 및 25GB의 스토리지가 계정에 무료로 제공됩니다. [체험 계층](https://azure.microsoft.com/pricing/details/cosmos-db/)에 대해 자세히 알아보세요.|

   > [!NOTE]
   > Azure 구독당 최대 1개의 체험 계층 Azure Cosmos DB 계정을 사용할 수 있으며 계정을 만들 때 옵트인해야 합니다. 체험 계층 할인을 적용하는 옵션이 표시되지 않으면 구독의 다른 계정에서 이미 체험 계층을 사용하도록 설정되었음을 의미합니다.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-cassandra/azure-cosmos-db-create-new-account.png" alt-text="Azure Cosmos DB Cassandra API에 대한 새 계정 페이지":::

1. **전역 배포** 탭에서 다음 세부 정보를 구성합니다. 이 빠른 시작의 목적을 위해 기본값을 그대로 둘 수 있습니다.

   |설정|값|Description |
   |---|---|---|
   |지리적 중복|사용 안 함|지역에 쌍 영역을 페어링하여 계정에서 글로벌 배포를 사용하거나 사용하지 않도록 설정합니다. 나중에 계정에 더 많은 지역을 추가할 수 있습니다.|
   |다중 지역 쓰기|사용 안 함|다중 영역 쓰기 기능을 사용하면 전 세계의 데이터베이스 및 컨테이너에 대해 프로비저닝된 처리량을 활용할 수 있습니다.|

   > [!NOTE]
   > **용량 모드** 로 **서버리스** 를 선택한 경우 다음 옵션을 사용할 수 없습니다.
   > - 체험 계층 할인 적용
   > - 지리적 중복
   > - 다중 지역 쓰기

1. 필요에 따라 다음 탭에서 추가 세부 정보를 구성할 수 있습니다.

   * **네트워킹** - [가상 네트워크에서 액세스](../how-to-configure-vnet-service-endpoint.md)를 구성합니다.
   * **백업 정책** - [주기적](../configure-periodic-backup-restore.md) 또는 [지속적인](../provision-account-continuous-backup.md) 백업 정책을 구성합니다.
   * **암호화** - 서비스 관리형 키 또는 [고객 관리형 키](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account)를 사용합니다.
   * **태그** - 태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다.

1. **검토 + 만들기** 를 선택합니다.

1. 계정 설정을 검토한 다음, **만들기** 를 선택합니다. 계정을 만드는 데 몇 분이 걸립니다. 포털 페이지에 **배포가 완료됨** 이 표시되기를 기다립니다.

   :::image type="content" source="./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png" alt-text="Azure Portal 알림 창":::

1. **리소스로 이동** 을 선택하여 Azure Cosmos DB 계정 페이지로 이동합니다. 
