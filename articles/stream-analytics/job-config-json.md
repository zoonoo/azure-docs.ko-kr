---
title: Azure 스트림 분석 JobConfig.json 필드
description: 이 문서에서는 Visual Studio 코드에서 작업을 만드는 데 사용되는 Azure 스트림 분석 JobConfig.json 파일에 대해 지원되는 필드를 나열합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 27ed553035ce9d7abf57ffe93078df9c17b8408c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617958"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure 스트림 분석 JobConfig.json 필드

다음 필드는 Visual Studio 코드를 사용하여 Azure 스트림 [분석 작업을 만드는](quick-create-vs-code.md)데 사용되는 *JobConfig.json* 파일에서 지원됩니다.

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

|이름|Type|필수|값|
|----|----|--------|-----|
|데이터 로컬|문자열|예|스트림 분석 작업의 데이터 로캘입니다. 값은 지원되는 이름의 값이어야 합니다. 지정되어 있지 않으면 기본값으로 'en-US'로 설정됩니다.|
|출력오류정책|문자열|예|출력에 도착하여 잘못된 형식(열 값 누락, 잘못된 형식 또는 크기의 열 값)으로 인해 외부 저장소에 쓸 수 없는 이벤트에 적용할 정책을 나타냅니다. - 중지 또는 드롭|
|이벤트레이트도착맥스딜레이지인초|integer|예|늦게 도착하는 이벤트가 포함될 수 있는 최대 허용 지연 시간(초)입니다. 지원되는 범위는 -1 ~ 1814399(20.23:59:59일)이며 -1은 대기를 무기한 지정하는 데 사용됩니다. 속성이 없는 경우 -1 값을 가진 것으로 해석됩니다.|
|이벤트아웃오브오더맥스딜레이지인초|integer|예|순서가 바도가 않은 이벤트를 순서대로 다시 조정할 수 있는 초 단위로 최대 허용 지연입니다.|
|이벤트아웃순서정책|문자열|예|입력 이벤트 스트림에서 순서가 바도 도착하는 이벤트에 적용할 정책을 나타냅니다. - 조정 또는 드롭|
|스트리밍 유닛|integer|yes|스트리밍 작업에서 사용하는 스트리밍 단위 수를 지정합니다.|
|CompatibilityLevel|문자열|예|스트리밍 작업의 특정 런타임 동작을 제어합니다. - 허용 값은 "1.0", "1.1", "1.2"입니다.|
|사용시스템할당된아이덴티티|boolean|예|이 작업이 관리되는 Azure Active Directory ID를 사용하여 다른 Azure 서비스와 자체적으로 통신할 수 있도록 true로 설정합니다.|
|글로벌 스토리지.계정 이름|문자열|예|전역 저장소 계정은 SQL 참조 데이터 스냅숏과 같은 스트림 분석 작업과 관련된 콘텐츠를 저장하는 데 사용됩니다.|
|글로벌 스토리지.계정 키|문자열|예|전역 저장소 계정에 대한 해당 키입니다.|
|데이터 소스 자격 증명 도메인|문자열|예|자격 증명 로컬 저장소에 대 한 예약 된 속성입니다.|
|스크립트 유형|문자열|yes|이 소스 파일의 형식을 표시하는 예약 된 속성입니다. 허용되는 값은 JobConfig.json의 "JobConfig"입니다.|
|태그들|JSON 키-값 쌍|예|태그는 여러 리소스 및 리소스 그룹에 동일한 태그를 적용하여 리소스를 분류하고 통합 청구를 볼 수 있는 이름/값 쌍입니다. 태그 이름은 대/소문자를 구분하지 않으며 태그 값은 대/소문자를 구분합니다.|

## <a name="next-steps"></a>다음 단계

* [시각적 스튜디오 코드에서 Azure 스트림 분석 작업 만들기](quick-create-vs-code.md)
* [Visual Studio 코드를 사용하여 샘플 데이터로 로컬에서 테스트 스트림 분석 쿼리](visual-studio-code-local-run.md)
* [Test Stream Analytics queries locally against live stream input by using Visual Studio Code](visual-studio-code-local-run-live-input.md)
*[CI/CD npm 패키지를](setup-cicd-vs-code.md) 사용하여 Azure 스트림 분석 작업을 배포하는 Visual Studio 코드를 사용하여 실시간 스트림 입력에 대해 로컬로 테스트 스트림 분석 쿼리