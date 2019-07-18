---
title: Azure Container Registry 작업 예약
description: 정의 된 일정에 따라 Azure Container Registry 태스크를 실행 하는 타이머를 설정 합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 06/27/2019
ms.author: danlep
ms.openlocfilehash: a1123a30025f9be6e994e69703f5ee1aa05d1b49
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509704"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>정의 된 일정에 ACR 태스크를 실행 합니다.

이 문서에서는 실행 하는 방법을 보여 줍니다.는 [ACR 작업](container-registry-tasks-overview.md) 일정에 따라 합니다. 하나 이상의 설정 하 여 작업을 예약 *타이머 트리거*합니다. 

작업을 예약할 경우 다음과 같은 시나리오에 유용 합니다.

* 예약 된 유지 관리 작업에 대 한 컨테이너 워크 로드를 실행 합니다. 예를 들어, 레지스트리에에서 불필요 한 이미지를 제거 하는 컨테이너 화 된 앱을 실행 합니다.
* 라이브 사이트 모니터링의 일부분으로 근무 시간 동안 프로덕션 이미지에는 일련의 테스트를 실행 합니다.

이 문서의 예제를 실행 하 여 Azure Cloud Shell 또는 Azure CLI의 로컬 설치를 사용할 수 있습니다. 사용 하 여 로컬로 버전 2.0.68 하려고 하거나 이상 버전이 필요 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.


## <a name="about-scheduling-a-task"></a>작업을 예약 하는 방법에 대 한

* **Cron 식 사용 하 여 트리거** -작업에 대 한 타이머 트리거를 사용 하는 *cron 식을*합니다. 식은 분, 시간, 일, 월 및 작업을 트리거하는 요일을 지정 필드 5 개가 포함 된 문자열입니다. 최대 1 분 마다 한 번의 주파수 지원 됩니다. 

  예를 들어 식 `"0 12 * * Mon-Fri"` 각 요일에 UTC 정오에 한 작업을 트리거합니다. 참조 [세부 정보](#cron-expressions) 이 문서의 뒷부분에 나오는.
* **타이머 트리거를 여러 개** -다른 일정으로 허용 됩니다 여러 타이머 작업에 추가 합니다. 
    * 작업을 만들거나 나중에 추가 하는 경우 여러 개의 타이머 트리거를 지정 합니다.
    * 필요에 따라 관리를 간소화 하기 위해 트리거 이름 또는 ACR 작업 트리거 이름은 기본 제공 됩니다.
    * 타이머 일정 한 번에 겹치는 ACR 작업을 각 타이머에 대 한 예약된 된 시간에 작업을 트리거합니다. 
* **다른 작업 트리거** -타이머 트리거 태스크에서 설정할 수 있습니다. 또한 기반으로 한 트리거 [소스 코드 커밋](container-registry-tutorial-build-task.md) 하거나 [기본 이미지 업데이트](container-registry-tutorial-base-image-update.md)합니다. 다른 ACR 작업과 마찬가지로 수도 있습니다 [수동으로 트리거][az-acr-task-run] 예약된 된 작업입니다.

## <a name="create-a-task-with-a-timer-trigger"></a>타이머 트리거를 사용 하 여 작업 만들기

태스크를 만들 때 합니다 [az acr 작업 만들기][az-acr-task-create] 명령을 타이머 트리거를 추가할 수도 있습니다. 추가 된 `--schedule` 매개 변수 및 타이머에 대 한 cron 식 전달 합니다. 

간단한 예로, 다음 명령을 실행 하는 트리거를 `hello-world` 매일 21:00 UTC에 Docker 허브에서 이미지입니다. 소스 코드 컨텍스트 없이 작업이 실행 됩니다.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --context /dev/null \
  --cmd hello-world \
  --schedule "0 21 * * *"
```

실행 합니다 [az acr 작업 표시][az-acr-task-show] 타이머 트리거 구성 되어 있는지 확인 합니다. 기본적으로 기본 이미지 업데이트 트리거를 사용할지도 합니다.

```console
$ az acr task show --name mytask --registry registry --output table
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

사용 하 여 수동으로 작업을 트리거합니다 [az acr 작업을 실행][az-acr-task-run] 제대로 설정 되어 있는지 확인 합니다.

```azurecli
az acr task run --name mytask --registry myregistry
```

컨테이너를 성공적으로 실행 하는 경우 출력은 다음과 비슷합니다.

```console
Queued a run with ID: cf2a
Waiting for an agent...
2019/06/28 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2019/06/28 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2019/06/28 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

예약된 된 시간 이후에 실행 합니다 [az acr 작업 목록-실행][az-acr-task-list-runs] 예상 대로 타이머 작업 트리거를 확인 하는 명령:

```azurecli
az acr task list runs --name mytask --registry myregistry --output table
``` 

타이머에 성공할 경우 출력은 다음과 비슷합니다.

```console
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```
            
## <a name="manage-timer-triggers"></a>타이머 트리거를 관리 합니다.

사용 된 [az acr 작업 타이머][az-acr-task-timer] 는 ACR 작업에 대 한 타이머 트리거를 관리 하는 명령입니다.

### <a name="add-or-update-a-timer-trigger"></a>추가 하거나 타이머 트리거를 업데이트 합니다.

작업을 만든 후 필요에 따라 타이머 트리거를 사용 하 여 추가 합니다 [az acr 작업 타이머 추가][az-acr-task-timer-add] 명령입니다. 다음 예제에서는 타이머 트리거 이름을 추가 *타이머 2* 하 *mytask* 이전에 생성 합니다. 이 타이머 작업을 10:30 일 마다 트리거합니다 UTC입니다.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

기존 트리거의 일정을 업데이트 하거나 사용 하 여 해당 상태를 변경 합니다 [az acr 작업 타이머 업데이트][az-acr-task-timer-update] 명령입니다. 예를 들어, 명명 된 트리거를 업데이트할 *타이머 2* 11:30 작업 트리거를 UTC:

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>타이머 트리거 나열

합니다 [az acr 작업 타이머 목록][az-acr-task-timer-list] 명령은 작업에 대해 설정 합니다. 타이머 트리거를 보여 줍니다.

```azurecli
az acr task timer list --name mytask --registry myregistry
```

예제 출력:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>타이머 트리거를 제거 합니다. 

사용 된 [az acr 작업 타이머 제거][az-acr-task-timer-remove] 작업에서 타이머 트리거를 제거 하려면 명령을 합니다. 다음 예제를 제거 합니다 *타이머 2* 에서 트리거 *mytask*:

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 식

ACR 작업을 사용 합니다 [NCronTab](https://github.com/atifaziz/NCrontab) cron 식을 해석 하는 라이브러리입니다. ACR 작업에서 지원 되는 식에 공백으로 구분 하는 다섯 개의 필수 필드

`{minute} {hour} {day} {month} {day-of-week}`

Cron 식을 사용 하 여 사용할 표준 시간대는 utc (협정 세계시). 시간은 24 시간 형식입니다.

> [!NOTE]
> ACR 작업을 지원 하지 않습니다 합니다 `{second}` 또는 `{year}` cron 식의 필드입니다. 다른 시스템에서 사용 하는 cron 식을 복사 하는 경우 제거 해야 해당 필드를 사용 하는 경우입니다.

각 필드에는 다음과 같은 형식의 값 중 하나가 포함될 수 있습니다.

|Type  |예  |트리거될 때  |
|---------|---------|---------|
|특정 값 |<nobr>"5 * * * *"</nobr>|5 분에서 1 시간 마다|
|모든 값(`*`)|<nobr>"* 5 * * *"</nobr>|시간 시작 부분 5시 UTC (하루 60 번)의 1 분 마다|
|범위(`-` 연산자)|<nobr>"0 1-3 * * *"</nobr>|3 번 하루 1:00:00, 2 및 3시 UTC|  
|값 집합(`,` 연산자)|<nobr>"20,30,40 * * * *"</nobr>|3 시간 마다 20 분, 30 분, 시간 40 분에|
|간격 값(`/` 연산자)|<nobr>"*/10 * * * *"</nobr>|10 분, 20 분 및 시간 등에 시간당 6 번

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 예제

|예|트리거될 때  |
|---------|---------|
|`"*/5 * * * *"`|5분마다 한 번|
|`"0 * * * *"`|1시간이 시작할 때마다 한 번|
|`"0 */2 * * *"`|2시간마다 한 번|
|`"0 9-17 * * *"`|한 번에서 1 시간 마다 9:00 17시 UTC|
|`"30 9 * * *"`|9:30 일 마다 UTC|
|`"30 9 * * 1-5"`|9:30 주중 매일 UTC|
|`"30 9 * Jan Mon"`|9:30 년 1 월의 매주 월요일 UTC|


## <a name="next-steps"></a>다음 단계

작업의 예제 트리거한 소스 코드 커밋 또는 기본 이미지 업데이트를 확인 합니다 [ACR 작업 자습서 시리즈](container-registry-tutorial-quick-task.md)합니다.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az-acr-task-timer-add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az-acr-task-timer-remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az-acr-task-timer-list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az-acr-task-timer-update
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli