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
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 2a40680b45522d8a8e49a3f01a50a748e1c71b53
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74274073"
---
# <a name="container-security-in-security-center"></a>Security Center의 컨테이너 보안

Azure Security Center는 컨테이너 보안을 위한 Azure 기본 솔루션입니다. 또한 Security Center은 클라우드 워크 로드, Vm, 서버 및 컨테이너의 보안을 위한 투명 효과의 단일 창입니다.

이 문서에서는 컨테이너 및 해당 앱의 보안을 개선, 모니터링 및 유지 관리 하는 방법을 설명 합니다. 컨테이너 보안의 이러한 핵심 측면을 Security Center 하는 데 도움이 되는 방법을 알아봅니다.

* 취약점 관리
* 컨테이너 환경의 강화
* 런타임 보호

[![Azure Security Center의 컨테이너 보안 탭](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>취약성 관리-컨테이너 이미지 검색 (미리 보기)
Azure Container Registry를 모니터링 하려면 Security Center의 표준 계층에 있는지 확인 합니다 ( [가격 책정](https://docs.microsoft.com/azure/security-center/security-center-pricing.md)참조). 그런 다음 선택적 컨테이너 레지스트리 번들을 사용 하도록 설정 합니다. 새 이미지가 푸시되 면 업계 최고의 취약점 검색 공급 업체 Qualys에서 스캐너를 사용 하 여 이미지를 검색 Security Center.

Qualys 또는 Security Center에서 문제가 발견 되 면 Security Center 대시보드의 알림이 표시 됩니다. 모든 취약점에 대해 Security Center는 심각도 분류 및 문제 해결 방법에 대 한 지침과 함께 조치 가능한 권장 사항을 제공 합니다. Security Center 권장 사항에 대 한 자세한 내용은 [가상 머신 보호](security-center-virtual-machine-protection.md##compute-and-app-recs)의 권장 사항 목록을 참조 하세요.

## <a name="environment-hardening"></a>환경 강화

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 구성의 지속적인 모니터링
Azure Security Center IaaS Linux Vm 또는 Docker 컨테이너를 실행 하는 다른 Linux 컴퓨터에서 호스트 되는 관리 되지 않는 컨테이너를 식별 합니다. Security Center은 이러한 컨테이너의 구성을 지속적으로 평가 합니다. 그런 다음 이러한 항목을 [CIS (Internet Security) Docker 벤치 마크의 중앙](https://www.cisecurity.org/benchmark/docker/)과 비교 합니다. 

Security Center에는 CIS Docker 벤치 마크의 전체 규칙 집합이 포함 되며 컨테이너가 컨트롤을 충족 하지 않는 경우 경고를 표시 합니다. 구성이 잘못 되 면 Security Center에서 보안 권장 사항을 생성 합니다. 권장 사항 **페이지** 를 사용 하 여 권장 사항을 확인 하 고 문제를 해결 합니다. 또한 Docker를 사용 하 여 배포 된 모든 가상 머신을 표시 하는 **컨테이너** 탭에서 권장 사항을 확인할 수 있습니다. 가상 컴퓨터의 보안 문제를 탐색 하는 경우 Security Center는 컴퓨터의 컨테이너에 대 한 추가 정보를 제공 합니다. 이러한 정보에는 호스트에서 실행 되는 Docker 버전 및 이미지 수가 포함 됩니다. 권장 사항에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)를 참조 하세요. 

>[!NOTE]
> 이러한 CIS 벤치 마크 확인은 AKS 관리 되는 인스턴스 또는 Databricks 관리 Vm에서 실행 되지 않습니다.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kubernetes 클러스터에 대 한 지속적인 모니터링 (미리 보기)
Security Center는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 컨테이너 오케스트레이션 서비스인 AKS (Azure Kubernetes Service)와 함께 작동 합니다.

AKS는 클러스터의 보안 상태에 대 한 보안 제어 및 가시성을 제공 합니다. Security Center는 다음과 같은 기능을 사용 합니다.
* AKS 클러스터의 구성을 지속적으로 모니터링
* 업계 표준에 맞춘 보안 권장 구성 생성

Security Center 권장 사항에 대 한 자세한 내용은 [가상 머신 보호](security-center-virtual-machine-protection.md)를 참조 하세요.

## <a name="run-time-protection---real-time-threat-detection"></a>런타임 보호-실시간 위협 감지

Security Center는 컨테이너 화 된 환경에 대 한 실시간 위협 감지 기능을 제공 하 고 의심 스러운 활동에 대 한 경고를 생성 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

호스트 및 AKS 클러스터 수준에서 위협을 검색 합니다. 자세한 내용은 [Azure container service](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)를 참조 하세요.


## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>컨테이너 관련 리소스의 보안 상태를 보려면
1.  Security Center의 **& 앱 계산** 페이지를 엽니다.
2.  **컨테이너** 탭을 클릭 합니다. AKS 클러스터, ACR 레지스트리 및 Docker를 실행 하는 Vm의 상태가 나타납니다.

## <a name="next-steps"></a>다음 단계

Azure Security Center의 컨테이너 보안에 대 한 자세한 내용은 다음을 참조 하세요.
* [Azure Kubernetes Service와의 통합](azure-kubernetes-service-integration.md) 에 대 한 세부 정보

* [Azure Container Registry와의 통합](azure-container-registry-integration.md) 에 대 한 세부 정보