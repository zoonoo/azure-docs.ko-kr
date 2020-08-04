---
title: '자습서: Synapse 작업 영역 만들기 시작'
description: 이 자습서에서는 Synapse 작업 영역, SQL 풀 및 Apache Spark 풀을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b4d48dcc8f09ae8e2ec3bb198f8864de1c945682
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093901"
---
# <a name="create-a-synapse-workspace"></a>Synapse 작업 영역 만들기

이 자습서에서는 Synapse 작업 영역, SQL 풀 및 Apache Spark 풀을 만드는 방법에 대해 알아봅니다. 

## <a name="prepare-a-storage-account"></a>스토리지 계정 준비

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 다음 설정이 있는 새 스토리지 계정을 만듭니다.

    |탭|설정 | 제안 값 | Description |
    |---|---|---|---|
    |기본 사항|**스토리지 계정 이름**| 이름을 선택합니다.| 이 문서에서는 **contosolake** 이름을 사용합니다.|
    |기본 사항|**계정 종류**| **StorageV2** ||
    |기본 사항|**위치**|임의의 위치를 선택합니다.| Azure Synapse Analytics 작업 영역과 Azure Data Lake Storage Gen2 계정이 동일한 지역에 있는 것이 좋습니다.|
    |고급|**Data Lake Storage Gen2**|**Enabled**| Azure Synapse는 이 설정을 사용하도록 설정된 스토리지 계정에서만 작동합니다.|
    |||||

1. 스토리지 계정이 만들어지면 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다. 그런 다음, 다음 역할을 할당하거나 이미 할당되어 있는지 확인합니다.
    * 사용자를 **소유자** 역할에 할당합니다.
    * 사용자를 **Storage Blob 데이터 소유자** 역할에 할당합니다.
1. 왼쪽 창에서 **컨테이너**를 선택하여 컨테이너를 만듭니다.
1. 임의의 컨테이너 이름을 지정할 수 있습니다. 이 문서에서는 컨테이너 이름을 **users**로 지정합니다.
1. **공용 액세스 수준** 기본 설정을 적용한 다음, **만들기**를 선택합니다.

다음 단계에서는 이 스토리지 계정을 "기본" 스토리지 계정으로 사용하고, 작업 영역 데이터를 저장하는 컨테이너를 사용하도록 Azure Synapse 작업 영역을 구성합니다. 작업 영역은 데이터를 Apache Spark 테이블에 저장합니다. Spark 애플리케이션 로그를 **/synapse/workspacename**이라는 폴더에 저장합니다.

## <a name="create-a-synapse-workspace"></a>Synapse 작업 영역 만들기

1. [Azure Portal](https://portal.azure.com)을 열고, 위쪽에서 **Synapse**를 검색합니다.
1. **서비스** 아래의 검색 결과에서 **Azure Synapse Analytics(작업 영역 미리 보기)** 를 선택합니다.
1. **추가**를 선택하여 다음 설정을 사용하는 작업 영역을 만듭니다.

    |탭|설정 | 제안 값 | Description |
    |---|---|---|---|
    |기본 사항|**작업 영역 이름**|원하는 이름을 지정할 수 있습니다.| 이 문서에서는 **myworkspace**를 사용합니다.|
    |기본 사항|**지역**|스토리지 계정의 지역과 일치합니다.|

1. **Data Lake Storage Gen 2 선택** 아래에서 이전에 만든 계정과 컨테이너를 선택합니다.
1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 분 안에 작업 영역이 준비됩니다.

## <a name="verify-access-to-the-storage-account"></a>스토리지 계정에 대한 액세스 확인

Azure Synapse 작업 영역의 관리 ID는 이미 스토리지 계정에 액세스할 수 있습니다. 다음 단계에 따라 확인합니다.

1. [Azure Portal](https://portal.azure.com) 및 작업 영역에 대해 선택한 기본 스토리지 계정을 엽니다.
1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. 다음 역할을 할당하거나 이미 할당되어 있는지 확인합니다. 작업 영역 ID와 작업 영역 이름에 동일한 이름을 사용합니다.
    * 스토리지 계정에 대한 **Storage Blob 데이터 기여자** 역할의 경우 **myworkspace**를 작업 영역 ID로 할당합니다.
    * **myworkspace**를 작업 영역 이름으로 할당합니다.

1. **저장**을 선택합니다.

## <a name="open-synapse-studio"></a>Synapse Studio 열기

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://portal.azure.com)에서 Synapse 작업 영역을 엽니다. **개요** 섹션의 위쪽에서 **Synapse Studio 시작**을 선택합니다.
* `https://web.azuresynapse.net`(으)로 이동하여 작업 영역에 로그인합니다.

## <a name="create-a-sql-pool"></a>SQL 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **SQL 풀**을 차례로 선택합니다.
1. **새로 만들기**를 선택하고 다음 설정을 입력합니다.

    |설정 | 제안 값 | 
    |---|---|---|
    |**SQL 풀 이름**| **SQLDB1**|
    |**성능 수준**|**DW100C**|
    |||

1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 분 후에 SQL 풀이 준비됩니다. SQL 풀이 **SQLDB1**이라고도 하는 SQL 풀 데이터베이스와 연결됩니다.

SQL 풀은 활성 상태인 동안 청구 가능한 리소스를 사용합니다. 비용을 줄이기 위해 풀을 일시 중지할 수 있습니다.

## <a name="create-an-apache-spark-pool"></a>Apache Spark 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **Apache Spark 풀**을 차례로 선택합니다.
1. **새로 만들기**를 선택하고 다음 설정을 입력합니다.

    |설정 | 제안 값 | 
    |---|---|---|
    |**Apache Spark 풀 이름**|**Spark1**
    |**노드 크기**| **소형**|
    |**노드 수**| 최솟값 및 최댓값을 각각 3으로 설정합니다.|

1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 초 후에 Apache Spark 풀이 준비됩니다.

> [!NOTE]
> 이름과 관계없이 Apache Spark 풀은 SQL 풀과 다릅니다. 이는 Spark와 상호 작용하는 방법을 Synapse 작업 영역에 알리는 데 사용하는 몇 가지 기본 메타데이터일 뿐입니다.

메타데이터이므로 Spark 풀을 시작하거나 중지할 수 없습니다.

Azure Synapse에서 Spark 작업을 수행할 때 사용할 Spark 풀을 지정합니다. 이 풀은 사용할 Spark 리소스의 수를 Azure Synapse에 알려줍니다. 사용하는 리소스에 대해서만 비용을 지불합니다. 풀 사용을 적극적으로 중지하면 리소스가 자동으로 시간 제한되고 재활용됩니다.

> [!NOTE]
> Spark 데이터베이스는 Spark 풀에서 독립적으로 만들어집니다. 작업 영역에는 항상 **default**라는 Spark 데이터베이스가 있습니다. Spark 데이터베이스는 추가로 만들 수 있습니다.

## <a name="the-sql-on-demand-pool"></a>SQL 주문형 풀

모든 작업 영역에는 **SQL 주문형**이라는 미리 빌드된 풀이 제공됩니다. 이 풀은 삭제할 수 없습니다. SQL 주문형 풀을 사용하면 Azure Synapse에서 SQL 풀을 만들거나 이를 관리하도록 고려하지 않고도 SQL을 사용할 수 있습니다.

다른 종류의 풀과 달리 SQL 주문형에 대한 요금 청구는 쿼리를 실행하는 데 사용된 리소스의 수가 아니라 쿼리를 실행하는 데 검사된 데이터의 양을 기준으로 합니다.

* SQL 주문형에는 SQL 주문형 풀과 별도로 존재하는 자체 SQL 주문형 데이터베이스가 있습니다.
* 작업 영역에는 항상 **SQL 주문형**이라는 하나의 SQL 주문형 풀만 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SQL 풀을 사용하여 분석](get-started-analyze-sql-pool.md)
