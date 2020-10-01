---
title: Azure HPC 캐시 관리 및 업데이트
description: Azure Portal 또는 Azure CLI를 사용 하 여 Azure HPC 캐시를 관리 하 고 업데이트 하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 08/31/2020
ms.author: v-erkel
ms.openlocfilehash: 19950ca215abbac3a56bdb901448c9d92ad369be
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2020
ms.locfileid: "91613056"
---
# <a name="manage-your-cache"></a>캐시 관리

Azure Portal의 캐시 개요 페이지에는 캐시에 대 한 프로젝트 세부 정보, 캐시 상태 및 기본 통계가 표시 됩니다. 캐시를 중지 또는 시작 하 고, 캐시를 삭제 하 고, 장기 저장소로 데이터를 플러시하고, 소프트웨어를 업데이트 하는 컨트롤도 있습니다.

이 문서에서는 Azure CLI 사용 하 여 이러한 기본 작업을 수행 하는 방법에 대해서도 설명 합니다.

개요 페이지를 열려면 Azure Portal에서 캐시 리소스를 선택 합니다. 예를 들어 **모든 리소스** 페이지를 로드 하 고 캐시 이름을 클릭 합니다.

![Azure HPC 캐시 인스턴스의 개요 페이지 스크린샷](media/hpc-cache-overview.png)

페이지 맨 위에 있는 단추를 통해 캐시를 관리할 수 있습니다.

* **시작** 및 [**중지**](#stop-the-cache) -캐시 작업을 다시 시작 하거나 일시 중지 합니다.
* [**플러시**](#flush-cached-data) -변경 된 데이터를 저장소 대상에 기록 합니다.
* [**업그레이드**](#upgrade-cache-software) -캐시 소프트웨어를 업데이트 합니다.
* [**진단 수집**](#collect-diagnostics) -디버깅 정보 업로드
* **새로 고침** -개요 페이지를 다시 로드 합니다.
* [**삭제**](#delete-the-cache) -캐시를 영구적으로 삭제 합니다.

아래에서 이러한 옵션에 대해 자세히 알아보세요.

캐시 관리 작업을 보여 주는 [동영상](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) 을 시청 하려면 아래 이미지를 클릭 하세요.

[![비디오 미리 보기: Azure HPC 캐시: 관리 (비디오 페이지를 방문 하려면 클릭)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>캐시 중지

비활성 기간 동안 캐시를 중지 하 여 비용을 줄일 수 있습니다. 캐시를 중지 하는 동안에는 작동 시간에 대 한 요금이 부과 되지 않지만 캐시의 할당 된 디스크 저장소에 대 한 요금이 청구 됩니다. 자세한 내용은 [가격 책정](https://aka.ms/hpc-cache-pricing) 페이지를 참조 하세요.

중지 된 캐시는 클라이언트 요청에 응답 하지 않습니다. 캐시를 중지 하기 전에 클라이언트를 분리 해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

**중지** 단추를 클릭 하면 활성 캐시가 일시 중단 됩니다. **중지** 단추는 캐시의 상태가 **정상** 이거나 **저하**된 경우에 사용할 수 있습니다.

![중지를 강조 표시 한 위쪽 단추의 스크린샷 및 중지 작업을 설명 하 고 ' 계속 하 시겠습니까? ' 라는 팝업 메시지 예 (기본값) 및 아니요 단추](media/stop-cache.png)

예를 클릭 하 여 캐시 중지를 확인 한 후 캐시에서 해당 콘텐츠를 저장소 대상에 자동으로 플러시합니다. 이 프로세스는 다소 시간이 걸릴 수 있지만 데이터 일관성을 보장 합니다. 마지막으로 캐시 상태가 **중지 됨**으로 변경 됩니다.

중지 된 캐시를 다시 활성화 하려면 **시작** 단추를 클릭 합니다. 확인은 필요 하지 않습니다.

![시작이 강조 표시 된 위쪽 단추의 스크린샷](media/start-cache.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[Az hpc cache stop](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-stop) 명령을 사용 하 여 캐시를 임시로 일시 중단 합니다. 이 작업은 캐시 상태가 **정상** 또는 **저하**인 경우에만 유효 합니다.

캐시는 중지 하기 전에 해당 콘텐츠를 저장소 대상에 자동으로 플러시합니다. 이 프로세스는 다소 시간이 걸릴 수 있지만 데이터 일관성을 보장 합니다.

작업이 완료 되 면 캐시 상태가 **중지 됨**으로 변경 됩니다.

[Az hpc-cache start](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-start)를 사용 하 여 중지 된 캐시를 다시 활성화 합니다.

시작 또는 중지 명령을 실행 하면 작업이 완료 될 때까지 명령줄에서 "실행 중" 상태 메시지를 표시 합니다.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

완료 되 면 메시지는 "완료 됨"으로 업데이트 되 고 반환 코드 및 기타 정보를 표시 합니다.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>캐시 된 데이터 플러시

개요 페이지의 **플러시** 단추는 캐시에 저장 된 변경 된 모든 데이터를 백 엔드 저장소 대상에 즉시 쓰도록 캐시에 지시 합니다. 캐시는 데이터를 저장소 대상에 정기적으로 저장 하므로 백 엔드 저장소 시스템이 최신 상태 인지 확인 하지 않는 한이 작업을 수동으로 수행할 필요가 없습니다. 예를 들어 저장소 스냅숏을 만들거나 데이터 집합 크기를 확인 하기 전에 **Flush** 를 사용할 수 있습니다.

> [!NOTE]
> 플러시 프로세스 중에 캐시는 클라이언트 요청을 처리할 수 없습니다. 작업이 완료 된 후에 캐시 액세스가 일시 중단 되 고 다시 시작 됩니다.

캐시 플러시 작업을 시작 하면 캐시에서 클라이언트 요청 수락이 중지 되 고 개요 페이지의 캐시 상태가 **플러시하**로 변경 됩니다.

캐시의 데이터는 적절 한 저장소 대상에 저장 됩니다. 플러시된 데이터 양에 따라 프로세스는 몇 분 정도 걸릴 수 있습니다.

모든 데이터가 저장소 대상에 저장 되 고 나면 캐시가 자동으로 클라이언트 요청을 다시 가져오기 시작 합니다. 캐시 상태가 **정상**으로 돌아갑니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

캐시를 플러시하려면 **플러시** 단추를 클릭 한 다음 **예** 를 클릭 하 여 작업을 확인 합니다.

![플러시가 강조 표시 된 위쪽 단추의 스크린샷 및 플러시 작업을 설명 하 고 ' 계속 할까요? ' 라는 팝업 메시지가 표시 됩니다. 예 (기본값) 및 아니요 단추](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

[Az hpc-cache flush](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-flush) 를 사용 하 여 캐시에서 변경 된 모든 데이터를 저장소 대상에 쓰도록 합니다.

예제:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

플러시가 완료 되 면 성공 메시지가 반환 됩니다.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>캐시 소프트웨어 업그레이드

새 소프트웨어 버전을 사용할 수 있는 경우 **업그레이드** 단추가 활성화 됩니다. 또한 페이지 맨 위에 소프트웨어 업데이트에 대 한 메시지가 표시 됩니다.

![업그레이드 단추가 활성화 된 단추 위쪽 행의 스크린샷](media/hpc-cache-upgrade-button.png)

클라이언트 액세스는 소프트웨어를 업그레이드 하는 동안 중단 되지 않지만 캐시 성능이 느려집니다. 사용량이 많지 않은 사용 시간 또는 계획 된 유지 관리 기간 동안 소프트웨어를 업그레이드 하도록 계획 합니다.

소프트웨어 업데이트에는 몇 시간이 걸릴 수 있습니다. 높은 처리량으로 구성 된 캐시는 최대 처리량 값이 적은 캐시 보다 업그레이드 하는 데 더 많은 시간이 걸립니다.

소프트웨어 업그레이드를 사용할 수 있는 경우 일주일 또는 수동으로 적용 해야 합니다. 종료 날짜는 업그레이드 메시지에 나열 됩니다. 이 시간 동안 업그레이드 하지 않으면 Azure에서 자동으로 캐시에 업데이트를 적용 합니다. 자동 업그레이드 타이밍은 구성할 수 없습니다. 캐시 성능 영향에 대해 염려 하는 경우 기간이 만료 되기 전에 소프트웨어를 직접 업그레이드 해야 합니다.

종료 날짜가 전달 될 때 캐시가 중지 되 면 다음에 시작 될 때 캐시가 자동으로 소프트웨어를 업그레이드 합니다. 업데이트는 즉시 시작 되지 않을 수도 있지만 처음부터 시작 됩니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

**업그레이드** 단추를 클릭 하 여 소프트웨어 업데이트를 시작 합니다. 작업이 완료 될 때까지 캐시 상태가 **업그레이드** 중으로 변경 됩니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Azure CLI에서 새 소프트웨어 정보는 캐시 상태 보고서의 끝에 포함 됩니다. (확인 하려면 [az hpc cache show](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-show) 를 사용 하십시오.) 메시지에서 "upgradeStatus" 문자열을 찾습니다.

[Az hpc-cache upgrade-펌웨어](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-upgrade-firmware) 를 사용 하 여 업데이트를 적용 합니다 (있는 경우).

업데이트를 사용할 수 없는 경우에는이 작업이 적용 되지 않습니다.

이 예제에서는 캐시 상태 (업데이트를 사용할 수 없음)와 업그레이드-펌웨어 명령의 결과를 보여 줍니다.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>진단 수집

**진단 수집** 단추는 수동으로 시스템 정보를 수집 하 고 Microsoft 서비스에 업로드 하는 프로세스를 시작 하 고 문제 해결을 지원 합니다. 캐시는 심각한 캐시 문제가 발생 하는 경우 동일한 진단 정보를 자동으로 수집 하 고 업로드 합니다.

Microsoft 서비스 및 지원에서 요청 하는 경우이 컨트롤을 사용 합니다.

단추를 클릭 한 후 **예** 를 클릭 하 여 업로드를 확인 합니다.

![' 진단 컬렉션 시작 ' 팝업 확인 메시지의 스크린샷 기본 단추 ' 예 '가 강조 표시 됩니다.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>캐시 삭제

삭제 단추를 클릭 하면 캐시가 **삭제** 됩니다. 캐시를 삭제 하면 모든 리소스가 삭제 되 고 더 이상 계정 요금이 발생 하지 않습니다.

저장소 대상으로 사용 되는 백 엔드 저장소 볼륨은 캐시를 삭제할 때 영향을 받지 않습니다. 나중에 나중에 캐시에 추가 하거나 개별적으로 서비스를 해제할 수 있습니다.

> [!NOTE]
> Azure HPC 캐시는 캐시를 삭제 하기 전에 캐시에서 자동으로 변경 된 데이터를 백 엔드 저장소 시스템에 기록 하지 않습니다.
>
> 캐시의 모든 데이터가 장기 저장소에 기록 되었는지 확인 하려면 삭제 하기 전에 [캐시를 중지](#stop-the-cache) 합니다. 삭제 하기 전에 상태가 **중지 됨** 으로 표시 되는지 확인 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

캐시를 중지 한 후 **삭제** 단추를 클릭 하 여 캐시를 영구적으로 제거 합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [cli-reminder.md](includes/cli-reminder.md)]

Azure CLI 명령 [az hpc-cache delete](/cli/azure/ext/hpc-cache/hpc-cache#ext-hpc-cache-az-hpc-cache-delete) 를 사용 하 여 캐시를 영구적으로 제거 합니다.

예제:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="cache-metrics-and-monitoring"></a>캐시 메트릭 및 모니터링

개요 페이지에는 몇 가지 기본 캐시 통계, 캐시 처리량, 초당 작업 및 대기 시간에 대 한 그래프가 표시 됩니다.

![샘플 캐시에 대해 위에서 언급 한 통계를 보여 주는 세 개의 선 그래프 스크린샷](media/hpc-cache-overview-stats.png)

이러한 차트는 Azure의 기본 제공 모니터링 및 분석 도구에 포함 됩니다. 추가 도구 및 경고는 포털 사이드바의 **모니터링** 제목 아래에 있는 페이지에서 사용할 수 있습니다. [Azure 모니터링 설명서](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal)의 포털 섹션에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

* [Azure 메트릭 및 통계 도구](../azure-monitor/index.yml) 에 대해 자세히 알아보기
* [AZURE HPC 캐시로 도움](hpc-cache-support-ticket.md) 받기
