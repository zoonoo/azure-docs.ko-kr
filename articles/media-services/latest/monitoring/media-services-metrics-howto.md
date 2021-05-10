---
title: Azure Monitor로 메트릭 보기
description: 이 문서에서는 Azure Portal 차트와 Azure CLI를 사용하여 메트릭을 모니터링하는 방법을 보여줍니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: b408020ecf6e4618f97c8bc51749278dc0361579
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108138916"
---
# <a name="monitor-media-services-metrics"></a>Media Services 메트릭 모니터링

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

[Azure Monitor](../../../azure-monitor/overview.md)를 사용하면 애플리케이션의 성능을 파악하는 데 도움이 되는 메트릭 및 진단 로그를 모니터링할 수 있습니다. 이 기능에 대한 자세한 설명을 보고 Azure Media Services 메트릭 및 진단 로그를 사용해야 하는 이유를 이해하려면 [Media Services 메트릭 및 진단 로그 모니터링](monitor-media-services-data-reference.md)을 참조하세요.

Azure Monitor에서는 포털에서의 차트 작성, REST API를 통한 액세스, Azure CLI를 통한 쿼리 등, 메트릭과 상호 작용하는 몇 가지 방법을 제공합니다. 이 문서에서는 Azure Portal 차트와 Azure CLI를 사용하여 메트릭을 모니터링하는 방법을 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

- [Media Services 계정 만들기](../account-create-how-to.md)
- [Media Services 메트릭 및 진단 로그 모니터링](monitor-media-services-data-reference.md)을 검토하세요.

## <a name="view-metrics-in-azure-portal"></a>Azure Portal에서 메트릭 보기

1. [https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.
1. Azure Media Services 계정으로 이동하여 **메트릭** 을 선택합니다.
1. **범위** 상자를 클릭하고 모니터링하려는 리소스를 선택합니다.

    **범위 선택** 창이 사용 가능한 리소스 목록과 함께 오른쪽에 표시됩니다. 여기서는 다음 리소스가 표시됩니다.

    * &lt;Media Services 계정 이름&gt;
    * &lt;Media Services 계정 이름&gt;/&lt; 스트리밍 엔드포인트 이름&gt;
    * &lt;스토리지 계정 이름&gt;

    필터링한 다음, 리소스를 선택하고 **적용** 을 누릅니다. 지원되는 리소스 및 메트릭에 대한 자세한 내용은 [Media Services 메트릭 모니터링](monitor-media-services-data-reference.md)을 참조하세요.

    > [!NOTE]
    > 모니터링할 리소스를 전환하려면 **원본** 상자를 다시 클릭하고 이 단계를 반복합니다.

1. 선택 사항: 차트 이름을 지정합니다(맨 위에 있는 연필을 눌러 이름 편집).
1. 보려는 메트릭을 추가합니다.
1. 차트를 대시보드에 고정할 수 있습니다.

## <a name="view-metrics-with-azure-cli"></a>Azure CLI로 메트릭 보기

Azure CLI를 사용하여 "송신" 메트릭을 가져오려면 다음 `az monitor metrics` 명령을 실행합니다.

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

다른 메트릭을 가져오려면 "송신"을 관심 있는 메트릭 이름을 바꿉니다.

## <a name="see-also"></a>추가 정보

- [Azure Monitor 메트릭](../../../azure-monitor/data-platform.md)
- [Azure Monitor를 사용하여 메트릭 경고 만들기, 보기 및 관리](../../../azure-monitor/alerts/alerts-metric.md)

## <a name="next-steps"></a>다음 단계

[진단 로그](../media-services-diagnostic-logs-howto.md)