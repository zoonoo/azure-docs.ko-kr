---
title: Azure Arc에 컨테이너용 Azure Monitor 온보딩(미리 보기)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc에 컨테이너용 Azure Monitor 온보딩
keywords: Kubernetes, Arc, Azure, K8s, 컨테이너
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680741"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Arc에 컨테이너용 Azure Monitor 온보딩(미리 보기)

[Azure Monitor 지원 컨테이너](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)를 Azure Arc 지원 Kubernetes 클러스터에 온보딩합니다.

## <a name="before-you-begin"></a>시작하기 전에

* [Kubernetes 버전](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* 클러스터(마스터 및 작업자) 노드용 Linux 배포판 – Ubuntu(18.04 LTS 및 16.04 LTS)
* Azure Arc 지원 Kubernetes 클러스터의 Azure 구독에 대한 최소 참가자 RBAC 역할 권한
* Azure Arc 지원 Kubernetes 클러스터의 정규화된 Azure 리소스 ID
* Kubernetes 클러스터의 Kubeconfig 컨텍스트
* 모니터링 에이전트는 성능 메트릭을 가져오기 위해 모든 노드에서 Kubelet의 cAdvisor가 보안 포트: 10250 또는 보안되지 않은 포트: 10255에서 실행되어야 합니다.   
* Kubelet cAdvisor 포트를 보안 포트: 10250으로 구성하는 것이 좋습니다.
* 모니터링 에이전트는 Azure Monitor 백 엔드에 모니터링 데이터를 전송하기 위해 다음 아웃바운드 포트 및 도메인이 필요합니다(프록시/방화벽으로 차단된 경우).
    -  *.ods.opinsights.azure.com 443
    -  *.oms.opinsights.azure.com 443
    -  *.blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>온보딩

### <a name="using-helm-chart"></a>HELM 차트 사용

### <a name="option-1-using-powershell--script"></a>옵션 1: PowerShell 스크립트 사용

1. 온보딩 스크립트 다운로드

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. 개발 컴퓨터에 [PowerShell core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6)를 설치하여 PowerShell 온보딩 스크립트 실행

3. Azure에 로그인

    ```console
    az login --use-device-code
    ```

4. 클러스터 Azure Arc K8s Cluster ResourceId 및 kubernetes 클러스터의 컨텍스트를 사용하여 아래 스크립트 실행

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>옵션 2: Bash 스크립트 사용

> **팁:** 이 스크립트는 bash 4 기능을 사용하므로 bash가 최신 상태인지 확인해야 합니다. `bash --version`을 사용하여 현재 버전을 확인할 수 있습니다.

1. 온보딩 스크립트 다운로드

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. 클러스터 Azure Arc K8s Cluster ResourceId 및 kubernetes 클러스터의 컨텍스트를 사용하여 아래 스크립트 실행

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>에이전트 데이터 수집 구성

기본적으로 에이전트는 kube 시스템 네임스페이스에 있는 컨테이너의 stdout 및 stderr 로그를 수집하지 않습니다.
원하는 데이터 수집 설정을 사용하여 에이전트를 구성하려면 https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config 를 참조하세요.

## <a name="configure-scraping-of-prometheus-metrics"></a>Prometheus 메트릭 스크래핑 구성

컨테이너용 Azure Monitor는 Prometheus 메트릭을 스크랩하고 Azure Monitor 백 엔드에 수집합니다.
Prometheus 스크래핑을 구성하는 방법에 대한 지침은 https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration 을 참조하세요.

## <a name="user-interface"></a>사용자 인터페이스

https://aka.ms/azmon-containers-azurearc 로 이동하여 온보딩된 클러스터를 확인합니다.

## <a name="disable-monitoring"></a>모니터링 사용 안 함

몇 가지 이유로 모니터링을 사용하지 않으려는 경우 컨테이너용 Azure Monitor HELM 차트를 삭제하기만 하면 컨테이너용 Azure Monitor로 모니터링 데이터 수집을 중단할 수 있습니다.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>다음 단계

* [Azure Policy를 사용하여 클러스터 구성 관리](./use-azure-policy.md)

