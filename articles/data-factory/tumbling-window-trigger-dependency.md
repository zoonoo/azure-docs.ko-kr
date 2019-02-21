---
title: Azure Data Factory에서 연속 창 트리거 종속성 만들기 | Microsoft Docs
description: Azure Data Factory에서 연속 창 트리거에 대한 종속성을 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: c51c1056869cbd7741fae2ed1a554a7c794d1a39
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55966853"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>연속 창 트리거 종속성 만들기

이 항목에서는 연속 창 트리거에 대한 종속성을 만드는 단계를 제공합니다. 연속 창 트리거에 대한 자세한 내용은 [연속 창 트리거를 만드는 방법](how-to-create-tumbling-window-trigger.md)을 참조하세요.

종속성 체인을 빌드하고 데이터 팩터리에서 다른 트리거를 성공적으로 실행한 후에만 트리거가 실행되도록 하려면 이 고급 기능을 사용하여 연속 창 종속성을 만듭니다.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Data Factory UI에서 종속성 만들기

트리거에 대한 종속성을 만들려면 **트리거 > 고급 > 새로 만들기**를 선택한 다음, 적절한 오프셋 및 크기의 종속 대상 트리거를 선택합니다. **마침**을 선택하고 데이터 팩터리 변경 내용을 게시하여 종속성을 적용합니다.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>연속 창 종속성 속성

연속 창 트리거 종속성의 속성은 다음과 같습니다.

```json
{
    "name": "DemoTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

다음 표에서는 연속 창 종속성을 정의하는 데 필요한 특성 목록을 제공합니다.

| **속성 이름** | **설명**  | **형식** | **필수** |
|---|---|---|---|
| 트리거  | 이 드롭다운에는 기존의 연속 창 트리거가 모두 표시됩니다. 종속성을 적용할 트리거를 선택합니다.  | TumblingWindowTrigger | 예 |
| Offset | 종속성 트리거의 오프셋입니다. 시간 범위 형식으로 값을 제공합니다. 음수 및 양수 오프셋이 둘 다 허용됩니다. 이 매개 변수는 트리거가 자체 종속적인 경우에만 필수이고, 다른 모든 경우에는 선택 사항입니다. 자체 종속성은 항상 음수 오프셋이어야 합니다. | Timespan | 자체 종속성: 예, 기타: 아니요 |
| 창 크기 | 종속성 연속 창의 크기입니다. 시간 범위 형식으로 값을 입력합니다. 이 매개 변수는 선택 사항입니다. | Timespan | 아니요  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>연속 창 자체 종속성 속성

이전 창이 성공적으로 완료될 때까지 트리거가 다음 창으로 진행되지 않는 시나리오에서는 자체 종속성을 빌드합니다. 자체 종속성 트리거의 속성은 다음과 같습니다.

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```

아래에는 연속 창 종속성 속성의 사용 및 시나리오에 대한 일러스트레이션이 나와 있습니다.

## <a name="dependency-offset"></a>종속성 오프셋

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>종속성 크기

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>자체 종속성

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>사용 시나리오

### <a name="dependency-on-another-tumbling-window-trigger"></a>다른 연속 창 트리거에 대한 종속성

예를 들어 지난 7일간의 출력을 집계하고 7일 롤링 창 스트림을 생성하는 다른 일일 작업에 종속된 일일 원격 분석 처리 작업입니다.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>자체 종속성

작업의 출력 스트림에 간격이 없는 일일 작업입니다.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>종속성 모니터링

트리거 실행 모니터링 페이지에서 종속성 체인 및 해당 창을 모니터링할 수 있습니다. **모니터링 > 트리거 실행**으로 이동합니다.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

거울을 클릭하여 선택한 창의 종속 트리거 실행을 모두 표시합니다.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>다음 단계

[연속 창 트리거를 만드는 방법](how-to-create-tumbling-window-trigger.md)을 검토합니다.