---
title: Azure Batch 작업 일정 실패 이벤트
description: Batch 태스크 일정 실패 이벤트에 대 한 참조입니다. 이 이벤트는 작업을 예약 하지 못했으며 나중에 다시 시도 하는 경우에 내보내집니다.
ms.topic: reference
ms.date: 09/20/2020
ms.openlocfilehash: 549281d2b2c371e8f09c584e771cf44f7abc8a00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91852193"
---
# <a name="task-schedule-fail-event"></a>작업 일정 실패 이벤트

 이 이벤트는 작업을 예약 하지 못했으며 나중에 다시 시도 하는 경우에 내보내집니다. 이는 리소스 제한으로 인 한 작업 예약 시간에 일시적인 오류입니다. 예를 들어, 지정 된 작업을 실행 하기 위해 노드에서 사용할 수 있는 슬롯이 충분 하지 않습니다 `requiredSlots` .

 다음 예에서는 태스크 일정 실패 이벤트의 본문을 보여 줍니다.

```
{
    "jobId": "job-01",
    "id": "task-01",
    "taskType": "User",
    "systemTaskVersion": 665378862,
    "requiredSlots": 1,
    "nodeInfo": {
        "poolId": "pool-01",
        "nodeId": " "
    },
    "multiInstanceSettings": {
        "numberOfInstances": 1
    },
    "constraints": {
        "maxTaskRetryCount": 0
    },
    "schedulingError": {
        "category": "UserError",
        "code": "JobPreparationTaskFailed",
        "message": "Task cannot run because the job preparation task failed on node"
    }
}
```

|요소 이름|Type|메모|
|------------------|----------|-----------|
|`jobId`|String|태스크가 포함된 작업의 ID입니다.|
|`id`|String|태스크의 ID입니다.|
|`taskType`|String|태스크의 유형입니다. 이는 작업 관리자 태스크를 나타내는 'JobManager' 또는 작업 관리자 태스크가 아님을 나타내는 'User'가 될 수 있습니다. 작업 준비 태스크, 작업 릴리스 태스크 또는 시작 태스크의 경우 이 이벤트가 내보내지지 않습니다.|
|`systemTaskVersion`|Int32|태스크에 대한 내부 재시도 카운터입니다. 내부적으로 Batch 서비스는 일시적인 문제를 해결하기 위해 태스크를 다시 시도할 수 있습니다. 이러한 문제에는 내부 일정 오류 또는 불량 상태의 컴퓨팅 노드 복구를 위한 시도가 포함될 수 있습니다.|
|`requiredSlots`|Int32|작업을 실행 하는 데 필요한 슬롯입니다.|
|[`nodeInfo`](#nodeInfo)|복합 형식|태스크가 실행된 컴퓨팅 노드에 대한 정보를 포함합니다.|
|[`multiInstanceSettings`](#multiInstanceSettings)|복합 형식|여러 컴퓨팅 노드가 필요한 다중 인스턴스 태스크임을 지정합니다.  자세한 내용은 [`multiInstanceSettings`](/rest/api/batchservice/get-information-about-a-task)를 참조하세요.|
|[`constraints`](#constraints)|복합 형식|이 태스크에 적용되는 실행 제약 조건입니다.|
|[`schedulingError`](#schedulingError)|복합 형식|태스크의 예약 오류에 대 한 정보를 포함 합니다.|

###  <a name="nodeinfo"></a><a name="nodeInfo"></a> nodeInfo

|요소 이름|Type|메모|
|------------------|----------|-----------|
|`poolId`|String|태스크가 실행된 풀의 ID입니다.|
|`nodeId`|String|태스크가 실행된 노드의 ID입니다.|

###  <a name="multiinstancesettings"></a><a name="multiInstanceSettings"></a> multiInstanceSettings

|요소 이름|Type|메모|
|------------------|----------|-----------|
|`numberOfInstances`|Int32|태스크에 필요한 컴퓨팅 노드 수입니다.|

###  <a name="constraints"></a><a name="constraints"></a> constraints

|요소 이름|Type|메모|
|------------------|----------|-----------|
|`maxTaskRetryCount`|Int32|태스크를 다시 시도할 수 있는 최대 횟수입니다. 종료 코드가 0이 아니면 Batch 서비스가 태스크를 다시 시도합니다.<br /><br /> 이 값은 구체적으로 재시도 횟수를 제어합니다. Batch 서비스는 태스크를 한 번 시도한 후 이 한도까지 다시 시도할 수 있습니다. 예를 들어 최대 재시도 횟수가 3일 경우 Batch는 최대 4회까지 태스크를 시도합니다(초기 시도 1회와 재시도 3회).<br /><br /> 최대 재시도 횟수가 0일 경우 Batch 서비스는 태스크를 다시 시도하지 않습니다.<br /><br /> 최대 재시도 횟수가 -1일 경우 Batch 서비스는 태스크를 무제한으로 다시 시도합니다.<br /><br /> 기본값은 0(재시도 안 함)입니다.|


###  <a name="schedulingerror"></a><a name="schedulingError"></a> schedulingError

|요소 이름|Type|메모|
|------------------|----------|-----------|
|`category`|String|오류의 범주입니다.|
|`code`|String|태스크 예약 오류에 대 한 식별자입니다. 코드는 고정 이며 프로그래밍 방식으로 사용 하기 위한 것입니다.|
|`message`|String|사용자 인터페이스에 표시 하기에 적합 한 태스크 예약 오류를 설명 하는 메시지입니다.|
