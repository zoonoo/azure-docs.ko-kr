---
title: 자습서 - ACR 작업 예약
description: 이 자습서에서는 하나 이상의 타이머 트리거를 설정하여 정의된 일정에 따라 Azure 컨테이너 레지스트리 작업을 실행하는 방법을 알아봅니다.
ms.topic: article
ms.date: 06/27/2019
ms.openlocfilehash: 3202b5d8c426165d81129f1affa69b3a3d515ce9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402871"
---
# <a name="run-an-acr-task-on-a-defined-schedule"></a>정의된 일정에 따라 ACR 작업 실행

이 자습서에서는 일정에 따라 ACR 작업을 실행하는 방법을 보여 주며, 이 자습서에서는 ACR 작업을 실행하는 방법을 보여 주며, 이 자습서에서는 [ACR 작업을](container-registry-tasks-overview.md) 실행하는 방법을 보여 주시면 됩니다. 하나 이상의 타이머 트리거를 설정하여 작업을 *예약합니다.* 타이머 트리거는 단독으로 사용하거나 다른 작업 트리거와 함께 사용할 수 있습니다.

이 자습서에서는 작업 예약 에 대해 알아봅니다.

> [!div class="checklist"]
> * 타이머 트리거로 작업 만들기
> * 타이머 트리거 관리

작업 예약은 다음과 같은 시나리오에 유용합니다.

* 예약된 유지 관리 작업에 대한 컨테이너 워크로드를 실행합니다. 예를 들어 컨테이너화된 앱을 실행하여 레지스트리에서 불필요한 이미지를 제거합니다.
* 라이브 사이트 모니터링의 일환으로 근무 일 동안 프로덕션 이미지에서 테스트 집합을 실행합니다.

Azure 클라우드 셸 또는 Azure CLI의 로컬 설치를 사용하여 이 문서의 예제를 실행할 수 있습니다. 로컬에서 사용하려면 버전 2.0.68 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하십시오.


## <a name="about-scheduling-a-task"></a>작업 예약 에 대해

* **cron 식 트리거** - 작업에 대한 타이머 트리거는 *cron 식을*사용합니다. 식은 작업을 트리거할 분, 시간, 일, 월 및 요일을 지정하는 5개의 필드가 있는 문자열입니다. 분당 최대 한 번의 주파수가 지원됩니다.

  예를 들어 식은 `"0 12 * * Mon-Fri"` 평일 정오 UTC에 작업을 트리거합니다. 이 문서의 [후반부에서 자세히 알아보십시오.](#cron-expressions)
* **여러 타이머 트리거** - 일정이 다른 한 작업에 여러 타이머를 추가하는 것이 허용됩니다.
    * 작업을 만들 때 여러 타이머 트리거를 지정하거나 나중에 추가합니다.
    * 선택적으로 더 쉽게 관리할 수 있도록 트리거이름을 지정하거나 ACR 태스크에서 기본 트리거 이름을 제공합니다.
    * 타이머 스케줄이 한 번에 겹치는 경우 ACR 작업은 각 타이머에 대해 예약된 시간에 작업을 트리거합니다.
* **다른 작업 트리거** - 타이머 트리거 작업에서 [소스 코드 커밋](container-registry-tutorial-build-task.md) 또는 기본 [이미지 업데이트를](container-registry-tutorial-base-image-update.md)기반으로 트리거를 활성화할 수도 있습니다. 다른 ACR 작업과 마찬가지로 예약된 작업을 [수동으로 트리거할][az-acr-task-run] 수도 있습니다.

## <a name="create-a-task-with-a-timer-trigger"></a>타이머 트리거로 작업 만들기

[az acr 태스크 만들기][az-acr-task-create] 명령을 사용하여 작업을 만들 때 선택적으로 타이머 트리거를 추가할 수 있습니다. 매개 `--schedule` 변수를 추가하고 타이머에 대한 cron 식을 전달합니다.

간단한 예로 다음 명령은 매일 21:00 UTC에서 Docker Hub에서 `hello-world` 이미지를 실행하는 트리거를 트리거합니다. 작업은 소스 코드 컨텍스트 없이 실행됩니다.

```azurecli
az acr task create \
  --name mytask \
  --registry myregistry \
  --cmd hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

az [acr 작업 표시][az-acr-task-show] 명령을 실행하여 타이머 트리거가 구성되는지 확인합니다. 기본적으로 기본 이미지 업데이트 트리거도 활성화됩니다.

```azurecli
az acr task show --name mytask --registry registry --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
mytask    linux       Enabled                           BASE_IMAGE, TIMER
```

[az acr 작업 실행을][az-acr-task-run] 사용하여 작업을 수동으로 트리거하여 제대로 설정되었는지 확인합니다.

```azurecli
az acr task run --name mytask --registry myregistry
```

컨테이너가 성공적으로 실행되면 출력은 다음과 유사합니다.

```output
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

예약된 시간 이후에 [az acr 작업 목록 실행][az-acr-task-list-runs] 명령을 실행하여 타이머가 예상대로 작업을 트리거했는지 확인합니다.

```azurecli
az acr task list-runs --name mytask --registry myregistry --output table
```

타이머가 성공하면 출력은 다음과 유사합니다.

```output
RUN ID    TASK     PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  -------- ----------  ---------  ---------  --------------------  ----------
[...]
cf2b      mytask   linux       Succeeded  Timer      2019-06-28T21:00:23Z  00:00:06
cf2a      mytask   linux       Succeeded  Manual     2019-06-28T20:53:23Z  00:00:06
```

## <a name="manage-timer-triggers"></a>타이머 트리거 관리

az [acr 작업 타이머][az-acr-task-timer] 명령을 사용하여 ACR 작업에 대한 타이머 트리거를 관리합니다.

### <a name="add-or-update-a-timer-trigger"></a>타이머 트리거 추가 또는 업데이트

태스크를 만든 후, 선택적으로 [az acr 작업][az-acr-task-timer-add] 타이머 추가 명령을 사용 하 여 타이머 트리거를 추가 합니다. 다음 예제는 이전에 만든 *mytask에* 타이머 트리거 이름 *타이머2를* 추가합니다. 이 타이머는 매일 10:30 UTC에서 작업을 트리거합니다.

```azurecli
az acr task timer add \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

[az acr 작업 타이머 업데이트][az-acr-task-timer-update] 명령을 사용하여 기존 트리거의 일정을 업데이트하거나 상태를 변경합니다. 예를 들어 11:30 UTC에서 작업을 트리거하도록 *타이머2라는* 트리거를 업데이트합니다.

```azurecli
az acr task timer update \
  --name mytask \
  --registry myregistry \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>목록 타이머 트리거

[az acr 작업 타이머 목록][az-acr-task-timer-list] 명령은 작업에 대해 설정된 타이머 트리거를 보여 주며 다음과 같은 작업을 표시합니다.

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

### <a name="remove-a-timer-trigger"></a>타이머 트리거 제거

az [acr 작업 타이머 를][az-acr-task-timer-remove] 사용하여 명령을 제거하여 작업에서 타이머 트리거를 제거합니다. 다음 예제는 *mytask에서* *timer2* 트리거를 제거합니다.

```azurecli
az acr task timer remove \
  --name mytask \
  --registry myregistry \
  --timer-name timer2
```

## <a name="cron-expressions"></a>크론 표현식

ACR 태스크는 [NCronTab](https://github.com/atifaziz/NCrontab) 라이브러리를 사용하여 크론 식을 해석합니다. ACR 작업에서 지원되는 표현식에는 공백으로 구분되는 5개의 필수 필드가 있습니다.

`{minute} {hour} {day} {month} {day-of-week}`

cron 식과 함께 사용되는 표준 시간대는 UTC(유니버설 타임)를 조정한 값입니다. 시간은 24시간 형식입니다.

> [!NOTE]
> ACR 작업은 cron `{second}` `{year}` 식의 또는 필드를 지원하지 않습니다. 다른 시스템에서 사용된 cron 식을 복사하는 경우 해당 필드를 제거하는 경우 해당 필드를 제거해야 합니다.

각 필드에는 다음과 같은 형식의 값 중 하나가 포함될 수 있습니다.

|Type  |예제  |트리거될 때  |
|---------|---------|---------|
|특정 값 |<nobr>`"5 * * * *"`</nobr>|1시간 5분 간격으로 매시간|
|모든 값(`*`)|<nobr>`"* 5 * * *"`</nobr>|5:00 UTC시작 시간당 60회(하루 60회)|
|범위(`-` 연산자)|<nobr>`"0 1-3 * * *"`</nobr>|1일 3회, 1:00, 2:00, 3:00 UTC|
|값 집합(`,` 연산자)|<nobr>`"20,30,40 * * * *"`</nobr>|시간당 3회, 20분, 30분, 40분|
|간격 값(`/` 연산자)|<nobr>`"*/10 * * * *"`</nobr>|시간당 6회, 10분, 20분 등, 시간 초과

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>크론 예제

|예제|트리거될 때  |
|---------|---------|
|`"*/5 * * * *"`|5분마다 한 번|
|`"0 * * * *"`|1시간이 시작할 때마다 한 번|
|`"0 */2 * * *"`|2시간마다 한 번|
|`"0 9-17 * * *"`|9:00~17:00 UTC에 1회|
|`"30 9 * * *"`|매일 9:30 UTC에서|
|`"30 9 * * 1-5"`|평일 9:30 UTC|
|`"30 9 * Jan Mon"`|1월 매주 월요일 9:30 UTC|

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 레지스트리 또는 레지스트리, 컨테이너 인스턴스, 키 자격 증명 모음 및 서비스 주체를 포함하여 이 자습서 시리즈에서 만든 모든 리소스를 제거하려면 다음 명령을 내보십니다.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 타이머에 의해 자동으로 트리거되는 Azure 컨테이너 레지스트리 작업을 만드는 방법을 배웠습니다. 

예약된 작업을 사용하여 레지스트리의 리포지토리를 정리하는 예는 [Azure 컨테이너 레지스트리에서 이미지 자동 제거를](container-registry-auto-purge.md)참조합니다.

소스 코드 커밋 또는 기본 이미지 업데이트에 의해 트리거되는 작업의 예는 [ACR 작업 자습서 시리즈의](container-registry-tutorial-quick-task.md)다른 문서를 참조하십시오.



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
