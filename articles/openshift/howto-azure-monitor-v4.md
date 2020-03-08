---
title: Azure Red Hat OpenShift 4.3에 대 한 Azure Monitor 통합
description: Red Hat OpenShift 클러스터 Microsoft Azure에서 Azure Monitor를 사용 하도록 설정 하는 방법을 알아봅니다.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: 1a372ef12ef7b0afb06ed3c540daaee5e38c3374
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78899166"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Red Hat OpenShift 4.3에 대 한 Azure Monitor 통합

> [!IMPORTANT]
> Azure Red Hat OpenShift 4.3는 미리 보기로 제공 됩니다. 미리 보기 기능은 셀프 서비스 이며 제공 되는 그대로 제공 되며 서비스 수준 계약 (SLA) 및 제한 된 보증에서 제외 됩니다. 따라서이 기능은 프로덕션 용도로 사용 되지 않습니다. 

이 문서에서는 온-프레미스 또는 모든 클라우드 환경에서 호스트 되는 OpenShift 4.3 클러스터의 컨테이너에 대 한 Azure Monitor 비공개 미리 보기를 사용 하도록 설정 하는 방법을 설명 합니다. Azure Red Hat OpenShift (ARO) 4.3 클러스터에 대해 모니터링을 사용 하도록 설정 하는 경우에도 동일한 지침이 적용 됩니다.  

## <a name="prerequisites"></a>사전 요구 사항

- [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [투구 3](https://helm.sh/docs/intro/install/)
- Kubernetes 클러스터의 kubeconfig에 대 한 액세스
- Azure 구독에 액세스
- OpenShift 4.3 클러스터에 액세스 하 여 컨테이너에 대 한 Azure Monitor를 설치 합니다. 투구 차트
- Azure 구독에 대 한 최소 참가자 RBAC 역할 권한  
- 모니터링 에이전트를 사용 하려면 다음 아웃 바운드 포트 및 도메인에서 모니터링 데이터를 Azure Monitor 백 엔드 (프록시/방화벽에 의해 차단 된 경우)로 보내야 합니다.
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>온보딩

> [!TIP]
> 이 스크립트는 bash 4 기능을 사용 하므로 bash가 최신 상태 인지 확인 합니다. `bash --version`를 사용 하 여 현재 버전을 확인할 수 있습니다.

### <a name="download-the-onboarding-script"></a>온 보 딩 스크립트 다운로드

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

AzureSubscriptionId, 작업 영역 영역, clusterName 및 Kubernetes 클러스터의 컨텍스트를 사용 하 여 다음 스크립트를 실행 합니다.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

다음은 그 예입니다.

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

기본적으로 모니터링 에이전트는 kube를 제외한 모든 네임 스페이스에서 실행 중인 모든 컨테이너의 {stdout; stderr} 컨테이너 로그를 수집 합니다.  특정 네임 스페이스 또는 네임 스페이스와 관련 된 컨테이너 로그 컬렉션을 구성 하려는 경우 [Container Insights 에이전트 구성](../azure-monitor/insights/container-insights-agent-config.md)을 참조할 수 있습니다. 여기서는 구성 맵을 사용 하 여 원하는 데이터 수집 설정으로 모니터링 에이전트를 구성할 수 있습니다.

## <a name="configure-scraping-of-prometheus-metrics"></a>스크랩의 프로메테우스 구성

컨테이너 Azure Monitor는 프로메테우스 메트릭을 스크랩 하 고 Azure Monitor 백 엔드에 수집 합니다. 프로메테우스 스크랩을 구성 하는 방법에 대 한 지침은 [컨테이너 Insights 프로메테우스 구성](../azure-monitor/insights/container-insights-prometheus-integration.md) 을 참조 하세요.

성공적으로 등록 되 면 [하이브리드 모니터링](https://aka.ms/azmon-containers-hybrid) 으로 이동 하 여 환경을 **"모두"** 로 선택 하 여 새로 등록 openshift v4 클러스터를 확인 합니다.

## <a name="disable-monitoring"></a>모니터링 사용 안 함

모니터링을 사용 하지 않도록 설정 하려면 다음 명령을 사용 하 여 컨테이너 수집에 대 한 Azure Monitor를 삭제 하 여 컨테이너 백 엔드에 대 한 Azure Monitor 데이터 수집 및 모니터링을 중지 합니다.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>업데이트 모니터링

최신 투구 차트로 업데이트 하려면 동일한 매개 변수를 사용 하 여 [온 보 딩](#onboarding) 섹션에 설명 된 대로 등록 스크립트를 다시 실행 합니다.

## <a name="after-successful-onboarding"></a>등록 성공 후

[하이브리드 모니터링](https://aka.ms/azmon-containers-hybrid)으로 이동 하면 모니터링 되는 **클러스터** 탭에서 상태를 사용 하 여 새로 활성화 된 openshift/ARO v4 클러스터를 볼 수 있습니다. 여기서는 **클러스터** 열을 클릭 하 여 메트릭, 인벤토리 및 로그와 같은 보다 심층적인 정보를 얻을 수 있습니다.

## <a name="supported-features"></a>지원되는 기능

지원 되는 기능 및 기능에 대 한 자세한 내용은 [Container Insights 개요](../azure-monitor/insights/container-insights-overview.md)를 참조 하세요.

사용자 의견 및 질문에 대해서는 askcoin@microsoft.com을 통해 문의해 주세요.

## <a name="next-steps"></a>다음 단계

모니터링에 대 한 자세한 내용은 다음을 참조 하세요.
- [컨테이너 Insights 개요](../azure-monitor/insights/container-insights-overview.md)

- [로그 쿼리 개요](../azure-monitor/log-query/log-query-overview.md)
