---
title: Azure Stream Analytics 통합을 사용하여 데이터 스트림(미리 보기)
description: Azure Stream Analytics 통합을 사용하여 Azure SQL Database에 데이터를 스트림합니다.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: ajetasin
ms.author: ajetasi
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 36a96a1927aeedb5f841083241d487e0c61d6813
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96454024"
---
# <a name="stream-data-into-azure-sql-database-using-azure-stream-analytics-integration-preview"></a>Azure Stream Analytics 통합을 사용하여 Azure SQL Database에 데이터 스트림(미리 보기)

이제 사용자는 Azure SQL Database의 데이터베이스에서 직접 실시간 스트리밍 데이터를 테이블로 수집, 처리, 표시, 분석할 수 있습니다. Azure Portal에서 [Azure Stream Analytics](../../stream-analytics/stream-analytics-introduction.md)를 사용하여 이 작업을 수행합니다. 이 환경에서는 연결된 자동차, 원격 모니터링, 부정 행위 탐지 등과 같은 다양한 시나리오가 가능합니다. Azure Portal에서 이벤트 원본(이벤트 허브/IoT Hub)을 선택하고, 들어오는 실시간 이벤트를 보고, 이벤트를 저장할 테이블을 선택할 수 있습니다. 포털에서 Azure Stream Analytics 쿼리 언어 쿼리를 작성하여 들어오는 이벤트를 변환하고 선택한 테이블에 저장할 수도 있습니다. 이 새로운 진입점은 Stream Analytics에 이미 존재하는 만들기 및 구성 환경에 추가됩니다. 이 환경은 데이터베이스의 컨텍스트에서 시작되므로 Stream Analytics 작업을 빠르게 설정하고 Azure SQL Database의 데이터베이스와 Stream Analytics 환경 간에 매끄럽게 이동할 수 있습니다.

![Stream Analytics 흐름](./media/stream-data-stream-analytics-integration/stream-analytics-flow.png)

## <a name="key-benefits"></a>주요 이점

- 최소 컨텍스트 전환: 포털에서는 Azure SQL Database의 데이터베이스에서 시작하고 다른 서비스로 전환하지 않고 실시간 데이터를 테이블에 수집하기 시작할 수 있습니다.
- 단계 수 감소: 데이터베이스 및 테이블의 컨텍스트는 Stream Analytics 작업을 미리 구성하는 데 사용됩니다.
- 미리 보기 데이터의 추가적인 사용 편의성: 선택한 테이블의 컨텍스트에서 이벤트 원본(이벤트 허브/IoT Hub)에서 들어오는 데이터를 미리 봅니다.

> [!IMPORTANT]
> Azure Stream Analytics 작업은 Azure SQL Database, Azure SQL Managed Instance 또는 Azure Synapse Analytics로 출력될 수 있습니다. 자세한 내용은 [출력](../../stream-analytics/stream-analytics-define-outputs.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 리소스가 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 [무료 계정을 만듭니다](https://azure.microsoft.com/free/).
- Azure SQL Database의 데이터베이스입니다. 자세한 내용은 [Azure SQL Database에서 단일 데이터베이스 만들기](single-database-create-quickstart.md)를 참조하세요.
- 컴퓨터가 서버에 연결하도록 허용하는 방화벽 규칙. 자세한 내용은 [서버 수준 방화벽 규칙 만들기](firewall-create-server-level-portal-quickstart.md)를 참조하세요.

## <a name="configure-stream-analytics-integration"></a>Stream Analytics 통합 구성

1. Azure Portal에 로그인합니다.
2. 스트리밍 데이터를 수집하려는 데이터베이스로 이동합니다. **Stream Analytics(미리 보기)** 를 선택합니다.

    ![Stream Analytics](./media/stream-data-stream-analytics-integration/stream-analytics.png)

3. 스트리밍 데이터를 이 데이터베이스로 수집하기 시작하려면 **만들기** 를 선택하고, 스트리밍 작업에 이름을 지정한 후, **다음: 입력** 을 선택합니다.

    ![Stream Analytics 작업 기본 사항 구성](./media/stream-data-stream-analytics-integration/create-job.png)

4. 이벤트 원본 세부 정보를 입력한 후 **다음: 출력** 을 선택합니다.

   - **입력 형식**: 이벤트 허브/IoT Hub
   - **입력 별칭**: 이벤트 원본을 식별하는 이름 입력
   - **구독**: Azure SQL Database 구독과 동일
   - **이벤트 허브 네임스페이스**: 네임스페이스 이름
   - **이벤트 허브 이름**: 선택한 네임스페이스 내의 이벤트 허브 이름
   - **이벤트 허브 정책 이름**(기본값: 새로 만들기): 정책 이름 지정
   - **이벤트 허브 소비자 그룹**(기본값: 새로 만들기): 소비자 그룹 이름 지정  

      여기에서 만드는 새 Azure Stream Analytics 작업마다 소비자 그룹과 정책을 만드는 것이 좋습니다. 소비자 그룹은 5명의 동시 읽기 권한자만 허용하므로 각 작업에 대한 전용 소비자 그룹을 제공하면 해당 한도 초과로 인해 발생할 수 있는 오류가 방지됩니다. 전용 정책을 사용하면 다른 리소스에 영향을 주지 않고 키를 회전하거나 권한을 취소할 수 있습니다.

     ![Stream Analytics 작업 출력 구성](./media/stream-data-stream-analytics-integration/create-job-output.png)

5. 스트리밍 데이터를 수집하려는 테이블을 선택합니다. 완료되면 **만들기** 를 선택합니다.

   - **사용자 이름**, **암호**: SQL 서버 인증을 위한 자격 증명을 입력합니다. **유효성 검사** 를 선택합니다.
   - **테이블**: **새로 만들기** 또는 **기존 항목 사용** 을 선택합니다. 이 흐름에서는 **만들기** 를 선택하겠습니다. 그러면 Stream Analytics 작업을 시작할 때 새 테이블이 생성됩니다.

     ![Stream Analytics 작업 만들기](./media/stream-data-stream-analytics-integration/create.png)

6. 다음 세부 정보가 포함된 쿼리 페이지가 열립니다.

   - 데이터를 수집할 **입력**(입력 이벤트 원본)  
   - 변환된 데이터를 저장할 **출력**(출력 테이블)
   - SELECT 문을 사용하는 샘플 [SAQL 쿼리](../../stream-analytics/stream-analytics-stream-analytics-query-patterns.md)
   - **입력 미리 보기**: 입력 이벤트 원본에서 들어오는 최신 데이터의 스냅샷을 표시합니다.
     - 데이터의 serialization 형식이 자동으로 검색됩니다(JSON/CSV). JSON/CSV/AVRO에도 수동으로 변경할 수 있습니다.
     - 들어오는 데이터를 테이블 형식이나 원시 형식으로 미리 볼 수 있습니다.
     - 표시된 데이터가 최신 상태가 아닌 경우 **새로 고침** 을 선택하여 최신 이벤트를 확인합니다.
     - **시간 범위 선택** 을 선택하여 들어오는 이벤트의 특정 시간 범위에 대해 쿼리를 테스트합니다.
     - **샘플 입력 업로드** 를 선택하여 샘플 JSON/CSV 파일을 업로드하는 방식으로 쿼리를 테스트합니다. SAQL 쿼리를 테스트하는 방법에 관한 자세한 내용은 [샘플 데이터를 사용하여 Azure Stream Analytics 작업 테스트](../../stream-analytics/stream-analytics-test-query.md)를 참조하세요.

     ![쿼리 테스트](./media/stream-data-stream-analytics-integration/test-query.png)

   - **테스트 결과**: **쿼리 테스트** 를 선택하면 스트리밍 쿼리 결과를 확인할 수 있습니다.

     ![테스트 결과](./media/stream-data-stream-analytics-integration/test-results.png)

   - **테스트 결과 스키마**: 테스트한 후 스트리밍 쿼리 결과의 스키마를 표시합니다. 테스트 결과 스키마가 출력 스키마와 일치하는지 확인합니다.

     ![테스트 결과 스키마](./media/stream-data-stream-analytics-integration/test-results-schema.png)

   - **출력 스키마**: 5단계에서 선택한 테이블의 스키마(신규 또는 기존)를 포함합니다.

      - 새로 만들기: 5단계에서 이 옵션을 선택한 경우 스트리밍 작업을 시작할 때까지 아직 스키마가 표시되지 않습니다. 새 테이블을 만들 때 적절한 테이블 인덱스를 선택합니다. 테이블 인덱싱에 관한 자세한 내용은 [클러스터형 및 비클러스터형 인덱스 소개](/sql/relational-databases/indexes/clustered-and-nonclustered-indexes-described/)를 참조하세요.
      - 기존 항목 사용: 5단계에서 이 옵션을 선택한 경우 선택한 테이블의 스키마가 표시됩니다.

7. 쿼리 작성 및 테스트를 완료한 후 **쿼리 저장** 을 선택합니다. **Stream Analytics 작업 시작** 을 선택하여 변환된 데이터를 SQL 테이블에 수집하기 시작합니다. 다음 필드를 완료한 후 작업을 **시작** 합니다.
   - **출력 시작 시간**: 작업의 첫 번째 출력 시간을 정의합니다.  
     - 지금: 작업이 지금 시작되고 들어오는 새 데이터를 처리합니다.
     - 사용자 지정: 작업이 지금 시작되지만 특정 시점의 데이터를 처리합니다(이전 또는 이후에 있을 수 있음). 자세한 내용은 [Azure Stream Analytics 작업을 시작하는 방법](../../stream-analytics/start-job.md)을 참조하세요.
   - **스트리밍 단위**: Azure Stream Analytics는 데이터를 서비스로 처리하는 데 필요한 스트리밍 단위의 개수로 가격이 책정됩니다. 자세한 내용은 [Azure Stream Analytics 가격 책정](https://azure.microsoft.com/pricing/details/stream-analytics/)을 참조하세요.
   - **출력 데이터 오류 처리**:  
     - 다시 시도: 오류가 발생하면 Azure Stream Analytics는 쓰기가 성공할 때까지 무기한으로 이벤트 작성을 다시 시도합니다. 재시도에 대한 시간 제한은 없습니다. 결국, 재시도 중인 이벤트에 의해 모든 후속 이벤트의 처리가 차단됩니다. 이 옵션은 기본 출력 오류 처리 정책입니다.
     - 삭제: Azure Stream Analytics는 데이터 변환 오류를 초래하는 모든 출력 이벤트를 삭제합니다. 삭제된 이벤트는 나중에 다시 처리하기 위해 복구할 수 없습니다. 모든 일시적인 오류(예: 네트워크 오류)는 출력 오류 처리 정책 구성과 상관없이 다시 시도됩니다.
   - **SQL Database 출력 설정**: 이전 쿼리 단계의 파티션 구성표를 상속하여 여러 기록기를 사용하는 완전 병렬 토폴로지를 테이블에서 사용하도록 설정하는 옵션입니다. 자세한 내용은 [Azure SQL Database에 Azure Stream Analytics 출력](../../stream-analytics/stream-analytics-sql-output-perf.md)을 참조하세요.
   - **최대 일괄 처리 수**: 모든 대량 삽입 트랜잭션을 통해 보내는 레코드 수에 권장되는 상한입니다.  
    출력 오류 처리에 관한 자세한 내용은 [Azure Stream Analytics의 출력 오류 정책](../../stream-analytics/stream-analytics-output-error-policy.md)을 참조하세요.  

     ![작업 시작](./media/stream-data-stream-analytics-integration/start-job.png)

8. 작업을 시작하면 실행 중인 작업이 목록에 표시되고 다음 작업을 수행할 수 있습니다.
   - **작업 시작/중지**: 작업이 실행 중인 경우 작업을 중지할 수 있습니다. 작업이 중지된 경우 작업을 시작할 수 있습니다.
   - **작업 편집**: 쿼리를 편집할 수 있습니다. 예를 들어, 작업 실행을 추가로 변경하려면 더 많은 입/출력을 추가한 다음, Stream Analytics에서 작업을 엽니다. 작업이 실행되는 동안에는 편집 옵션을 사용할 수 없습니다.
   - **출력 테이블 미리 보기**: SQL 쿼리 편집기에서 테이블을 미리 볼 수 있습니다.
   - **Stream Analytics에서 열기**: Stream Analytics에서 작업을 열어 작업의 모니터링, 디버깅 세부 정보를 봅니다.

     ![Stream Analytics 작업](./media/stream-data-stream-analytics-integration/jobs.png)

## <a name="next-steps"></a>다음 단계

- [Azure Stream Analytics 설명서](../../stream-analytics/index.yml)
- [Azure Stream Analytics 솔루션 패턴](../../stream-analytics/stream-analytics-solution-patterns.md)
