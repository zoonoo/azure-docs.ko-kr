---
title: Azure Stream Analytics Blob 출력에 대한 날짜/시간 경로 패턴(미리 보기)
description: 이 문서에서는 Azure Stream Analytics 작업에서 Blob 스토리지 출력에 대한 사용자 지정 날짜/시간 경로 패턴 기능을 설명합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ba386539c3f3c6740b843575bbccd4b028b8a5a7
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090792"
---
# <a name="custom-datetime-path-patterns-for-azure-stream-analytics-blob-storage-output-preview"></a>Azure Stream Analytics Blob Storage 출력에 대한 사용자 지정 날짜/시간 경로 패턴(미리 보기)

Azure Stream Analytics는 Blob Storage 출력에 대한 파일 경로에서 사용자 지정 날짜 및 시간 서식 지정자를 지원합니다. 사용자 지정 날짜/시간 경로 패턴을 사용하면 Hive 스트리밍 규칙을 사용하여 정렬하는 출력 형식을 지정하여 Azure Stream Analytics에서 다운스트림 처리를 위해 Azure Databricks 및 Azure HDInsight에 데이터를 보낼 수 있는 기능을 부여할 수 있습니다. 사용자 지정 날짜/시간 경로 패턴은 서식 지정자와 함께 Blob 출력의 경로 접두사 필드에서 `datetime` 키워드를 사용하여 쉽게 구현됩니다. 예: `{datetime:yyyy}`

[Azure Portal](https://portal.azure.com/?Microsoft_Azure_StreamAnalytics_bloboutputcustomdatetimeformats=true)에 대한 이 링크를 사용하여 Blob Storage 출력 미리 보기에 대해 사용자 지정 날짜/시간 경로 패턴을 사용하도록 설정하는 기능 플래그를 설정/해제합니다. 이 기능은 곧 기본 포털에서 사용됩니다.

## <a name="supported-tokens"></a>지원되는 토큰

다음 서식 지정자 토큰은 사용자 지정 날짜/시간 형식을 설정하기 위해 단독 또는 함께 사용할 수 있습니다.

|서식 지정자   |설명   |예제 시간 2018-01-02T10:06:08에 대한 결과|
|----------|-----------|------------|
|{datetime:yyyy}|연도(4자리 숫자)|2018|
|{datetime:MM}|월(01-12)|01|
|{datetime:M}|월(1-12)|1|
|{datetime:dd}|일(01-31)|02|
|{datetime:d}|일(1-12)|2|
|{datetime:HH}|시간(00-23의 24시간 형식)|10|
|{datetime:mm}|분(00-24)|06|
|{datetime:m}|분(0-24)|6|
|{datetime:ss}|초(00-60)|08|

사용자 지정 날짜/시간 패턴을 사용하려는 경우 {date} 및/또는 {time} 토큰을 경로 접두사에 추가하여 기본 제공 날짜/시간 서식을 사용하여 드롭다운을 생성할 수 있습니다.

![Stream Analytics 이전 날짜/시간 서식](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-old-date-time-formats.png)

## <a name="extensibility-and-restrictions"></a>확장성 및 제한 사항

경로 접두사 문자 제한에 도달할 때까지 경로 패턴에서 원하는 만큼 `{datetime:<specifier>}` 토큰을 사용할 수 있습니다. 서식 지정자는 날짜 및 시간 드롭다운 목록에서 이미 나열된 조합 이외의 단일 토큰 내에서 결합할 수 없습니다. 

`logs/MM/dd` 경로 파티션의 경우:

|유효한 식   |잘못된 식   |
|----------|-----------|
|`logs/{datetime:MM}/{datetime:dd}`|`logs/{datetime:MM/dd}`|

경로 접두사에서 동일한 서식 지정자를 여러 번 사용할 수 있습니다. 토큰은 매번 반복되어야 합니다.

## <a name="hive-streaming-conventions"></a>Hive 스트리밍 규칙

Blob Storage에 대한 사용자 지정 경로 패턴은 Hive 스트리밍 규칙에서 사용될 수 있습니다. 폴더 이름에서 `column=`으로 레이블이 지정된 폴더가 생성됩니다.

예: `year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}/hour={datetime:HH}`

사용자 지정 출력은 테이블을 변경하고 파티션을 수동으로 추가하는 번거로움을 없애서 Azure Stream Analytics와 Hive 간에 데이터를 전송합니다. 대신, 다음을 사용하여 자동으로 여러 폴더를 추가할 수 있습니다.

```SQL
MSCK REPAIR TABLE while hive.exec.dynamic.partition true
```

### <a name="example"></a>예

[Azure Stream Analytics Azure Portal](stream-analytics-quick-create-portal.md) 빠른 시작 가이드에 따라 저장소 계정, 리소스 그룹, Stream Analytics 작업 및 입력 원본을 만듭니다. 빠른 시작 가이드에서 사용된 동일한 샘플 데이터를 사용하며 [GitHub](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json)에서도 사용 가능합니다.

다음 구성을 사용하여 Blob 출력 싱크를 만듭니다.

![Stream Analytics 생성 Blob 출력 싱크](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-create-output-sink.png)

전체 경로 패턴은 다음과 같습니다.


`year={datetime:yyyy}/month={datetime:MM}/day={datetime:dd}`


작업을 시작하는 경우 경로 패턴에 따라 폴더 구조는 Blob 컨테이너에서 생성됩니다. 일 수준으로 드릴다운할 수 있습니다.

![사용자 지정 경로 패턴을 사용한 Stream Analytics Blob 출력](./media/stream-analytics-custom-path-patterns-blob-storage-output/stream-analytics-blob-output-folder-structure.png)

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics의 출력 이해](stream-analytics-define-outputs.md)
