---
title: 데이터베이스에서 Azure로 대량 복사 Azure Data Factory 템플릿 사용 데이터 탐색기
description: 이 항목에서는 데이터베이스에서 Azure로 대량 복사를 위해 Azure Data Factory 템플릿을 사용 하는 방법에 대해 알아봅니다 데이터 탐색기
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: acfcee6e8308bc508bd709c40bd1794beab3130f
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804731"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>데이터베이스에서 Azure로 대량 복사 Azure Data Factory 템플릿 사용 데이터 탐색기

Azure 데이터 탐색기는 응용 프로그램, 웹 사이트 및 IoT 장치와 같은 많은 원본에서 많은 양의 데이터 스트리밍을 실시간으로 분석 하기 위한 완전히 관리 되는 데이터 분석 서비스입니다. Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. Azure Data Factory 서비스를 사용 하 여 Azure 데이터 탐색기 데이터베이스를 기존 시스템의 데이터로 채우고 분석 솔루션을 빌드할 때 시간을 절약할 수 있습니다. 

[Azure Data Factory 템플릿은](/azure/data-factory/solution-templates-introduction) Data Factory을 신속 하 게 시작 하 고 데이터 통합 프로젝트를 빌드하기 위한 개발 시간을 줄일 수 있도록 미리 정의 된 Azure Data Factory 파이프라인입니다. **데이터베이스에서 Azure 데이터 탐색기로 대량 복사** 는 **조회** 및 **ForEach** 활동을 사용 하 여 생성 됩니다. 이 템플릿을 사용 하 여 데이터를 빠르게 복사할 수 있도록 데이터베이스나 테이블당 많은 파이프라인을 만들 수 있습니다. 

> [!IMPORTANT]
> * **데이터베이스에서 azure 데이터 탐색기로 대량 복사** 를 사용 하 여 SQL Server 및 Google 이상 쿼리와 같은 데이터베이스의 많은 양의 데이터를 azure 데이터 탐색기에 복사 합니다. 
> * [복사 도구](data-factory-load-data.md) 를 사용 하 여 적은 양의 데이터를 포함 하는 몇 개의 테이블을 Azure 데이터 탐색기에 복사 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [Azure 데이터 탐색기 클러스터 및 데이터베이스](create-cluster-database-portal.md)
* [데이터 팩터리 만들기](data-factory-load-data.md#create-a-data-factory)
* 데이터베이스의 데이터 원본

## <a name="create-controltabledataset"></a>ControlTableDataset 만들기

**Controltabledataset** 은 파이프라인의 원본에서 대상으로 복사할 데이터를 나타냅니다. 행 수는 데이터를 복사 하는 데 필요한 총 파이프라인 수를 표시 합니다. **Controltabledataset** 은 원본 데이터베이스의 일부로 정의 해야 합니다.

SQL Server 원본 테이블 형식의 예:
    
    ```sql   
    CREATE TABLE control_table (
        PartitionId int,
        SourceQuery varchar(255),
        ADXTableName varchar(255)
    );
    ```
    
        |속성  |Description  | 예제
        |---------|---------| ---------|
        |PartitionId   |   복사 순서 | 1  |  
        |SourceQuery   |   파이프라인 런타임 중에 복사할 데이터를 나타내는 쿼리입니다. | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
        |ADXTableName  |  대상 테이블 이름 | MyAdxTable       |  

**Controltabledataset** 의 형식이 다른 경우 형식에 대 한 비교할 수 있는 **controltabledataset** 을 만듭니다.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사 사용

1. **시작 하기** 페이지에서 템플릿에서 **파이프라인 만들기** 타일을 선택 하거나, 템플릿에서 오른쪽  > > **+** **파이프라인** 의 연필 아이콘 (**작성자** 탭)을 선택 하 여 템플릿 갤러리를 엽니다.

    ![Azure Data Factory 시작 하겠습니다.](media/data-factory-template/adf-get-started.png)

1. **데이터베이스에서 Azure 데이터 탐색기로 템플릿 대량 복사를**선택 합니다.
 
    ![템플릿에서 파이프라인 선택](media/data-factory-template/pipeline-from-template.png)

1.  **데이터베이스에서 Azure로 대량 복사 데이터 탐색기** 창에서 드롭다운에서 기존 데이터 집합을 선택 합니다. 

    * **Controltabledataset** 연결 된 서비스는 원본에서 대상으로 복사 되는 데이터와 대상에 저장 되는 데이터를 나타내는 테이블을 제어 합니다. 
    * **Sourcedataset** – 원본 데이터베이스에 연결 된 서비스입니다. 
    * **AzureDataExplorerTable** -Azure 데이터 탐색기 테이블. 데이터 집합이 없는 경우 [Azure 데이터 탐색기 연결 된 서비스를 만들어](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) 데이터 집합을 추가 합니다.
    * **이 템플릿 사용**을 선택 합니다.

    ![Azure 데이터 탐색기 템플릿 대량 복사 구성](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. 활동 외부에서 캔버스의 영역을 선택 하 여 템플릿 파이프라인에 액세스 합니다. **이름** (컨트롤 테이블 이름) 및 **기본값** (열 이름)을 포함 하 여 테이블에 대 한 매개 변수를 입력 하려면 **매개 변수** 를 선택 합니다.

    ![파이프라인 매개 변수](media/data-factory-template/pipeline-parameters.png)

1.  기본 설정을 보려면 조회 작업, **Get파티션 목록**을 차례로 선택 합니다. 쿼리가 자동으로 만들어집니다.
1.  명령 작업 **ForEachPartition**를 선택 하 고 **설정** 을 선택 합니다.
    * **일괄 처리 수**: 1-50에서 숫자를 선택 합니다. 이 선택은 **Controltabledataset** 의 행 수에 도달할 때까지 병렬로 실행 되는 파이프라인 수를 결정 합니다. 
    * 파이프라인 배치가 병렬로 실행 되도록 **순차** 확인란을 선택 하지 마십시오.

    ![ForEachPartition 설정](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > 데이터를 더 빠르게 복사할 수 있도록 여러 파이프라인을 병렬로 실행 하는 것이 가장 좋습니다. 원본 테이블의 데이터를 분할 하 고 날짜 및 테이블에 따라 파이프라인 당 파티션을 할당 하 여 효율성을 높입니다.

1. **모두 유효성 검사** 를 선택 하 여 ADF 파이프라인의 유효성을 검사 합니다. **파이프라인 유효성 검사 출력**을 참조 하세요.

    ![템플릿 파이프라인 유효성 검사](media/data-factory-template/validate-template-pipelines.png)

1. 필요한 경우 **디버그**를 선택한 다음 트리거를 **추가** 하 여 파이프라인을 실행 합니다.

    ![파이프라인 실행](media/data-factory-template/trigger-run-of-pipeline.png)    


이제 **데이터베이스에서 Azure 데이터 탐색기 템플릿으로 대량 복사** 를 사용 하 여 데이터베이스 및 테이블에서 대량의 데이터를 효율적으로 복사할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Factory를 사용 하 여 Azure 데이터 탐색기에 데이터를 복사](data-factory-load-data.md)하는 절차에 대해 알아봅니다.

* Azure Data Factory의 [Azure 데이터 탐색기 커넥터](/azure/data-factory/connector-azure-data-explorer) 에 대해 알아봅니다.

* 데이터 쿼리를 위한 [Azure 데이터 탐색기 쿼리](/azure/data-explorer/web-query-data) 에 대해 알아봅니다.






