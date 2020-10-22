---
title: '자습서: Synapse 작업 영역 만들기 시작'
description: 이 자습서에서는 Synapse 작업 영역, SQL 풀 및 Apache Spark 풀을 만드는 방법에 대해 알아봅니다.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: 1b33fa6ea3b5aa5933c1f969f1f1a07aaec79373
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173323"
---
# <a name="creating-a-synapse-workspace"></a>Synapse 작업 영역 만들기

이 자습서에서는 Synapse 작업 영역, SQL 풀 및 Apache Spark 풀을 만드는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 자습서의 모든 단계를 완료하려면 **소유자** 역할이 할당된 리소스 그룹에 대한 액세스 권한이 있어야 합니다. 이 리소스 그룹에 Synapse 작업 영역을 만듭니다.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Azure Portal에서 Synapse 작업 영역 만들기

1. [Azure Portal](https://portal.azure.com)을 열고, 위쪽에서 **Synapse**를 검색합니다.
1. **서비스** 아래의 검색 결과에서 **Azure Synapse Analytics(작업 영역 미리 보기)** 를 선택합니다.
1. **추가**를 선택하여 작업 영역을 만듭니다.
1. **기본 사항**에서 원하는 **구독**, **리소스 그룹**, **지역**을 입력한 다음, 작업 영역 이름을 선택합니다. 이 자습서에서는 **myworkspace**를 사용합니다.
1. 작업 영역을 만들려면 ADLSGEN2 계정과 해당 계정의 컨테이너가 필요합니다. 새 항목을 만드는 것이 가장 간단합니다. 기존 항목을 다시 사용하려는 경우 몇 가지 추가 구성을 수행해야 합니다. 
    1. Synapse 작업 영역은 이 컨테이너를 기본 위치로 사용하여 Spark 로그 및 Spark 테이블에 대한 데이터를 저장합니다.
1. 옵션 1: 새 ADLSGEN2 계정 만들기 
    1. **Data Lake Storage Gen 2**로 이동합니다. 
    1. **새로 만들기**를 클릭하고 이름을 **contosolake**로 지정합니다.
    1. **파일 시스템**을 클릭하고 **사용자**의 이름을 지정합니다. 이렇게 하면 **users**라는 컨테이너가 생성됩니다.
1. 옵션 2: 기존 ADLSGEN2 계정을 사용합니다. 이 문서 하단에 있는 **ADLSGEN2 스토리지 계정 준비** 지침을 참조하세요.
1. Azure Synapse 작업 영역에서는 이 스토리지 계정을 "기본" 스토리지 계정으로 사용하고 작업 영역 데이터를 저장하는 컨테이너로 사용합니다. 작업 영역은 데이터를 Apache Spark 테이블에 저장합니다. Spark 애플리케이션 로그를 **/synapse/workspacename**이라는 폴더에 저장합니다.
1. **검토 + 만들기** > **만들기**를 차례로 선택합니다. 몇 분 안에 작업 영역이 준비됩니다.

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

## <a name="preparing-a-adlsgen2-storage-account"></a>ADLSGEN2 스토리지 계정 준비

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>작업 영역을 만들기 전에 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.
1. 기존 스토리지 계정으로 이동합니다.
1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다. 
1. 다음 역할을 할당하거나 이미 할당되어 있는지 확인합니다.
    * 사용자를 **소유자** 역할에 할당합니다.
    * 사용자를 **Storage Blob 데이터 소유자** 역할에 할당합니다.
1. 왼쪽 창에서 **컨테이너**를 선택하여 컨테이너를 만듭니다.
1. 컨테이너에 이름을 지정할 수 있습니다. 이 문서에서는 **users**라는 이름을 사용합니다.
1. **공용 액세스 수준** 기본 설정을 적용한 다음, **만들기**를 선택합니다.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>작업 영역을 만든 후 다음 단계를 수행합니다.

작업 영역에서 스토리지 계정에 대한 액세스를 구성합니다. Azure Synapse 작업 영역의 관리 ID는 이미 스토리지 계정에 액세스할 수 있습니다. 다음 단계에 따라 확인합니다.

1. [Azure Portal](https://portal.azure.com) 및 작업 영역에 대해 선택한 기본 스토리지 계정을 엽니다.
1. 왼쪽 창에서 **액세스 제어(IAM)** 를 선택합니다.
1. 다음 역할을 할당하거나 이미 할당되어 있는지 확인합니다. 작업 영역 ID와 작업 영역 이름에 동일한 이름을 사용합니다.
    * 스토리지 계정에 대한 **Storage Blob 데이터 기여자** 역할의 경우 **myworkspace**를 작업 영역 ID로 할당합니다.
    * **myworkspace**를 작업 영역 이름으로 할당합니다.
1. **저장**을 선택합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [SQL 풀을 사용하여 분석](get-started-analyze-sql-pool.md)
