---
title: "Azure Stream Analytics 진단 로그 | Microsoft Docs"
description: "Microsoft Azure에서 Stream Analytics 작업의 진단 로그를 분석하는 방법을 알아봅니다."
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 0dac2cc79de884def8d4cf0ee89dc2f645d35b34
ms.lasthandoff: 03/29/2017


---
# <a name="job-diagnostic-logs"></a>작업 진단 로그

## <a name="introduction"></a>소개
Stream Analytics에서는 두 가지 유형의 로그를 노출합니다. 
* [활동 로그:](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) 항상 사용되며 작업(job)에 대해 수행한 작업(operation)에 대한 정보를 제공합니다.
* [진단 로그:](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 구성 가능하며 작업이 실행되어 삭제될 때까지 생성, 업데이트된 작업의 작업 시작과 함께 발생한 모든 항목에 대한 풍부한 정보를 제공합니다.

> [!NOTE]
> Azure Storage, 이벤트 허브 및 Log Analytics와 같은 서비스를 비규격 데이터 분석에 사용하면 해당 서비스의 가격 책정 모델에 따라 요금이 부과됩니다.

## <a name="how-to-enable-diagnostic-logs"></a>진단 로그를 사용하도록 설정하는 방법
진단 로그는 기본적으로 **해제**됩니다. 사용하도록 설정하려면 다음 단계를 따르세요.

Azure Portal에 로그온하고 스트리밍 작업 블레이드로 이동합니다. 그런 후 “모니터링” 아래의 “진단 로그” 블레이드로 이동합니다.

![진단 로그에 대한 블레이드 탐색](./media/stream-analytics-job-diagnostic-logs/image1.png)  

그런 다음 "진단 사용" 링크를 클릭합니다.

![진단 로그 사용](./media/stream-analytics-job-diagnostic-logs/image2.png)

열린 진단에서 상태를 "켜기"로 변경합니다.

![진단 로그 상태 변경](./media/stream-analytics-job-diagnostic-logs/image3.png)

원하는 보관 대상(저장소 계정, 이벤트 허브, Log Analytics)을 구성하고 수집할 로그의 범주(실행, 작성)를 선택합니다. 그런 다음 새 진단 구성을 저장합니다.

저장된 구성이 적용되는 데 약 10분이 소요됩니다. 그 후 구성된 보관 대상에 로그가 나타나기 시작합니다("진단 로그" 블레이드에서 볼 수 있음).

![진단 로그에 대한 블레이드 탐색](./media/stream-analytics-job-diagnostic-logs/image4.png)

진단 구성에 대한 자세한 내용은 [진단 로그](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) 페이지에서 확인할 수 있습니다.

## <a name="diagnostic-logs-categories"></a>진단 로그 범주
현재 두 가지 범주의 진단 로그를 캡처할 수 있습니다.

* **작성:** 생성, 입력 및 출력 추가 및 삭제, 쿼리 추가 및 업데이트, 작업 시작 및 중지 등 작업 작성 작업(operation)과 관련된 로그를 캡처합니다.
* **실행:** 작업 실행 중에 발생한 항목을 캡처합니다.
    * 연결 오류
    * 데이터 처리 오류
        * 쿼리 정의를 따르지 않는 이벤트(필드 형식 및 값 불일치, 필드 누락 등)
        * 식 평가 오류
    * 등

## <a name="diagnostic-logs-schema"></a>진단 로그 스키마
모든 로그는 JSON 형식으로 저장되며 각 항목에는 다음과 같은 일반적인 문자열 필드가 있습니다.

이름 | 설명
------- | -------
실시간 | 로그의 타임스탬프(UTC)
resourceId | 작업이 수행되는 리소스의 ID(대문자). 여기에는 구독 ID, 리소스 그룹 및 작업 이름이 포함됩니다. 예: **/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB**.
카테고리 | 로그 범주: **실행** 또는 **제작**
operationName | 기록된 작업의 이름 예: **이벤트 전송: mysqloutput에 대한 SQL 출력 쓰기 실패**
status | 작업의 상태. 예: **실패, 성공**.
최소 수준 | 로그 수준. 예: **오류, 경고, 정보**.
properties | 로그 항목별 세부 정보, JSON 문자열로 직렬화됨, 자세한 내용은 아래 참조

### <a name="execution-logs-properties-schema"></a>실행 로그 속성 스키마
실행 로그에는 Stream Analytics 작업 실행 중에 발생한 이벤트에 대한 정보가 포함됩니다.
이벤트 유형에 따라 속성의 스키마가 다릅니다. 현재는 다음과 같은 유형이 있습니다.

### <a name="data-errors"></a>데이터 오류
데이터 처리에 대한 오류는 이 로그 범주에 속합니다. 주로 데이터 읽기, 직렬화 및 쓰기 작업 중에 발생합니다. 일반 이벤트로 처리되는 연결 오류는 포함되지 않습니다.

이름 | 설명
------- | -------
원본 | 오류가 발생한 작업 입력 또는 출력 이름
Message | 오류와 연결된 메시지
형식 | 오류 유형. 예: **DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError**.
Data | 오류 출처를 정확히 찾는 데 도움이 되는 데이터를 포함합니다. 크기에 따라 잘릴 수 있습니다.

**operationName** 값에 따라 데이터 오류의 스키마는 다음과 같습니다.
* **이벤트 직렬화** - 이벤트 읽기 작업 중에 입력 데이터가 쿼리 스키마에 맞지 않는 경우 발생합니다.
    * 이벤트 (역)직렬화 중에 형식 불일치: 필드로 인해 오류 발생
    * 이벤트를 읽을 수 없음, 잘못된 직렬화: 입력 데이터에서 오류가 발생한 위치에 대한 정보: Blob 입력을 위한 Blob 이름, 데이터의 오프셋 및 샘플
* **이벤트 보내기** - 쓰기 작업: 오류를 발생시킨 스트리밍 이벤트

### <a name="generic-events"></a>일반 이벤트
기타 등등

이름 | 설명
-------- | --------
오류 | (선택 사항) 오류 정보, 일반적으로 예외 정보(사용 가능한 경우)
Message| 로그 메시지
형식 | 메시지 유형, 내부 오류 범주로 매핑: 예를 들어 **JobValidationError, BlobOutputAdapterInitializationFailure** 등
상관관계 ID | 작업 실행을 고유하게 식별하는 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier). 작업이 시작된 후 중단될 때까지 생성된 모든 실행 로그 항목은 동일한 "상관 관계 ID"를 포함합니다.



## <a name="next-steps"></a>다음 단계
* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-get-started.md)
* [Azure 스트림 분석 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure 스트림 분석 쿼리 언어 참조](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)


