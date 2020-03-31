---
title: Azure 스트림 분석 사용
description: 실시간 솔루션을 개발하기 위해 Azure Synapse의 데이터 웨어하우스와 함께 Azure Stream Analytics를 사용하는 팁입니다.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 2/5/2020
ms.author: martinle
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: feb7b52c84e5e702202bc668cfda676d291ea82e
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350431"
---
# <a name="use-azure-stream-analytics-with-azure-synapse-analytics"></a>Azure 시냅스 분석과 함께 Azure 스트림 분석 사용

Azure Stream Analytics는 완전히 관리되는 서비스로, 클라우드의 스트리밍 데이터에 대해 대기 시간이 짧고 확장성이 뛰어난 고가용성의 복합 이벤트 처리 기능을 제공합니다. [Azure Stream Analytics 소개](../../stream-analytics/stream-analytics-introduction.md)를 읽어 기본 사항을 배울 수 있습니다. [Azure Stream Analytics를 사용하여 시작](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) 자습서에 따라 Stream Analytics로 엔드투엔드 솔루션을 만드는 방법에 대해 알 수 있습니다.

이 문서에서는 Azure Stream Analytics 작업에 대한 출력 싱크로 데이터 웨어하우스를 사용하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 스트림 분석 작업 - Azure 스트림 분석 작업을 만들려면 [Azure 스트림 분석](../../stream-analytics/stream-analytics-real-time-fraud-detection.md) 자습서를 사용하여 시작하기 의 단계를 수행합니다.  

    1. 이벤트 허브 입력 만들기
    2. 이벤트 생성기 애플리케이션 구성 및 시작
    3. Stream Analytics 작업 프로비전
    4. 작업 입력 및 쿼리 지정
* Azure Synapse SQL 풀 데이터 웨어하우스 - 새 데이터 웨어하우스를 만들려면 빠른 시작의 단계를 따라 [새 데이터 웨어하우스를 만듭니다.](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)

## <a name="specify-streaming-output-to-point-to-your-data-warehouse"></a>데이터 웨어하우스를 가리키는 스트리밍 출력 지정

### <a name="step-1"></a>1단계

Azure 포털에서 스트림 분석 작업으로 이동하여 **작업 토폴로지** 메뉴 에서 **출력을 클릭합니다.**

### <a name="step-2"></a>2단계

**추가** 단추를 클릭하고 드롭다운 메뉴에서 **SQL Database를** 선택합니다.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutput.png)

### <a name="step-3"></a>3단계

다음 값을 입력합니다.

* *출력 별칭*: 이 작업 출력에 대해 친숙한 이름을 입력합니다.
* *구독*:
  * 데이터 웨어하우스가 스트림 분석 작업과 동일한 구독에 있는 경우 ***구독에서 SQL 데이터베이스 선택을 클릭합니다.***
  * 데이터베이스가 다른 구독에 있는 경우 SQL Database 데이터베이스 제공 설정을 수동으로 클릭합니다.
* *데이터베이스*: 드롭다운 목록에서 대상 데이터베이스를 선택합니다.
* *사용자 이름*: 데이터베이스에 대한 쓰기 권한이 있는 계정의 사용자 이름을 지정합니다.
* *암호*: 지정된 사용자 계정에 대한 암호를 제공합니다.
* *테이블*: 데이터베이스에서 대상 테이블의 이름을 지정합니다.
* 저장 버튼을 **클릭합니다.**

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaoutputdbsettings.png)

### <a name="step-4"></a>4단계:

테스트를 실행하려면 전에 데이터 웨어하우스에서 테이블을 만들어야 합니다.  SQL Server 관리 스튜디오(SSMS) 또는 선택한 쿼리 도구를 사용하여 다음 테이블 만들기 스크립트를 실행합니다.

```sql
CREATE TABLE SensorLog
(
    RecordType VARCHAR(2)
    , SystemIdentity VARCHAR(2)
    , FileNum INT
    , SwitchNum VARCHAR(50)
    , CallingNum VARCHAR(25)
    , CallingIMSI VARCHAR(25)
    , CalledNum VARCHAR(25)
    , CalledIMSI VARCHAR(25)
    , DateS VARCHAR(25)
    , TimeS VARCHAR(25)
    , TimeType INT
    , CallPeriod INT
    , CallingCellID VARCHAR(25)
    , CalledCellID VARCHAR(25)
    , ServiceType VARCHAR(25)
    , [Transfer] INT
    , IncomingTrunk VARCHAR(25)
    , OutgoingTrunk VARCHAR(25)
    , MSRN VARCHAR(25)
    , CalledNum2 VARCHAR(25)
    , FCIFlag VARCHAR(25)
    , callrecTime VARCHAR(50)
    , EventProcessedUtcTime VARCHAR(50)
    , PartitionId int
    , EventEnqueuedUtcTime VARCHAR(50)
    )
WITH (DISTRIBUTION = ROUND_ROBIN)
```

### <a name="step-5"></a>5단계

스트림 분석 작업에 대한 Azure 포털에서 작업 이름을 클릭합니다.  ***출력 세부 정보*** 창에서 ***테스트*** 단추를 클릭합니다.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asatest.png)데이터베이스에 대한 연결이 성공하면 포털에 알림이 표시됩니다.

### <a name="step-6"></a>6단계

***작업 토폴로지*** 에서 ***쿼리*** 메뉴를 클릭하고 쿼리를 변경하여 만든 스트림 출력에 데이터를 삽입합니다.  ***선택한 쿼리 테스트*** 단추를 클릭하여 쿼리를 테스트합니다.  쿼리 테스트가 성공하면 ***쿼리 저장*** 단추를 클릭합니다.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asaquery.png)

### <a name="step-7"></a>7단계

Azure 스트림 분석 작업을 시작합니다.  개요 메뉴에서 ***시작*** 버튼을 ***클릭합니다.***

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastart.png)

시작 작업 창에서 ***시작*** 버튼을 클릭합니다.

![](./media/sql-data-warehouse-integrate-azure-stream-analytics/sqlpool-asastartconfirm.png)

## <a name="next-steps"></a>다음 단계

통합 개요는 [다른 서비스 통합을](sql-data-warehouse-overview-integrate.md)참조하십시오.
자세한 개발 팁은 [데이터 웨어하우스에 대한 설계 결정 및 코딩 기술을](sql-data-warehouse-overview-develop.md)참조하십시오.
