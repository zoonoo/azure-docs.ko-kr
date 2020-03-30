---
title: 스트림 분석 통합을 사용하여 데이터 스트림(미리 보기)
description: Azure 스트림 분석을 사용하여 Azure SQL 데이터베이스로 데이터를 스트리밍합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: d233d3c98cc495e4b9e84142781f5eb9faa7eec8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820837"
---
# <a name="stream-data-by-using-azure-sql-database-stream-analytics-integration-preview"></a>Azure SQL 데이터베이스 스트림 분석 통합(미리 보기)을 사용하여 데이터 스트리밍

이제 사용자는 [Azure Stream Analytics를](../stream-analytics/stream-analytics-introduction.md)사용하여 Azure 포털의 SQL 데이터베이스에서 직접 실시간 스트리밍 데이터를 테이블로 수집, 처리, 보기 및 분석할 수 있습니다. 이 경험을 통해 커넥티드 카, 원격 모니터링, 사기 탐지 등 다양한 시나리오를 수행할 수 있습니다. Azure 포털에서 이벤트 소스(이벤트 허브/IoT Hub)를 선택하고, 들어오는 실시간 이벤트를 보고, 이벤트를 저장할 테이블을 선택할 수 있습니다. 포털에서 검색 분석 쿼리 언어 쿼리쿼리를 작성하여 들어오는 이벤트를 변환하고 선택한 테이블에 저장할 수도 있습니다. 이 새로운 진입점은 스트림 애널리틱스에 이미 존재하는 생성 및 구성 환경과 추가됩니다. 이 환경은 데이터베이스의 컨텍스트에서 시작하여 Stream Analytics 작업을 신속하게 설정하고 Azure SQL Database 및 Stream Analytics 환경 간에 원활하게 탐색할 수 있습니다.

![스트림 분석 흐름](media/sql-database-stream-analytics/stream-analytics-flow.png)

## <a name="key-benefits"></a>주요 이점

- 최소 컨텍스트 전환: 포털의 SQL Database에서 시작하여 다른 서비스로 전환하지 않고 실시간 데이터를 테이블로 수집할 수 있습니다. 
- 단계 수 감소: 데이터베이스 및 테이블의 컨텍스트를 사용하여 스트림 분석 작업을 미리 구성합니다.
- 미리 보기 데이터로 사용 편의성 추가: 선택한 테이블의 컨텍스트에서 이벤트 소스(이벤트 허브/IoT Hub)에서 들어오는 데이터를 미리 보기 


## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

- Azure 구독 Azure 구독이 없는 경우 [무료 계정을 만듭니다.](https://azure.microsoft.com/free/) 
- SQL 데이터베이스입니다. 자세한 내용은 [Azure SQL 데이터베이스에서 단일 데이터베이스 만들기를](sql-database-single-database-get-started.md)참조하십시오.
- 컴퓨터가 Azure SQL 서버에 연결할 수 있도록 허용하는 방화벽 규칙입니다. 자세한 내용은 [서버 수준 방화벽 규칙 만들기를](sql-database-server-level-firewall-rule.md)참조하십시오.


## <a name="configure-stream-analytics-integration"></a>스트림 분석 통합 구성

1. Azure Portal에 로그인합니다. 
2. 스트리밍 데이터를 수집하려는 SQL 데이터베이스로 이동합니다. **스트림 분석(미리 보기)을 선택합니다.** 

    ![Stream Analytics](media/sql-database-stream-analytics/stream-analytics.png)

3. 스트리밍 데이터를 이 SQL 데이터베이스로 수집하려면 스트리밍 작업에 이름 **만들기및** 이름을 지정한 다음 **다음: 입력을**선택합니다. 

    ![스트림 분석 작업 만들기](media/sql-database-stream-analytics/create-job.png)

4. 이벤트 소스 세부 정보를 입력한 다음 다음: 출력 을 **선택합니다.**

   - **입력 유형**: 이벤트 허브/IoT 허브
   - **입력 별칭**: 이름을 입력하여 이벤트 소스를 식별합니다. 
   - **구독**: SQL 데이터베이스 구독과 동일 
   - **이벤트 허브 네임스페이스**: 네임스페이스의 이름 
   - **이벤트 허브 이름**: 선택한 네임스페이스 내의 이벤트 허브 이름 
   - **이벤트 허브 정책 이름(새** 정책 만들기 기본값): 정책 이름 지정 
   - **이벤트 허브 소비자 그룹(새** 만들기 기본값): 소비자 그룹 이름 지정  
     - 여기에서 만드는 각 새 Azure Stream Analytics 작업에 대한 소비자 그룹과 정책을 만드는 것이 좋습니다. 소비자 그룹은 5개의 동시 판독기만 허용하므로 각 작업에 대해 전용 소비자 그룹을 제공하면 해당 제한을 초과하여 발생할 수 있는 오류를 방지할 수 있습니다. 전용 정책을 사용하면 다른 리소스에 영향을 주지 않고 키를 회전하거나 권한을 취소할 수 있습니다.

    ![스트림 분석 작업 만들기](media/sql-database-stream-analytics/create-job-output.png)

5. 스트리밍 데이터를 수집할 테이블을 선택합니다. 완료되면 에서 **만들기를**선택합니다.
   - **사용자 이름,** **암호**: SQL 서버 인증에 대한 자격 증명을 입력합니다. **유효성 검사**를 선택합니다.
   - **표**: **새 만들기** 또는 기존 **사용**선택. 이 흐름에서 **만들기를**선택해 보겠습니다. 이렇게 하면 검색 어마션 작업을 시작할 때 새 테이블이 생성됩니다.

    ![스트림 분석 작업 만들기](media/sql-database-stream-analytics/create.png)

6. 다음과 같은 세부 정보로 쿼리 페이지가 열립니다.

   - 데이터를 수집할 **입력(입력** 이벤트 소스)  
   - 변환된 데이터를 저장하는 **출력(출력** 테이블) 
   - SELECT 문을 사용하여 [SAQL 쿼리를](../stream-analytics/stream-analytics-stream-analytics-query-patterns.md) 샘플링합니다. 
   - **입력 미리 보기**: 입력 이벤트 소스에서 최신 수신 데이터의 스냅샷을 표시합니다.  
     - 데이터의 직렬화 유형이 자동으로 검색됩니다(JSON/CSV). JSON/CSV/AVRO로도 수동으로 변경할 수 있습니다. 
     - 테이블 형식 또는 Raw 형식으로 들어오는 데이터를 미리 볼 수 있습니다. 
     - 표시된 데이터가 최신 데이터가 아닌 경우 **새로 고침을** 선택하여 최신 이벤트를 확인합니다. 
     - **시간 범위 선택을** 선택하여 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트합니다. 
     - 샘플 JSON/CSV 파일을 업로드하여 쿼리를 테스트하려면 **샘플 입력 업로드를** 선택합니다. SAQL 쿼리 테스트에 대한 자세한 내용은 [샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트를](../stream-analytics/stream-analytics-test-query.md)참조하십시오. 

    ![테스트 쿼리](media/sql-database-stream-analytics/test-query.png)


   - **테스트 결과**: **테스트 쿼리를** 선택하고 스트리밍 쿼리의 결과를 볼 수 있습니다. 

    ![테스트 결과](media/sql-database-stream-analytics/test-results.png)

   - **테스트 결과 스키마**: 테스트 후 스트리밍 쿼리 결과의 스키마를 표시합니다. 테스트 결과 스키마가 출력 스키마와 일치하는지 확인합니다. 

    ![테스트 결과 스키마](media/sql-database-stream-analytics/test-results-schema.png)


   - **출력 스키마**: 여기에는 5단계에서 선택한 테이블의 스키마(새 또는 기존)가 포함됩니다.
     - 새 만들기: 5단계에서 이 옵션을 선택한 경우 스트리밍 작업을 시작할 때까지 스키마가 아직 표시되지 않습니다. 새 테이블을 만들 때 적절한 테이블 인덱스를 선택합니다. 테이블 인덱싱에 대한 자세한 내용은 [설명된 클러스터및 비클러스터된 인덱스를](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)참조하십시오.
    - 기존 사용: 5단계에서 이 옵션을 선택한 경우 선택한 테이블의 스키마가 표시됩니다. 
 
7. 쿼리 테스트 & 작성이 완료되면 **쿼리 저장을**선택합니다. **변환된** 데이터를 SQL 테이블로 수집하려면 스트림 분석 시작 작업을 선택합니다. 다음 필드를 완료하면 작업을 **시작합니다.** 
   - **출력 시작 시간**: 작업의 첫 번째 출력 시간을 정의합니다.  
     - 이제 작업이 지금 시작되고 새 들어오는 데이터를 처리합니다.
     - 사용자 지정: 작업이 지금 시작되지만 특정 시점(과거 또는 미래에 있을 수 있음)의 데이터를 처리합니다. 자세한 내용은 [Azure 스트림 분석 작업을 시작하는 방법을 참조하세요.](../stream-analytics/start-job.md)
   - **스트리밍 단위**: Azure Stream Analytics는 서비스로 데이터를 처리하는 데 필요한 스트리밍 단위 수에 따라 가격이 책정됩니다. 자세한 내용은 [Azure 스트림 분석 가격 책정을](https://azure.microsoft.com/pricing/details/stream-analytics/)참조하십시오. 
   - **출력 데이터 오류 처리**:  
     - 다시 시도: 오류가 발생하면 Azure Stream Analytics는 쓰기가 성공할 때까지 이벤트를 무기한 으로 다시 작성합니다. 재시도에 대한 시간 제한은 없습니다. 결국, 재시도 중인 이벤트에 의해 모든 후속 이벤트의 처리가 차단됩니다. 이 옵션은 기본 출력 오류 처리 정책입니다. 
     - 드롭: Azure Stream Analytics는 데이터 변환 오류를 초래하는 모든 출력 이벤트를 삭제합니다. 삭제된 이벤트는 나중에 다시 처리하도록 복구할 수 없습니다. 모든 일시적인 오류(예: 네트워크 오류)는 출력 오류 처리 정책 구성과 상관없이 다시 시도됩니다. 
   - **SQL Database 출력 설정**: 이전 쿼리 단계의 분할 체계를 상속하여 여러 작성기를 테이블에 완전히 병렬토폴로지할 수 있는 옵션입니다. 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](../stream-analytics/stream-analytics-sql-output-perf.md)을 참조하세요.
   - **최대 일괄 처리 수**: 모든 대량 삽입 트랜잭션과 함께 전송되는 레코드 수에 대한 권장 상한입니다.  
    출력 오류 처리에 대한 자세한 내용은 [Azure Stream Analytics의 출력 오류 정책을](../stream-analytics/stream-analytics-output-error-policy.md)참조하십시오.  

    ![작업 시작](media/sql-database-stream-analytics/start-job.png)

8. 작업을 시작하면 목록에 실행 중인 작업이 표시되고 다음 작업을 수행할 수 있습니다. 
   - **작업 시작/중지**: 작업이 실행 중인 경우 작업을 중지할 수 있습니다. 작업이 중지된 경우 작업을 시작할 수 있습니다. 
   - **작업 편집**: 쿼리를 편집할 수 있습니다. 작업 ex를 더 많이 변경하려면 입력/출력을 더 추가한 다음 Stream Analytics에서 작업을 엽니다. 작업이 실행 중일 때 편집 옵션이 비활성화됩니다. 
   - **출력 테이블 미리 보기**: SQL 쿼리 편집기에서 테이블을 미리 볼 수 있습니다. 
   - **스트림 분석에서 열기**: 스트림 분석 서비스에서 작업을 열어 모니터링을 보고 작업의 세부 정보를 디버깅합니다. 


    ![스트림 분석 작업](media/sql-database-stream-analytics/jobs.png)






## <a name="next-steps"></a>다음 단계

- [Azure Stream Analytics 설명서](https://docs.microsoft.com/azure/stream-analytics/)
- [Azure Stream Analytics 솔루션 패턴](../stream-analytics/stream-analytics-solution-patterns.md)
