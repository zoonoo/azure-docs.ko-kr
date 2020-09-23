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
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: cbea99b08ed0cbcc1125d15c78119a509b6dcc48
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90977254"
---
# <a name="container-security-in-security-center"></a>Security Center의 컨테이너 보안

Azure Security Center는 컨테이너를 보호 하기 위한 Azure 기본 솔루션입니다.

Security Center는 다음과 같은 컨테이너 리소스 유형을 보호할 수 있습니다.

| 리소스 유형 | Security Center에서 제공 하는 보호 |
|:--------------------:|-----------|
| ![Kubernetes 서비스](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**AKS (Azure Kubernetes Service) 클러스터** | -잘못 된 구성에 대 한 가시성을 제공 하 고 검색 된 문제를 해결 하는 데 도움이 되는 지침을 제공 하는 AKS 클러스터 구성의 지속적인 평가.<br>[보안 권장 사항을 통한 환경 강화에 대해 자세히 알아보세요](#environment-hardening).<br><br>-AKS 클러스터 및 Linux 노드에 대 한 위협 방지 의심 스러운 활동에 대 한 경고는 선택적  [Azure Defender For Kubernetes](defender-for-kubernetes-introduction.md)에서 제공 합니다.<br>[AKS 노드 및 클러스터에 대 한 런타임 보호에 대해 자세히 알아보세요](#run-time-protection-for-aks-nodes-and-clusters).|
| ![컨테이너 호스트](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**컨테이너 호스트**<br>(Docker를 실행 하는 Vm) | -잘못 된 구성에 대 한 가시성을 제공 하는 Docker 구성의 지속적인 평가 및  [서버에 대 한 선택적 Azure Defender](defender-for-servers-introduction.md)에서 발견 된 문제를 해결 하는 데 도움이 되는 지침입니다.<br>[보안 권장 사항을 통한 환경 강화에 대해 자세히 알아보세요](#environment-hardening).|
| ![컨테이너 레지스트리](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**ACR (Azure Container Registry) 레지스트리** | - [컨테이너 레지스트리 용 선택적 Azure Defender](defender-for-container-registries-introduction.md)를 사용 하 여 AZURE RESOURCE MANAGER 기반 ACR 레지스트리에 있는 이미지에 대 한 취약성 평가 및 관리 도구입니다.<br>[취약성에 대 한 컨테이너 이미지를 검색 하는 방법에 대해 자세히 알아보세요](#vulnerability-management---scanning-container-images). |
|||

이 문서에서는 컨테이너 레지스트리, 서버 및 Kubernetes에 대 한 선택적 Azure Defender 계획과 함께 Security Center를 사용 하 여 컨테이너 및 해당 앱의 보안을 개선 하 고 모니터링 하 고 유지 관리 하는 방법을 설명 합니다.

컨테이너 보안의 이러한 핵심 측면을 Security Center 하는 데 도움이 되는 방법을 알아봅니다.

- [취약성 관리-컨테이너 이미지 검색](#vulnerability-management---scanning-container-images)
- [환경 강화](#environment-hardening)
- [AKS 노드 및 클러스터에 대 한 런타임 보호](#run-time-protection-for-aks-nodes-and-clusters)

다음 스크린샷에서는 자산 인벤토리 페이지와 Security Center로 보호 되는 다양 한 컨테이너 리소스 유형을 보여 줍니다.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Security Center의 자산 인벤토리 페이지의 컨테이너 관련 리소스" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>취약성 관리-컨테이너 이미지 검색

Azure Resource Manager 기반 Azure 컨테이너 레지스트리에서 이미지를 모니터링 하려면 [컨테이너 레지스트리에 대해 Azure Defender](defender-for-container-registries-introduction.md)를 사용 하도록 설정 합니다. Security Center는 지난 30 일 이내에 끌어온 이미지를 검색 하거나, 레지스트리에 푸시 하거나, 가져왔습니다. 통합 스캐너는 업계 최고의 취약성 검색 공급 업체 (Qualys)에서 제공 합니다.

Qualys 또는 Security Center에서 문제가 발견 되 면 [Azure Defender 대시보드에](azure-defender-dashboard.md)대 한 알림이 표시 됩니다. 모든 취약점에 대해 Security Center는 심각도 분류 및 문제 해결 방법에 대 한 지침과 함께 조치 가능한 권장 사항을 제공 합니다. 컨테이너에 대 한 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항에 대 한 [참조 목록을](recommendations-reference.md#recs-containers)참조 하세요.

Security Center는 스캐너에서 검색 결과를 필터링 하 고 분류 합니다. 이미지가 정상 상태 이면 Security Center 표시 합니다. Security Center는 문제를 해결 해야 하는 이미지에 대해서만 보안 권장 사항을 생성 합니다. 문제가 발생 하는 경우에만 Security Center 하면 원치 않는 정보 알림이 발생할 가능성이 줄어듭니다.

## <a name="environment-hardening"></a>환경 강화

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 구성의 지속적인 모니터링

Azure Security Center IaaS Linux Vm 또는 Docker 컨테이너를 실행 하는 다른 Linux 컴퓨터에서 호스트 되는 관리 되지 않는 컨테이너를 식별 합니다. Security Center은 이러한 컨테이너의 구성을 지속적으로 평가 합니다. 그런 다음 이러한 항목을 [CIS (Internet Security) Docker 벤치 마크](https://www.cisecurity.org/benchmark/docker/)와 비교 합니다.

Security Center에는 CIS Docker 벤치 마크의 전체 규칙 집합이 포함 되며 컨테이너가 컨트롤을 충족 하지 않는 경우 경고를 표시 합니다. 구성이 잘못 되 면 Security Center에서 보안 권장 사항을 생성 합니다. 권장 사항 **페이지** 를 사용 하 여 권장 사항을 확인 하 고 문제를 해결 합니다. 또한 Docker를 사용 하 여 배포 된 모든 가상 머신을 표시 하는 **컨테이너** 탭에서 권장 사항을 확인할 수 있습니다. CIS 벤치 마크 확인은 AKS 관리 되는 인스턴스 또는 Databricks 관리 Vm에서 실행 되지 않습니다.

이 기능에 대해 나타날 수 있는 관련 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하십시오.

VM의 보안 문제를 탐색 하는 경우 Security Center는 컴퓨터의 컨테이너에 대 한 추가 정보를 제공 합니다. 이러한 정보에는 호스트에서 실행 되는 Docker 버전 및 이미지 수가 포함 됩니다. 

IaaS Linux Vm에서 호스팅되는 관리 되지 않는 컨테이너를 모니터링 하려면 [서버에 대 한 선택적 Azure Defender](defender-for-servers-introduction.md)를 사용 하도록 설정 합니다.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes 클러스터에 대 한 지속적인 모니터링
Security Center는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 컨테이너 오케스트레이션 서비스인 AKS (Azure Kubernetes Service)와 함께 작동 합니다.

AKS는 클러스터의 보안 상태에 대 한 보안 제어 및 가시성을 제공 합니다. Security Center는 다음과 같은 기능을 사용 합니다.
* AKS 클러스터의 구성을 지속적으로 모니터링
* 업계 표준에 맞춘 보안 권장 구성 생성

이 기능에 대해 나타날 수 있는 관련 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하십시오.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Kubernetes 허용 제어를 사용 하는 워크 로드 보호 모범 사례

**Kubernetes에 대 한 추가 기능을 Azure Policy** 설치 하 여 Kubernetes 컨테이너의 워크 로드를 보호 하기 위한 권장 구성 번들을 가져옵니다.

[Kubernetes에 대 한이 Azure Policy 페이지](../governance/policy/concepts/policy-for-kubernetes.md)에서 설명 하는 것 처럼 추가 기능은 Open Policy Agent에 대 한 오픈 소스 [게이트 키퍼 v3](https://github.com/open-policy-agent/gatekeeper)   허용 컨트롤러 webhook를 확장 합니다. [Open Policy Agent](https://www.openpolicyagent.org/) Kubernetes 허용 컨트롤러는 클러스터를 사용 하는 방법을 강제 적용 하는 플러그 인입니다. 추가 기능은 Kubernetes 허용 제어에 웹 후크로 등록 되며, 중앙의 일관 된 방식으로 클러스터에 규모 사항을 및 보호 기능을 적용할 수 있습니다. 

AKS 클러스터에 추가 기능을 설치한 경우 Kubernetes API 서버에 대 한 모든 요청은 클러스터에 유지 되기 전에 미리 정의 된 모범 사례 집합에 대해 모니터링 됩니다. 그런 다음 모범 사례를 **적용** 하 고 향후 작업을 위해이를 요구 하도록를 구성할 수 있습니다. 

예를 들어, 권한 있는 컨테이너를 만들지 않아야 하 고 이후의 모든 요청이 차단 되도록 할 수 있습니다.

[Kubernetes 워크 로드를 보호 하](kubernetes-workload-protections.md)는 방법에 대해 자세히 알아보세요.


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>AKS 노드 및 클러스터에 대 한 런타임 보호

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>다음 단계

이 개요에서는 Azure Security Center의 컨테이너 보안 핵심 요소에 대해 알아보았습니다. 관련 자료는 다음을 참조 하세요.

- [Azure Defender for Kubernetes 소개](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리에 대 한 Azure Defender 소개](defender-for-container-registries-introduction.md)