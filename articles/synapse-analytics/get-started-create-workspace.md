---
title: '빠른 시작: 시작 - Synapse 작업 영역 만들기'
description: 이 자습서에서는 Synapse 작업 영역, 전용 SQL 풀 및 서버리스 Apache Spark 풀을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: f7b96bcebb2106e52c62426ca2b64f9305e09141
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515412"
---
# <a name="creating-a-synapse-workspace"></a>Synapse 작업 영역 만들기

이 자습서에서는 Synapse 작업 영역, 전용 SQL 풀 및 서버리스 Apache Spark 풀을 만드는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 단계를 완료하려면 **소유자** 역할이 할당된 리소스 그룹에 대한 액세스 권한이 있어야 합니다. 이 리소스 그룹에 Synapse 작업 영역을 만듭니다.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure Portal에서 Synapse 작업 영역 만들기

1. [Azure Portal](https://portal.azure.com)을 열고, 위쪽에서 **Synapse** 를 검색합니다.
1. **서비스** 아래의 검색 결과에서 **Azure Synapse Analytics(작업 영역 미리 보기)** 를 선택합니다.
1. **추가** 를 선택하여 작업 영역을 만듭니다.
1. **기본 사항** 에서 원하는 **구독**, **리소스 그룹**, **지역** 을 입력한 다음, 작업 영역 이름을 선택합니다. 이 자습서에서는 **myworkspace** 를 사용합니다.
1. **Data Lake Storage Gen 2** 로 이동합니다. 
1. **새로 만들기** 를 클릭하고 이름을 **contosolake** 로 지정합니다.
1. **파일 시스템** 을 클릭하고 **사용자** 의 이름을 지정합니다. 이렇게 하면 **users** 라는 컨테이너가 생성됩니다.
1. 작업 영역에서는 이 스토리지 계정을 Spark 테이블 및 Spark 애플리케이션 로그에 대한 "기본" 스토리지 계정으로 사용합니다.
1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 분 안에 작업 영역이 준비됩니다.

## <a name="open-synapse-studio"></a>Synapse Studio 열기

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://portal.azure.com)에서 Synapse 작업 영역을 엽니다. **개요** 섹션의 위쪽에서 **Synapse Studio 시작** 을 선택합니다.
* `https://web.azuresynapse.net`(으)로 이동하여 작업 영역에 로그인합니다.

## <a name="create-a-dedicated-sql-pool"></a>전용 SQL 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **SQL 풀** 을 차례로 선택합니다.
1. **새로 만들기** 를 선택하고 다음 설정을 입력합니다.

    |설정 | 제안 값 | 
    |---|---|---|
    |**SQL 풀 이름**| **SQLDB1**|
    |**성능 수준**|**DW100C**|
    |||

1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 분 안에 전용 SQL 풀이 준비됩니다. 전용 SQL 풀이 **SQLDB1** 이라고도 하는 전용 SQL 풀 데이터베이스와 연결됩니다.

전용 SQL 풀은 활성 상태인 동안 청구 가능한 리소스를 사용합니다. 비용을 줄이기 위해 풀을 일시 중지할 수 있습니다.

## <a name="create-a-serverless-apache-spark-pool"></a>서버리스 Apache Spark 풀 만들기

1. Synapse Studio의 왼쪽 창에서 **관리** > **Apache Spark 풀** 을 차례로 선택합니다.
1. **새로 만들기** 를 선택하고 다음 설정을 입력합니다.

    |설정 | 제안 값 | 
    |---|---|---|
    |**Apache Spark 풀 이름**|**Spark1**
    |**노드 크기**| **소형**|
    |**노드 수**| 최솟값 및 최댓값을 각각 3으로 설정합니다.|

1. **검토 + 만들기** > **만들기** 를 차례로 선택합니다. 몇 초 후에 Apache Spark 풀이 준비됩니다.

Azure Synapse에서 Spark 작업을 수행할 때 사용할 Spark 풀을 지정합니다. 이 풀은 사용할 Spark 리소스의 수를 Azure Synapse에 알려줍니다. 사용하는 리소스에 대해서만 비용을 지불합니다. 풀 사용을 적극적으로 중지하면 리소스가 자동으로 시간 제한되고 재활용됩니다.

> [!NOTE]
> Spark 데이터베이스는 Spark 풀에서 독립적으로 만들어집니다. 작업 영역에는 항상 **default** 라는 Spark 데이터베이스가 있습니다. Spark 데이터베이스는 추가로 만들 수 있습니다.

## <a name="the-serverless-sql-pool"></a>서버리스 SQL 풀

모든 작업 영역에는 **기본 제공** 이라는 미리 빌드된 풀이 제공됩니다. 이 풀은 삭제할 수 없습니다. 서버리스 SQL 풀을 사용하면 Azure Synapse에서 서버리스 SQL 풀을 만들거나 관리할 필요 없이 SQL을 사용할 수 있습니다. 전용 SQL 풀과 달리 서버리스 SQL 풀에 대한 요금 청구는 쿼리를 실행하는 데 사용된 리소스의 수가 아니라 쿼리를 실행하는 데 검사된 데이터의 양을 기준으로 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [전용 SQL 풀을 사용하여 분석](get-started-analyze-sql-pool.md)
