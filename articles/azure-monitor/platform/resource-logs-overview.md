---
title: Azure 리소스 로그 개요 | Microsoft Docs
description: Azure 리소스 로그에 대해 지원 되는 서비스 및 이벤트 스키마를 이해 합니다.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 10/22/2019
ms.openlocfilehash: e1bf6a55568671ddb8f6999356cc9be43ce6a728
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72804016"
---
# <a name="azure-resource-logs-overview"></a>Azure 리소스 로그 개요
Azure 리소스 로그는 내부 작업을 설명 하는 Azure 리소스에서 내보낸 [플랫폼 로그](platform-logs-overview.md) 입니다. 모든 리소스 로그는 일반적인 최상위 스키마를 각 서비스의 유연성과 공유 하 여 고유한 이벤트의 고유한 속성을 내보냅니다.

> [!NOTE]
> 이전에는 리소스 로그를 진단 로그 라고 했습니다.

## <a name="collecting-resource-logs"></a>리소스 로그 수집
리소스 로그는 지원 되는 Azure 리소스에 의해 자동으로 생성 되지만 [진단 설정을](diagnostic-settings.md)사용 하 여 구성 하지 않는 한 수집 되지 않습니다. 각 Azure 리소스에 대 한 진단 설정을 만들어 로그를 다음 대상으로 전달 합니다.

| 대상 | 시나리오 |
|:---|:---|:---|
| [Log Analytics 작업 영역](resource-logs-collect-storage.md) | 다른 모니터링 데이터를 사용 하 여 로그를 분석 하 고 로그 쿼리 및 로그 경고와 같은 Azure Monitor 기능을 활용 합니다. |
| [Azure Storage](archive-diagnostic-logs.md) | 감사 또는 백업용 로그를 보관 합니다. |
| [이벤트 허브](resource-logs-stream-event-hubs.md) | 로그를 타사 로깅 및 원격 분석 시스템으로 스트리밍합니다.  |

## <a name="compute-resources"></a>Compute 리소스
리소스 로그는 Azure compute 리소스의 게스트 OS 수준 로그와 다릅니다. 계산 리소스에는 이벤트 로그, syslog 및 성능 카운터와 같은 데이터를 포함 하 여 게스트 OS에서 로그 및 메트릭을 수집 하는 에이전트가 필요 합니다. [진단 확장](agents-overview.md#azure-diagnostic-extension) 을 사용 하 여 azure 가상 머신에서 로그 데이터를 라우팅하고 [Log Analytics 에이전트](agents-overview.md#log-analytics-agent) 를 사용 하 여 azure의 가상 머신, 다른 클라우드 및 온-프레미스에서 Log Analytics 작업 영역으로 로그 및 메트릭을 수집 합니다. 자세한 내용은 [Azure Monitor의 모니터링 데이터 원본](data-sources.md) 을 참조 하세요.

## <a name="resource-logs-schema"></a>리소스 로그 스키마
리소스 로그 스키마 및 범주에 대 한 자세한 내용은 [리소스 로그 스키마](diagnostic-logs-schema.md)를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* Azure를 모니터링 하는 데 사용할 수 있는 [다른 azure platform 로그에 대해 자세히 알아보세요](platform-logs-overview.md) .
