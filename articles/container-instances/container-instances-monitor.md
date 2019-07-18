---
title: Azure Container Instances에서 컨테이너 모니터링
description: Azure Container Instances에서 컨테이너의 CPU 및 메모리와 같은 컴퓨팅 리소스의 소비를 모니터링하는 방법입니다.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 7b46ea0518038eeb908591b8438acc2a9095242c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570915"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Azure Container Instances에서 컨테이너 리소스 모니터링

[Azure Monitor][azure-monitoring]는 컨테이너 인스턴스에서 사용되는 컴퓨팅 리소스에 대한 인사이트를 제공합니다. 이 리소스 사용량 데이터를 통해 컨테이너 그룹에 대한 최상의 리소스 설정을 결정할 수 있습니다. 또한 Azure Monitor는 컨테이너 인스턴스에서 네트워크 작업을 추적하는 메트릭을 제공합니다.

이 문서에서는 Azure Portal 및 Azure CLI를 사용하여 컨테이너 인스턴스에 대한 Azure Monitor 메트릭 수집에 대해 자세히 설명합니다.

> [!IMPORTANT]
> Azure Container Instances의 Azure Monitor 메트릭은 현재 미리 보기로 제공되며, 일부 [제한 사항이 적용](#preview-limitations)됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

이 때 Azure Monitor 메트릭은 Linux 컨테이너에만 사용할 수 있습니다.

## <a name="available-metrics"></a>사용 가능한 메트릭

Azure Monitor는 다음과 같은 [Azure Container Instances를 위한 메트릭][supported-metrics]을 제공합니다. 이러한 메트릭은 컨테이너 그룹 및 개별 컨테이너에 사용할 수 있습니다.

* **CPU 사용량** - **밀리코어** 단위로 측정됩니다. 1밀리코어는 CPU 코어의 1/1000이므로 500밀리코어(또는 500m)는 CPU 코어의 50% 사용을 나타냅니다. 모든 코어에 대해 **평균 사용량**으로 집계됩니다.

* **메모리 사용량** - **평균 바이트**로 집계됩니다.

* **초당 수신된 네트워크 바이트** 및 **초당 전송된 네트워크 바이트** - **초당 평균 바이트 수**로 집계됩니다. 

## <a name="get-metrics---azure-portal"></a>메트릭 가져오기 - Azure Portal

컨테이너 그룹을 만들 때 Azure Portal에서 Azure Monitor 데이터를 사용할 수 있습니다. 컨테이너 그룹에 대한 메트릭을 보려면 컨테이너 그룹에 대한 **개요** 페이지로 이동합니다. 여기서 각 사용 가능한 메트릭에 대해 미리 생성된 차트를 볼 수 있습니다.

![이중 차트][dual-chart]

여러 컨테이너를 포함하는 컨테이너 그룹에서 [차원][monitor-dimension]을 사용하여 컨테이너별로 메트릭을 표시합니다. 개별 컨테이너 메트릭이 있는 차트를 만들려면 다음 단계를 수행합니다.

1. **개요** 페이지에서 **CPU**와 같은 메트릭 차트 중 하나를 선택합니다. 
1. **분할 적용** 단추를 선택하고 **컨테이너 이름**을 선택합니다.

![차원][dimension]

## <a name="get-metrics---azure-cli"></a>메트릭 가져오기 - Azure CLI

Azure CLI를 사용하여 컨테이너 인스턴스에 대한 메트릭을 수집할 수도 있습니다. 먼저 다음 명령을 사용하여 컨테이너 그룹의 ID를 가져옵니다. `<resource-group>`을 리소스 그룹 이름으로 바꾸고, `<container-group>`을 컨테이너 그룹의 이름으로 바꿉니다.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

다음 명령을 사용하여 **CPU** 사용량 메트릭을 가져옵니다.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

명령에서 `--metric` 매개 변수의 값을 변경하여 다른 [지원되는 메트릭][supported-metrics]을 가져옵니다. 예를 들어 **메모리** 사용량 메트릭을 가져오려면 다음 명령을 사용합니다. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

다중 컨테이너 그룹의 경우 컨테이너당 메트릭을 반환하도록 `containerName` 차원을 추가할 수 있습니다.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>다음 단계

[Azure 모니터링 개요][azure-monitoring]에서 Azure 모니터링에 대해 자세히 알아봅니다.

Azure Container Instances에 대한 메트릭이 임계값을 초과할 때 알림을 받을 수 있도록 [메트릭 경고][metric-alert]를 만드는 방법을 알아봅니다.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
