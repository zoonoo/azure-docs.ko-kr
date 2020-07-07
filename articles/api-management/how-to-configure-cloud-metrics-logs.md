---
title: Azure API Management 자체 호스팅 게이트웨이에 대 한 클라우드 메트릭 및 로그 구성 | Microsoft Docs
description: Azure API Management 자체 호스팅 게이트웨이의 클라우드 메트릭 및 로그를 구성 하는 방법에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: d0fbdcb877e91a703306f15fdc7507fd19d534f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205132"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Azure API Management 자체 호스팅 게이트웨이에 대 한 클라우드 메트릭 및 로그 구성

이 문서에서는 [자체 호스팅 게이트웨이의](./self-hosted-gateway-overview.md)클라우드 메트릭과 로그를 구성 하는 방법에 대 한 세부 정보를 제공 합니다.

자체 호스팅 게이트웨이는 API management 서비스와 연결 되어야 하며, 포트 443의 Azure에 대 한 아웃 바운드 TCP/IP 연결이 필요 합니다. 게이트웨이는 아웃 바운드 연결을 활용 하 여 원격 분석을 수행 하도록 구성 된 경우 Azure에 원격 분석을 보냅니다. 

## <a name="metrics"></a>메트릭
기본적으로 자체 호스팅 게이트웨이는 [클라우드에서](api-management-howto-use-azure-monitor.md)관리 되는 게이트웨이와 동일한 [Azure Monitor](https://azure.microsoft.com/services/monitor/)를 통해 여러 메트릭을 내보냅니다. 

`telemetry.metrics.cloud`게이트웨이 배포의 ConfigMap에서 키를 사용 하 여이 기능을 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 다음은 사용 가능한 구성에 대 한 분석입니다.

| 필드  | 기본값 | Description |
| ------------- | ------------- | ------------- |
| 원격 분석. 메트릭 클라우드  | `true` | Azure Monitor를 통해 로깅을 사용 하도록 설정 합니다. 값은, 일 수 있습니다 `true` `false` . |


다음은 샘플 구성입니다.

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

자체 호스팅 게이트웨이는 현재 Azure Monitor를 통해 다음 메트릭을 내보냅니다.

| 메트릭  | Description |
| ------------- | ------------- |
| 요청  | 해당 기간의 API 요청 수 |
| 게이트웨이 요청 기간 | 게이트웨이에서 요청을 수신한 순간부터 응답이 완전히 전송될 때까지 걸린 시간(밀리초) |
| 백 엔드 요청 기간 | 전체 백 엔드 IO(연결, 바이트 송신 및 수신)에 소요된 시간(밀리초)  |

## <a name="logs"></a>로그

자체 호스팅 게이트웨이에서는 현재 클라우드로 [진단 로그](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) 를 보내지 않습니다. 그러나 자체 호스팅 게이트웨이가 배포 되는 위치에서 [로그를 로컬로 구성 하 고 유지할](how-to-configure-local-metrics-logs.md) 수 있습니다. 

게이트웨이가 [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)에 배포 되는 경우 컨테이너에서 로그를 수집 하 고 Log Analytics에서 볼 수 있도록 [컨테이너에 대해 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) 를 사용 하도록 설정할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대해 자세히 알아보려면 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md) 를 참조 하세요.
* [로컬에서 로그를 구성 하 고 유지 하](how-to-configure-local-metrics-logs.md) 는 방법 알아보기


