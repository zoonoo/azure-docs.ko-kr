---
title: 자습서 - ACR 작업 예약
description: 이 자습서에서는 하나 이상의 타이머 트리거를 설정하여 정의된 일정에 Azure Container Registry 작업을 실행하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780860"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>자습서: 정의된 일정에 따라 ACR 작업 실행

이 자습서에서는 일정에 따라 [ACR 작업](container-registry-tasks-overview.md)을 실행하는 방법을 보여 줍니다. 하나 이상의 *타이머 트리거* 를 설정하여 작업을 예약합니다. 타이머 트리거는 단독으로 사용하거나 다른 작업 트리거와 함께 사용할 수 있습니다.

이 자습서에서는 다음 작업을 예약하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 타이머 트리거를 사용하여 작업 만들기
> * 타이머 트리거 관리

작업 예약은 다음과 같은 시나리오에 유용합니다.

* 예약된 유지 관리 작업에 대해 컨테이너 워크로드를 실행합니다. 예를 들어 컨테이너화된 앱을 실행하여 레지스트리에서 불필요한 이미지를 제거합니다.
* 근무일 동안 프로덕션 이미지에 대한 테스트 집합을 라이브 사이트 모니터링의 일부로 실행합니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>작업 예약 정보

* **cron 식을 사용한 트리거** - 작업의 타이머 트리거는 *cron 식* 을 사용합니다. 이 식은 작업을 트리거할 분, 시간, 일, 월 및 요일을 지정하는 5개의 필드가 포함된 문자열입니다. 분당 최대 1번의 빈도가 지원됩니다.

  예를 들어 `"0 12 * * Mon-Fri"` 식은 평일 정오 UTC에 작업을 트리거합니다. 이 도움말 뒷부분의 [세부 정보](#cron-expressions)를 참조하세요.
* **다중 타이머 트리거** - 일정이 다른 경우 작업에 여러 타이머를 추가할 수 있습니다.
    * 작업을 만들거나 나중에 추가할 때 여러 타이머 트리거를 지정합니다.
    * 필요에 따라 더 쉽게 관리할 수 있도록 트리거의 이름을 지정하거나 ACR 작업에서 기본 트리거 이름을 제공합니다.
    * 타이머 일정이 한 번에 겹치면 ACR 작업에서 각 타이머에 대해 예약된 시간에 작업을 트리거합니다.
* **기타 작업 트리거** - 타이머로 트리거되는 작업에서 [소스 코드 커밋](container-registry-tutorial-build-task.md) 또는 [기본 이미지 업데이트](container-registry-tutorial-base-image-update.md)를 기반으로 트리거를 사용하도록 설정할 수도 있습니다. 다른 ACR 작업과 마찬가지로 예약된 작업을 [수동으로 실행][az-acr-task-run]할 수도 있습니다.

## <a name="create-a-task-with-a-timer-trigger"></a>타이머 트리거를 사용하여 작업 만들기

### <a name="task-command"></a>작업 명령

먼저 다음 셸 환경 변수를 사용자 환경에 적합한 값으로 채웁니다. 이 단계는 반드시 필요한 것이 아니지만, 여러 줄로 된 Azure CLI 명령을 이 자습서에서 좀 더 쉽게 실행할 수 있게 합니다. 이러한 환경 변수를 채우지 않는 경우 명령 예에 나타나는 각 값을 수동으로 바꿔야 합니다.

[![Embed 시작](https://shell.azure.com/images/launchcloudshell.png "Azure Cloud Shell 시작")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

[az acr task create][az-acr-task-create] 명령을 사용하여 작업을 생성할 때 선택적으로 타이머 트리거를 추가할 수 있습니다. `--schedule` 매개 변수를 추가하고 타이머에 대한 cron 식을 전달합니다.

간단한 예로서 다음 작업은 매일 21:00 UTC에 Microsoft Container Registry에서 `hello-world` 이미지 실행을 트리거합니다. 작업은 소스 코드 컨텍스트 없이 실행됩니다.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

[az acr task show][az-acr-task-show] 명령을 실행하여 타이머 트리거가 구성되어 있는지 확인합니다. 기본적으로 기본 이미지 업데이트 트리거도 사용하도록 설정됩니다.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>작업 트리거

[az acr task run][az-acr-task-run]을 사용하여 작업을 수동으로 트리거하여 올바르게 설정되었는지 확인합니다.

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

컨테이너가 성공적으로 실행되면 출력은 다음과 유사합니다. 출력에서는 주요 단계를 요약된 형식으로 보여 주고 있습니다.

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

예약된 시간 후에 [az acr task list-runs][az-acr-task-list-runs] 명령을 실행하여 타이머가 예상대로 작업을 트리거했는지 확인합니다.

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

타이머가 성공하면 출력은 다음과 유사합니다.

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>타이머 트리거 관리

[az acr task timer][az-acr-task-timer] 명령을 사용하여 ACR 작업에 대한 타이머 트리거를 관리합니다.

### <a name="add-or-update-a-timer-trigger"></a>타이머 트리거 추가 또는 업데이트

작업이 생성된 후 선택적으로 [az acr task timer add][az-acr-task-timer-add] 명령을 사용하여 타이머 트리거를 추가합니다. 다음 예에서는 이전에 만든 *timertask* 에 타이머 트리거 이름 *timer2* 를 추가합니다. 이 타이머는 매일 10:30 UTC 마다 작업을 트리거합니다.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

[az acr task timer update][az-acr-task-timer-update] 명령을 사용하여 기존 트리거의 일정을 업데이트하거나 해당 상태를 변경합니다. 예를 들어 11:30 UTC에 작업을 트리거하도록 이름이 *timer2* 인 트리거를 업데이트합니다.

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>타이머 트리거 나열

[az acr task timer list][az-acr-task-timer-list] 명령은 작업에 설정된 타이머 트리거를 보여 줍니다.

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
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

[az acr task timer remove][az-acr-task-timer-remove] 명령을 사용하여 작업에서 타이머 트리거를 제거합니다. 다음 예에서는 *timertask* 에서 *timer2* 트리거를 제거합니다.

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Cron 식

ACR 작업은 [NCronTab](https://github.com/atifaziz/NCrontab) 라이브러리를 사용하여 cron 식을 해석합니다. ACR 작업에서 지원되는 식에는 공백으로 구분된 5개의 필수 필드가 있습니다.

`{minute} {hour} {day} {month} {day-of-week}`

cron 식과 함께 사용하는 표준 시간대는 UTC(협정 세계시)입니다. 시간은 24시간 형식입니다.

> [!NOTE]
> ACR 작업은 cron 식에서 `{second}` 또는 `{year}` 필드를 지원하지 않습니다. 다른 시스템에 사용되는 cron 식을 복사하는 경우 이러한 필드를 사용하는 경우 제거해야 합니다.

각 필드에는 다음과 같은 형식의 값 중 하나가 포함될 수 있습니다.

|Type  |예제  |트리거될 때  |
|---------|---------|---------|
|특정 값 |<nobr>`"5 * * * *"`</nobr>|매시간 5분|
|모든 값(`*`)|<nobr>`"* 5 * * *"`</nobr>|5:00 UTC부터 매시간 분마다(하루에 60번)|
|범위(`-` 연산자)|<nobr>`"0 1-3 * * *"`</nobr>|하루에 3번, 1:00, 2:00 및 3:00 UTC|
|값 집합(`,` 연산자)|<nobr>`"20,30,40 * * * *"`</nobr>|시간당 3번, 매시간 20분, 30분, 40분|
|간격 값(`/` 연산자)|<nobr>`"*/10 * * * *"`</nobr>|시간당 6번, 매시간 10분, 20분 등

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Cron 예

|예제|트리거될 때  |
|---------|---------|
|`"*/5 * * * *"`|5분마다 한 번|
|`"0 * * * *"`|1시간이 시작할 때마다 한 번|
|`"0 */2 * * *"`|2시간마다 한 번|
|`"0 9-17 * * *"`|시간당 1번, 9:00 ~ 17:00 UTC|
|`"30 9 * * *"`|매일 9:30 UTC|
|`"30 9 * * 1-5"`|평일마다 9:30 UTC|
|`"30 9 * Jan Mon"`|1월 월요일마다 9:30 UTC|

## <a name="clean-up-resources"></a>리소스 정리

컨테이너 레지스트리, 컨테이너 인스턴스, 키 자격 증명 모음 및 서비스 주체를 포함하여 이 자습서 시리즈에서 만든 모든 리소스를 제거하려면 다음 명령을 실행합니다.

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 타이머에 의해 자동으로 트리거되는 Azure Container Registry 작업을 만드는 방법에 대해 알아보았습니다. 

예약된 작업을 사용하여 레지스트리의 리포지토리를 정리하는 예는 [Azure Container Registry에서 이미지 자동 제거](container-registry-auto-purge.md)를 참조하세요.

소스 코드 커밋 또는 기본 이미지 업데이트로 트리거되는 작업의 예는 [ACR 작업 자습서 시리즈](container-registry-tutorial-quick-task.md)의 다른 문서를 참조하세요.



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
