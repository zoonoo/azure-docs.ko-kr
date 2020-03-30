---
title: Azure Red Hat 오픈시프트 4.3에 대한 Azure 모니터 통합
description: Microsoft Azure Red Hat OpenShift 클러스터에서 Azure 모니터를 사용하도록 설정하는 방법에 대해 알아봅니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082849"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Red Hat 오픈시프트 4.3에 대한 Azure 모니터 통합

> [!IMPORTANT] 
> Azure Red Hat OpenShift 4.3은 현재 미국 동부의 비공개 미리 보기에서만 사용할 수 있습니다. 비공개 미리 보기 수락은 초대에 의해서만 가능합니다. 이 기능을 활성화하기 전에 구독을 등록하십시오: [Azure Red Hat OpenShift 개인 미리 보기 등록](https://aka.ms/aro-preview-register)

> [!NOTE]
> 미리 보기 기능은 셀프 서비스이며 현재 제공된 대로 제공되며 서비스 수준 계약(SLA) 및 제한 보증에서 제외됩니다. 따라서 이 기능은 프로덕션용이 아닙니다.

이 문서에서는 Prem 또는 모든 클라우드 환경에서 호스팅되는 OpenShift 4.3 클러스터에 대한 컨테이너에 대한 Azure Monitor의 개인 미리 보기를 활성화하는 방법을 설명합니다. Azure Red Hat OpenShift(ARO) 4.3 클러스터에 대한 모니터링을 활성화하는 데도 동일한 지침이 적용됩니다.  

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [투구 3](https://helm.sh/docs/intro/install/)
- 큐베네츠 클러스터의 쿠베콘피그 이용
- Azure 구독에 액세스
- OpenShift 4.3 클러스터에 액세스하여 컨테이너 헬름 차트에 대한 Azure 모니터 설치
- Azure 구독에 대한 최소 기여자 RBAC 역할 권한  
- 모니터링 에이전트에는 다음과 같은 아웃바운드 포트와 도메인이 Azure Monitor 백 엔드로 모니터링 데이터를 보내야 합니다(프록시/방화벽에 의해 차단된 경우).
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>온보딩

> [!TIP]
> 스크립트는 bash 4 기능을 사용하므로 bash가 최신 상태인지 확인하십시오. 을 통해 현재 버전을 `bash --version`확인할 수 있습니다.

### <a name="download-the-onboarding-script"></a>온보딩 스크립트 다운로드

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

azureSubscriptionId, 작업 영역, 클러스터 이름 및 Kubernetes 클러스터의 컨텍스트를 통해 다음 스크립트를 실행합니다.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

예를 들어:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

기본적으로 모니터링 에이전트는 kube 시스템을 제외한 모든 네임스페이스에서 실행되는 모든 컨테이너의 {stdout; stderr} 컨테이너 로그를 수집합니다.  특정 네임스페이스 또는 네임스페이스에 특정한 컨테이너 로그 컬렉션을 구성하려는 경우 [컨테이너 Insights 에이전트 구성을](../azure-monitor/insights/container-insights-agent-config.md)참조할 수 있습니다. 여기서 는 구성 맵을 사용하여 원하는 데이터 수집 설정으로 모니터링 에이전트를 구성할 수 있습니다.

## <a name="configure-scraping-of-prometheus-metrics"></a>프로메테우스 메트릭스 스크랩 구성

컨테이너에 대한 Azure 모니터는 Prometheus 메트릭을 스크랩하고 Azure 모니터 백 엔드에 인제됩니다. 프로메테우스 스크래핑을 구성하는 방법에 대한 지침은 [컨테이너 인사이트 프로메테우스 구성을](../azure-monitor/insights/container-insights-prometheus-integration.md) 참조하십시오.

온보딩에 성공한 후 [하이브리드 모니터링으로](https://aka.ms/azmon-containers-hybrid) 이동하여 **환경을 "모두"로** 선택하여 새로 온보딩된 OpenShift v4 클러스터를 봅니다.

## <a name="disable-monitoring"></a>모니터링 사용 안 함

모니터링을 사용하지 않으려면 다음 명령을 사용하여 컨테이너 헬름용 Azure Monitor 차트를 삭제하여 컨테이너 백엔드에 대한 모니터링 데이터 수집 및 수집을 중지할 수 있습니다.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>업데이트 모니터링

동일한 매개변수를 사용하여 [온보딩](#onboarding) 섹션에 설명된 대로 온보딩 스크립트를 다시 실행하여 최신 Helm 차트로 업데이트합니다.

## <a name="after-successful-onboarding"></a>성공적인 온보딩 후

하이브리드 [모니터링으로](https://aka.ms/azmon-containers-hybrid)이동하면 **모니터링된 클러스터** 탭에서 새로 활성화된 OpenShift/ARO v4 클러스터의 상태를 확인할 수 있습니다. 클러스터 **열을** 클릭하여 메트릭, 인벤토리 및 로그와 같은 심층적인 통찰력을 얻을 수 있습니다.

## <a name="supported-features"></a>지원되는 기능

지원되는 기능 및 기능에 대한 자세한 내용은 [컨테이너 인사이트 개요를](../azure-monitor/insights/container-insights-overview.md)참조하십시오.

피드백과 askcoin@microsoft.com 질문은 통해 문의하십시오.

## <a name="next-steps"></a>다음 단계

모니터링에 대해 자세히 알아보려면 다음을 참조하십시오.
- [컨테이너 인사이트 개요](../azure-monitor/insights/container-insights-overview.md)

- [로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)
