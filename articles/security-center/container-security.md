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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: cd9907d041562d23bc487de319a17d36b68db6f3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916120"
---
# <a name="container-security-in-security-center"></a>Security Center의 컨테이너 보안

Azure Security Center는 컨테이너 보안을 위한 Azure 기본 솔루션입니다. 또한 Security Center은 클라우드 워크 로드, Vm, 서버 및 컨테이너의 보안을 위한 투명 효과의 단일 창입니다.

이 문서에서는 Security Center 하 여 컨테이너 및 해당 앱의 보안을 개선, 모니터링 및 유지 관리 하는 방법을 설명 합니다. 컨테이너 보안의 이러한 핵심 측면을 Security Center 하는 데 도움이 되는 방법을 알아봅니다.

* 취약점 관리
* 컨테이너 환경의 강화
* 런타임 보호

[![Azure Security Center의 컨테이너 보안 탭](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

이러한 기능을 사용 하는 방법에 대 한 지침은 [컨테이너의 보안 모니터링](monitor-container-security.md)을 참조 하세요.

## <a name="vulnerability-management---scanning-container-images-preview"></a>취약성 관리-컨테이너 이미지 검색 (미리 보기)
ARM 기반 Azure Container Registry를 모니터링 하려면 Security Center의 표준 계층 ( [가격 책정](/azure/security-center/security-center-pricing)참조)을 사용 하 고 있는지 확인 하세요. 그런 다음 선택적 컨테이너 레지스트리 번들을 사용 하도록 설정 합니다. 새 이미지가 푸시되 면 업계 최고의 취약점 검색 공급 업체 Qualys에서 스캐너를 사용 하 여 이미지를 검색 Security Center.

Qualys 또는 Security Center에서 문제가 발견 되 면 Security Center 대시보드의 알림이 표시 됩니다. 모든 취약점에 대해 Security Center는 심각도 분류 및 문제 해결 방법에 대 한 지침과 함께 조치 가능한 권장 사항을 제공 합니다. 컨테이너에 대 한 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항에 대 한 [참조 목록을](recommendations-reference.md#recs-containers)참조 하세요.

## <a name="environment-hardening"></a>환경 강화

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 구성의 지속적인 모니터링
Azure Security Center IaaS Linux Vm 또는 Docker 컨테이너를 실행 하는 다른 Linux 컴퓨터에서 호스트 되는 관리 되지 않는 컨테이너를 식별 합니다. Security Center은 이러한 컨테이너의 구성을 지속적으로 평가 합니다. 그런 다음 이러한 항목을 [CIS (Internet Security) Docker 벤치 마크](https://www.cisecurity.org/benchmark/docker/)와 비교 합니다.

Security Center에는 CIS Docker 벤치 마크의 전체 규칙 집합이 포함 되며 컨테이너가 컨트롤을 충족 하지 않는 경우 경고를 표시 합니다. 구성이 잘못 되 면 Security Center에서 보안 권장 사항을 생성 합니다. 권장 사항 **페이지** 를 사용 하 여 권장 사항을 확인 하 고 문제를 해결 합니다. 또한 Docker를 사용 하 여 배포 된 모든 가상 머신을 표시 하는 **컨테이너** 탭에서 권장 사항을 확인할 수 있습니다. 

이 기능에 대해 나타날 수 있는 관련 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하십시오.

VM의 보안 문제를 탐색 하는 경우 Security Center는 컴퓨터의 컨테이너에 대 한 추가 정보를 제공 합니다. 이러한 정보에는 호스트에서 실행 되는 Docker 버전 및 이미지 수가 포함 됩니다. 

>[!NOTE]
> 이러한 CIS 벤치 마크 확인은 AKS 관리 되는 인스턴스 또는 Databricks 관리 Vm에서 실행 되지 않습니다.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kubernetes 클러스터에 대 한 지속적인 모니터링 (미리 보기)
Security Center는 컨테이너 화 된 응용 프로그램 개발, 배포 및 관리를 위한 Microsoft의 관리 되는 컨테이너 오케스트레이션 서비스인 AKS (Azure Kubernetes Service)와 함께 작동 합니다.

AKS는 클러스터의 보안 상태에 대 한 보안 제어 및 가시성을 제공 합니다. Security Center는 다음과 같은 기능을 사용 합니다.
* AKS 클러스터의 구성을 지속적으로 모니터링
* 업계 표준에 맞춘 보안 권장 구성 생성

이 기능에 대해 나타날 수 있는 관련 Security Center 권장 사항에 대 한 자세한 내용은 권장 사항 참조 표의 [컨테이너 섹션](recommendations-reference.md#recs-containers) 을 참조 하십시오.

## <a name="run-time-protection---real-time-threat-protection"></a>런타임 보호-실시간 위협 방지

Security Center는 컨테이너 화 된 환경에 대 한 실시간 위협 방지 기능을 제공 하 고 의심 스러운 활동에 대 한 경고를 생성 합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

호스트 및 AKS 클러스터 수준에서 위협을 검색 합니다. 자세한 내용은 [Azure 컨테이너에 대 한 위협 방지](threat-protection.md#azure-containers)를 참조 하세요.


## <a name="container-security-faq"></a>컨테이너 보안 FAQ

### <a name="what-types-of-images-can-azure-security-center-scan"></a>스캔할 수 있는 이미지 종류는 무엇 Azure Security Center?
Security Center 셸 액세스를 제공 하는 Linux OS 기반 이미지를 검색 합니다. 

Qualys 스캐너는 [Docker 스크래치](https://hub.docker.com/_/scratch/) 이미지와 같은 슈퍼 전적 이미지 또는 응용 프로그램 및 해당 런타임 종속성만 포함 하는 "Distroless" 이미지 (패키지 관리자, 셸 또는 OS 없음)를 지원 하지 않습니다.

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>이미지를 검색 Azure Security Center 검색 하는 방법
레지스트리에서 이미지를 추출 합니다. 그런 다음 알려진 취약점 목록을 추출 하는 Qualys 스캐너가 포함 된 격리 된 샌드박스에서 실행 됩니다.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>에서 이미지를 얼마나 자주 Azure Security Center 검색 하나요?
이미지 검색은 푸시 마다 트리거됩니다.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API를 통해 검사 결과를 가져올 수 있나요?
예. 결과는 [하위 평가 REST API](/rest/api/securitycenter/subassessments/list/)에 있습니다. 또한 모든 리소스에 대 한 Kusto like API 인 ARG (Azure 리소스 그래프)를 사용할 수 있습니다. 쿼리는 특정 검색을 페치할 수 있습니다.
 

## <a name="next-steps"></a>다음 단계

Azure Security Center의 컨테이너 보안에 대해 자세히 알아보려면 다음 관련 문서를 참조 하세요.

* 컨테이너 관련 리소스의 보안 상태를 보려면 [컴퓨터 및 응용 프로그램 보호](security-center-virtual-machine-protection.md#containers)의 컨테이너 섹션을 참조 하세요.

* [Azure Kubernetes Service와의 통합](azure-kubernetes-service-integration.md) 에 대 한 세부 정보

* [Azure Container Registry와의 통합](azure-container-registry-integration.md) 에 대 한 세부 정보