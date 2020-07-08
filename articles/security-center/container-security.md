---
title: Azure Security Center의 컨테이너 보안 | Microsoft Docs
description: Azure Security Center의 컨테이너 보안 기능에 대해 알아봅니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800609"
---
# <a name="container-security-in-security-center"></a>Security Center의 컨테이너 보안

Azure Security Center는 컨테이너를 보호 하기 위한 Azure 기본 솔루션입니다. Security Center는 다음과 같은 컨테이너 리소스 유형을 보호할 수 있습니다.



|리소스 |이름  |설명  |
|:---------:|---------|---------|
|![컨테이너 호스트](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|컨테이너 호스트 (Docker를 실행 하는 가상 컴퓨터)|Security Center는 Docker 구성을 검색하고 평가된 모든 실패 규칙 목록을 제공하여 구성 오류를 표시합니다. Security Center는 이러한 문제를 신속하게 해결하고 시간을 절약하기 위한 지침을 제공합니다. Security Center는 Docker 구성을 지속적으로 평가하고 최신 상태를 제공합니다.|
|![Kubernetes 서비스](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|AKS (Azure Kubernetes Service) 클러스터|표준 계층 사용자를 위한 [Security Center의 선택적 AKS 번들](azure-kubernetes-service-integration.md) 을 사용 하 여 AKS 노드, 클라우드 트래픽 및 보안 제어를 보다 자세히 파악할 수 있습니다.|
|![컨테이너 레지스트리](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|ACR (Azure Container Registry) 레지스트리|표준 계층 사용자를 위한 [Security Center의 선택적 acr 번들](azure-kubernetes-service-integration.md) 을 사용 하 여 ARM 기반 acr 레지스트리에 있는 이미지의 취약성에 대 한 심층적인 가시성을 얻습니다.|
||||


이 문서에서는 이러한 번들을 사용 하 여 컨테이너 및 해당 앱의 보안을 개선, 모니터링 및 유지 관리 하는 방법을 설명 합니다. 컨테이너 보안의 이러한 핵심 측면을 Security Center 하는 데 도움이 되는 방법을 알아봅니다.

- [취약성 관리-컨테이너 이미지 검색](#vulnerability-management---scanning-container-images)
- [환경 강화-Docker 구성 및 Kubernetes 클러스터에 대 한 지속적인 모니터링](#environment-hardening)
- [런타임 보호-실시간 위협 감지](#run-time-protection---real-time-threat-detection)

[![Azure Security Center의 컨테이너 보안 탭](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

이러한 기능을 사용 하는 방법에 대 한 지침은 [컨테이너의 보안 모니터링](monitor-container-security.md)을 참조 하세요.

## <a name="vulnerability-management---scanning-container-images"></a>취약성 관리-컨테이너 이미지 검색
ARM 기반 Azure Container Registry를 모니터링 하려면 Security Center의 표준 계층 ( [가격 책정](/azure/security-center/security-center-pricing)참조)을 사용 하 고 있는지 확인 하세요. 그런 다음 선택적 컨테이너 레지스트리 번들을 사용 하도록 설정 합니다. 새 이미지가 푸시되 면 업계 최고의 취약점 검색 공급 업체 Qualys에서 스캐너를 사용 하 여 이미지를 검색 Security Center.

Qualys 또는 Security Center에서 문제가 발견 되 면 Security Center 대시보드의 알림이 표시 됩니다. 모든 취약점에 대해 Security Center는 심각도 분류 및 문제 해결 방법에 대 한 지침과 함께 조치 가능한 권장 사항을 제공 합니다. 컨테이너에 대 한 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항에 대 한 [참조 목록을](recommendations-reference.md#recs-containers)참조 하세요.

Security Center는 스캐너에서 검색 결과를 필터링 하 고 분류 합니다. 이미지가 정상 상태 이면 Security Center 표시 합니다. Security Center는 문제를 해결 해야 하는 이미지에 대해서만 보안 권장 사항을 생성 합니다. 문제가 발생 하는 경우에만 Security Center 하면 원치 않는 정보 알림이 발생할 가능성이 줄어듭니다.

## <a name="environment-hardening"></a>환경 강화

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 구성의 지속적인 모니터링
Azure Security Center IaaS Linux Vm 또는 Docker 컨테이너를 실행 하는 다른 Linux 컴퓨터에서 호스트 되는 관리 되지 않는 컨테이너를 식별 합니다. Security Center은 이러한 컨테이너의 구성을 지속적으로 평가 합니다. 그런 다음 이러한 항목을 [CIS (Internet Security) Docker 벤치 마크](https://www.cisecurity.org/benchmark/docker/)와 비교 합니다.

Security Center에는 CIS Docker 벤치 마크의 전체 규칙 집합이 포함 되며 컨테이너가 컨트롤을 충족 하지 않는 경우 경고를 표시 합니다. 구성이 잘못 되 면 Security Center에서 보안 권장 사항을 생성 합니다. 권장 사항 **페이지** 를 사용 하 여 권장 사항을 확인 하 고 문제를 해결 합니다. 또한 Docker를 사용 하 여 배포 된 모든 가상 머신을 표시 하는 **컨테이너** 탭에서 권장 사항을 확인할 수 있습니다. 

이 기능에 대해 나타날 수 있는 관련 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하십시오.

VM의 보안 문제를 탐색 하는 경우 Security Center는 컴퓨터의 컨테이너에 대 한 추가 정보를 제공 합니다. 이러한 정보에는 호스트에서 실행 되는 Docker 버전 및 이미지 수가 포함 됩니다. 

>[!NOTE]
> 이러한 CIS 벤치 마크 확인은 AKS 관리 되는 인스턴스 또는 Databricks 관리 Vm에서 실행 되지 않습니다.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes 클러스터에 대 한 지속적인 모니터링
Security Center는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 컨테이너 오케스트레이션 서비스인 AKS (Azure Kubernetes Service)와 함께 작동 합니다.

AKS는 클러스터의 보안 상태에 대 한 보안 제어 및 가시성을 제공 합니다. Security Center는 다음과 같은 기능을 사용 합니다.
* AKS 클러스터의 구성을 지속적으로 모니터링
* 업계 표준에 맞춘 보안 권장 구성 생성

이 기능에 대해 나타날 수 있는 관련 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하십시오.

## <a name="run-time-protection---real-time-threat-detection"></a>런타임 보호-실시간 위협 감지

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>다음 단계

이 개요에서는 Azure Security Center의 컨테이너 보안 핵심 요소에 대해 알아보았습니다. [컨테이너의 보안을 모니터링 하는 방법을](monitor-container-security.md)계속 진행 합니다.
> [!div class="nextstepaction"]
> [컨테이너의 보안 모니터링](monitor-container-security.md)