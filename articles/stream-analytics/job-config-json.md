---
title: Azure Stream Analytics JobConfig.json 필드
description: 이 문서에서는 Visual Studio Code에서 작업을 생성하는 데 사용되는 Azure Stream Analytics JobConfig.json 파일에 지원되는 필드를 나열합니다.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020488"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.json 필드

다음 필드는 [Visual Studio Code를 사용하여 Azure Stream Analytics 작업을 생성](quick-create-visual-studio-code.md)하는 데 사용되는 *JobConfig.json* 파일에서 지원됩니다.

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|이름|유형|필수|값|
|----|----|--------|-----|
|DataLocale|문자열|No|Stream analytics 작업의 데이터 로캘입니다. 값은 지원되는 이름이어야 합니다. 지정하지 않을 경우 'en-US'으로 기본 설정됩니다.|
|OutputErrorPolicy|문자열|No|형식이 잘못되었기 때문에 출력에 도착하고 외부 스토리지에 쓸 수 없는 이벤트에 적용할 정책을 나타냅니다(누락된 열 값, 잘못된 형식 또는 크기의 열 값). -Stop 또는 Drop|
|EventsLateArrivalMaxDelayInSeconds|정수|예|늦게 도착하는 이벤트를 포함하는 최대 허용 지연 시간(초)입니다. 지원되는 범위는-1에서 1814399(20.23:59:59일)이고,-1은 무기한 대기를 지정하는 데 사용됩니다. 속성이 없으면 값이-1인 것으로 해석됩니다.|
|EventsOutOfOrderMaxDelayInSeconds|정수|예|순서가 잘못된 이벤트를 다시 순서대로 조정할 수 있는 최대 허용 지연 시간(초)입니다.|
|EventsOutOfOrderPolicy|문자열|No|입력 이벤트 스트림에 잘못된 순서로 도착하는 이벤트에 적용할 정책을 나타냅니다. - Adjust 또는 Drop|
|StreamingUnits|정수|예|스트리밍 작업에서 사용하는 스트리밍 단위 수를 지정합니다.|
|CompatibilityLevel|문자열|No|스트리밍 작업의 특정 런타임 동작을 제어합니다. - 허용되는 값은 "1.0", "1.1", "1.2"입니다|
|UseSystemAssignedIdentity|boolean|예|이 작업이 관리 Azure Active Directory ID를 사용하여 다른 Azure 서비스와 자체 통신할 수 있도록 하려면 true를 설정합니다.|
|GlobalStorage.AccountName|문자열|No|글로벌 스토리지 계정은 SQL 참조 데이터 스냅샷 등의 Stream Analytics 작업과 관련된 콘텐츠를 저장하는 데 사용됩니다.|
|GlobalStorage.AccountKey|문자열|No|글로벌 스토리지 계정에 대한 키입니다.|
|DataSourceCredentialDomain|문자열|No|자격 증명 로컬 스토리지에 대해 예약된 속성입니다.|
|ScriptType|문자열|예|이 원본 파일의 형식을 나타내는 예약된 속성입니다. 허용되는 값은 JobConfig.json에 대해 "JobConfig"입니다.|
|태그들|JSON 키-값 쌍|예|태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다. 태그 이름은 대/소문자를 구분하지 않지만, 태그 값은 대/소문자를 구분합니다.|

## <a name="next-steps"></a>다음 단계

* [Visual Studio Code에서 Azure Stream Analytics 작업 생성하기](quick-create-visual-studio-code.md)
* [Visual Studio Code를 통해 샘플 데이터를 사용하여 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run.md)
* [Visual Studio Code를 사용하여 라이브 스트림 입력에 대해 로컬로 Stream Analytics 쿼리 테스트](visual-studio-code-local-run-live-input.md)
*[CI/CD npm 패키지를 사용하여 Azure Stream Analytics 작업 배포](./cicd-overview.md)