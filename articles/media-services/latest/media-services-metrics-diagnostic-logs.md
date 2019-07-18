---
title: Azure Media Services 메트릭 및 Azure Monitor를 통해 진단 로그를 모니터링 합니다. | Microsoft Docs
description: 이 문서에서는 Azure Media Services 메트릭 및 Azure Monitor를 통해 진단 로그를 모니터링 하는 방법의 개요를 제공 합니다.
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
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806006"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Media Services 메트릭 및 진단 로그를 모니터링 합니다.

[Azure Monitor](../../azure-monitor/overview.md) 응용 프로그램을 수행 하는 방법을 이해할 수 있도록 진단 로그 및 메트릭 모니터링 하는 사용 하도록 설정 합니다. Azure Monitor에서 수집한 모든 데이터는 두 가지 기본 형식, 메트릭 및 로그 중 하나에 적합 합니다. Media Services에 대 한 진단 정보를 모니터링 하 고 수집 된 메트릭 및 로그에 대 한 알림과 경고를 만들 수 있습니다. 시각화 하 고 사용 하 여 메트릭 데이터를 분석할 수 있습니다 [메트릭 탐색기](../../azure-monitor/platform/metrics-getting-started.md)합니다. 로그를 보낼 수 있습니다 [Azure Storage](https://azure.microsoft.com/services/storage/)로 스트리밍하 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)를 내보낼 [Log Analytics](https://azure.microsoft.com/services/log-analytics/), 또는 타사 서비스를 사용 합니다.

자세한 개요를 참조 하세요 [Azure Monitor 메트릭](../../azure-monitor/platform/data-platform.md) 하 고 [Azure Monitor 진단 로그](../../azure-monitor/platform/diagnostic-logs-overview.md)합니다.

이 항목에 설명 지원 [Media Services 메트릭을](#media-services-metrics) 하 고 [Media Services 진단 로그](#media-services-diagnostic-logs)합니다.

## <a name="media-services-metrics"></a>Media Services 메트릭

메트릭은 값이 변경되었는지 여부와 상관 없이 정기적으로 수집됩니다. 메트릭은 자주 샘플링할 수 있고 경고는 비교적 간단한 논리를 사용하여 신속하게 발생시킬 수 있기 때문에, 메트릭은 경고에 유용합니다. 메트릭 경고를 만드는 방법에 대 한 자세한 내용은 [만들기, 보기 및 관리 Azure Monitor를 사용 하 여 메트릭 경고](../../azure-monitor/platform/alerts-metric.md)합니다.

Media Services는 다음 리소스를 모니터링 메트릭을 지원합니다.

* 계정
* 스트리밍 엔드포인트
 
### <a name="account"></a>계정

다음 계정 메트릭을 모니터링할 수 있습니다. 

|메트릭 이름|Display name|설명|
|---|---|---|
|AssetCount|자산 수|계정에 자산입니다.|
|AssetQuota|자산 할당량|계정에 자산 할당량입니다.|
|AssetQuotaUsedPercentage|자산 사용 할당량 비율|이미 사용 되는 자산 할당량의 비율입니다.|
|ContentKeyPolicyCount|콘텐츠 키 정책 수|계정에서 콘텐츠 키 정책입니다.|
|ContentKeyPolicyQuota|콘텐츠 키 정책 할당량|계정에서 콘텐츠 키 정책 할당량입니다.|
|ContentKeyPolicyQuotaUsedPercentage|콘텐츠 키 정책 할당량 사용 비율|이미 사용 되는 콘텐츠 키 정책 할당량의 비율입니다.|
|StreamingPolicyCount|스트리밍 정책 개수|계정에서 스트리밍 정책입니다.|
|StreamingPolicyQuota|스트리밍 정책 할당량|계정의 스트리밍 정책 할당량입니다.|
|StreamingPolicyQuotaUsedPercentage|스트리밍 정책 할당량 사용 비율|이미 사용 되는 스트리밍 정책 할당량의 비율입니다.|
 
도 검토 해야 [계정 할당량 및 제한 사항](limits-quotas-constraints.md)합니다.

### <a name="streaming-endpoint"></a>스트리밍 엔드포인트

다음 Media Services [스트리밍 끝점](https://docs.microsoft.com/rest/api/media/streamingendpoints) 메트릭이 지원 됩니다.

|메트릭 이름|Display name|Description|
|---|---|---|
|요청|요청|스트리밍 끝점에서 제공 하는 HTTP 요청의 총 수를 제공 합니다.|
|송신|송신|송신 바이트의 총 수입니다. 예를 들어 (바이트) 스트리밍 끝점에 의해 스트리밍됩니다.|
|SuccessE2ELatency|성공 종단 간 대기 시간|스트리밍 끝점에서 응답의 마지막 바이트가 전송 된 경우에 요청을 수신 하는 경우의 기간입니다.|

### <a name="why-would-i-want-to-use-metrics"></a>메트릭을 사용 하려고 하는 이유 

미디어 서비스 메트릭을 모니터링 하는 방법을 응용 프로그램을 수행 하는 방법을 이해의 예는 다음과 같습니다. Media Services 메트릭을 사용 하 여 해결할 수 있는 몇 가지 질문 다음과 같습니다.

* 제한을 초과한 않는 경우 때 알아야 할 내 표준 스트리밍 끝점을 모니터링 하려면 어떻게 해야 하나요?
* 충분 한 프리미엄 스트리밍 끝점 크기 조정 단위가 있는지 어떻게 알 수 있나요? 
* 알고 내 스트리밍 끝점을 강화 하는 경우 경고를 설정 하려면 어떻게 해야 하나요?
* 계정에 구성 된 최대 송신에 도달한 경우 알고 있어야 하는 경고를 설정 하려면 어떻게 해야 할까요?
* 실패 한 요청에 대 한 분석을 볼 수는 방법 및 실패의 원인이 무엇 인지?
* 어떻게 packager에서 끌어온는 DASH 또는 HLS에 대 한 몇 개의 요청을 볼 수 있습니까?
* 실패 한 요청 횟수의 임계값에 도달한 경우 경고를 설정 하려면 어떻게 해야 할까요? 

### <a name="example"></a>예제

참조 [미디어 서비스 메트릭을 모니터링 하는 방법](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Media Services에 대 한 진단 정보

진단 로그는 Azure 리소스의 작업에 대 한 풍부 하 고 빈번한 데이터를 제공합니다. 자세한 내용은 [Azure 리소스에서 로그 데이터 수집 및 소비 하는 방법을](../../azure-monitor/platform/diagnostic-logs-overview.md)합니다.

Media Services는 다음 진단 로그를 지원합니다.

* 키 배달

### <a name="key-delivery"></a>키 배달

|이름|Description|
|---|---|
|키 배달 서비스 요청|키 배달 서비스를 보여 주는 로그 정보를 요청 합니다. 자세한 내용은 참조 하세요. [스키마](media-services-diagnostic-logs-schema.md)합니다.|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>진단 로그를 사용 하려고 하는 이유 

일부 키 배달에 대 한 진단 정보를 사용 하 여 검사할 수 있는 사항은 다음과 같습니다.

* DRM 형식으로 배달 하는 라이선스 수를 참조 하세요.
* 정책에서 제공 하는 라이선스 수를 참조 하세요. 
* DRM 또는 정책 유형별로 오류를 참조 하세요.
* 클라이언트에서 무단된 라이선스 요청 수를 참조 하세요.

### <a name="example"></a>예제

참조 [미디어 서비스에 대 한 진단 정보를 모니터링 하는 방법](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>다음 단계 

* [Azure 리소스에서 로그 데이터 수집 및 소비 하는 방법](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [만들기, 보기 및 Azure Monitor를 사용 하 여 메트릭 경고 관리](../../azure-monitor/platform/alerts-metric.md)
* [미디어 서비스 메트릭을 모니터링 하는 방법](media-services-metrics-howto.md)
* [미디어 서비스에 대 한 진단 정보를 모니터링 하는 방법](media-services-diagnostic-logs-howto.md)
