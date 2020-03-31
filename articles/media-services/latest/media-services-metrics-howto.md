---
title: Azure 모니터를 통해 메트릭 보기
description: 이 문서에서는 Azure 포털 차트 및 Azure CLI를 사용하여 메트릭을 모니터링하는 방법을 보여 주며 있습니다.
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382939"
---
# <a name="monitor-media-services-metrics"></a>Media Services 메트릭 모니터링

[Azure Monitor를](../../azure-monitor/overview.md) 사용하면 응용 프로그램의 성능을 이해하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. 이 기능에 대한 자세한 설명과 Azure Media 서비스 메트릭 및 진단 로그를 사용하려는 이유를 보려면 [미디어 서비스 메트릭 및 진단 로그 모니터링을](media-services-metrics-diagnostic-logs.md)참조하십시오.

Azure Monitor는 포털에서 차트를 표시하거나, REST API를 통해 액세스하거나, Azure CLI를 사용하여 쿼리하는 등 메트릭과 상호 작용하는 여러 가지 방법을 제공합니다. 이 문서에서는 Azure 포털 차트 및 Azure CLI를 사용하여 메트릭을 모니터링하는 방법을 보여 주며 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [미디어 서비스 계정 만들기](create-account-cli-how-to.md)
- [미디어 서비스 메트릭 및 진단 로그 모니터링](media-services-metrics-diagnostic-logs.md) 검토

## <a name="view-metrics-in-azure-portal"></a>Azure 포털에서 메트릭 보기

1. [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.
1. Azure 미디어 서비스 계정으로 이동하여 **메트릭을 선택합니다.**
1. **RESOURCE** 상자를 클릭하고 메트릭을 모니터링할 리소스를 선택합니다.

    사용 가능한 리소스 목록이 있는 리소스 **선택** 창이 오른쪽에 나타납니다. 이 경우 다음이 표시됩니다.

    * &lt;미디어 서비스 계정 이름&gt;
    * &lt;미디어 서비스&gt;/&lt;계정 이름 스트리밍 끝점 이름&gt;
    * &lt;저장소 계정 이름&gt;

    리소스를 선택하고 **적용을**누릅니다. 지원되는 리소스 및 메트릭에 대한 자세한 내용은 [미디어 서비스 모니터링 메트릭을](media-services-metrics-diagnostic-logs.md)참조하십시오.

    ![메트릭](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > 메트릭을 모니터링할 리소스 간에 전환하려면 **RESOURCE** 상자를 다시 클릭하고 이 단계를 반복합니다.
1. (선택적으로) 차트에 이름을 지정합니다(상단의 연필을 눌러 이름을 편집).
1. 보려는 측정항목을 추가합니다.

    ![메트릭](media/media-services-metrics/metrics03.png)
1. 대시보드에 차트를 고정할 수 있습니다.

## <a name="view-metrics-with-azure-cli"></a>Azure CLI를 통해 메트릭 보기

Azure CLI를 사용하여 "Egress" 메트릭을 얻으려면 `az monitor metrics` 다음 명령을 실행합니다.

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

다른 메트릭을 얻으려면 관심 있는 메트릭 이름에 대해 "Egress"로 대체합니다.

## <a name="see-also"></a>참조

* [Azure 모니터 메트릭](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor 를 사용하여 메트릭 경고를 생성, 보기 및 관리합니다.](../../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>다음 단계

[진단 로그](media-services-diagnostic-logs-howto.md)
