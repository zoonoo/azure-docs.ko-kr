---
title: Media Services 메트릭 및 Azure Monitor를 통해 진단 로그를 모니터링 합니다. | Microsoft Docs
description: 이 문서에서는 Media Services 메트릭 및 Azure Monitor를 통해 진단 로그를 모니터링 하는 방법의 개요를 제공 합니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2019
ms.locfileid: "65964759"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Media Services 메트릭 및 진단 로그를 모니터링 합니다.

[Azure Monitor](../../azure-monitor/overview.md) 응용 프로그램을 수행 하는 방법을 이해할 수 있도록 진단 로그 및 메트릭 모니터링 하는 사용 하도록 설정 합니다. Azure Monitor에서 수집한 모든 데이터는 두 가지 기본 형식, 메트릭 및 로그 중 하나에 적합 합니다. Media Services에 대 한 진단 정보를 모니터링 하 고 수집 된 메트릭 및 로그에 대 한 알림과 경고를 만들 수 있습니다. 시각화 하 고 사용 하 여 메트릭 데이터를 분석할 수 있습니다 [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)합니다. 로그를 보낼 수 있습니다 [Azure Storage](https://azure.microsoft.com/services/storage/)로 스트리밍하 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)를 내보낼 [Log Analytics](https://azure.microsoft.com/services/log-analytics/), 또는 타사 서비스를 사용 합니다.

자세한 내용은 참조 하세요 [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md) 하 고 [Azure Monitor 진단 로그](../../azure-monitor/platform/diagnostic-logs-overview.md)합니다.

이 항목에서는 현재 사용할 수 있는 설명 [Media Services 메트릭을](#media-services-metrics) 하 고 [Media Services 진단 로그](#media-services-diagnostic-logs)합니다.

## <a name="media-services-metrics"></a>Media Services 메트릭

메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생시킬 수 있기 때문에, 메트릭은 경고에 유용합니다.

다음 Media Services를 현재 [스트리밍 끝점](https://docs.microsoft.com/rest/api/media/streamingendpoints) Azure에서 메트릭을 내보냅니다.

|메트릭|Display name|설명|
|---|---|---|
|요청|요청|스트리밍 끝점에서 서비스 요청 총 수에 대 한 세부 정보를 제공 합니다.|
|송신|송신|송신 바이트의 총 수입니다. 예를 들어 (바이트) 스트리밍 끝점에 의해 스트리밍됩니다.|
|SuccessE2ELatency|성공 종단 간 대기 시간| 성공한 요청의 종단 간 대기 시간에 대 한 정보를 제공합니다.|

예를 들어 하려면 CLI 사용 하 여 "송신" 메트릭을 가져오려면 실행 다음 `az monitor metrics` CLI 명령:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

메트릭 경고를 만드는 방법에 대 한 자세한 내용은 [만들기, 보기 및 관리 Azure Monitor를 사용 하 여 메트릭 경고](../../azure-monitor/platform/alerts-metric.md)합니다.

## <a name="media-services-diagnostic-logs"></a>Media Services에 대 한 진단 정보

현재 다음 진단 로그를 가져올 수 있습니다.

|이름|설명|
|---|---|
|키 배달 서비스 요청|키 배달 서비스를 보여 주는 로그 정보를 요청 합니다. 자세한 내용은 참조 하세요. [스키마](media-services-diagnostic-logs-schema.md)합니다.|

저장소 계정에서 진단 로그의 저장소를 사용 하도록 설정 하려면 다음 실행 `az monitor diagnostic-settings` CLI 명령: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

예를 들면 다음과 같습니다.

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>다음 단계 

[Azure 리소스에서 로그 데이터 수집 및 소비 하는 방법을](../../azure-monitor/platform/diagnostic-logs-overview.md)합니다.
