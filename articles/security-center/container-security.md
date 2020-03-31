---
title: Azure 보안 센터의 컨테이너 보안 | 마이크로 소프트 문서
description: Azure 보안 센터의 컨테이너 보안 기능에 대해 알아봅니다.
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
ms.openlocfilehash: d46e2a9820ec0c45d197f135428f1ace712b2fb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80125134"
---
# <a name="container-security-in-security-center"></a>보안 센터의 컨테이너 보안

Azure 보안 센터는 컨테이너 보안을 위한 Azure 네이티브 솔루션입니다. 보안 센터는 또한 클라우드 워크로드, VM, 서버 및 컨테이너의 보안을 위한 최적의 단일 유리 경험 창이기도 합니다.

이 문서에서는 보안 센터에서 컨테이너 및 해당 앱의 보안을 개선, 모니터링 및 유지하는 데 어떻게 도움이 되는지 설명합니다. 보안 센터가 컨테이너 보안의 이러한 핵심 측면에 어떻게 도움이 되는지 알아봅니다.

* 취약점 관리
* 컨테이너 환경 의 경화
* 런타임 보호

[![Azure 보안 센터의 컨테이너 보안 탭](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

이러한 기능을 사용하는 방법에 대한 지침은 [컨테이너의 보안 모니터링을](monitor-container-security.md)참조하십시오.

## <a name="vulnerability-management---scanning-container-images"></a>취약점 관리 - 컨테이너 이미지 검색
ARM 기반 Azure 컨테이너 레지스트리를 모니터링하려면 보안 센터의 표준 계층에 있는지 확인합니다(가격 [책정](/azure/security-center/security-center-pricing)참조). 그런 다음 선택적 컨테이너 레지스트리 번들을 활성화합니다. 새 이미지를 푸시하면 보안 센터는 업계 최고의 취약점 검색 공급업체인 Qualys의 스캐너를 사용하여 이미지를 스캔합니다.

Qualys 또는 보안 센터에서 문제가 발견되면 보안 센터 대시보드에서 알림을 받게 됩니다. 모든 취약점에 대해 보안 센터는 심각도 분류 및 문제 해결 방법에 대한 지침과 함께 실행 가능한 권장 사항을 제공합니다. 컨테이너에 대한 보안 센터의 권장 사항에 대한 자세한 내용은 [권장 사항 참조 목록을](recommendations-reference.md#recs-containers)참조하십시오.

보안 센터는 스캐너에서 결과를 필터링하고 분류합니다. 이미지가 정상이면 보안 센터에서 이미지로 표시합니다. 보안 센터는 해결해야 할 문제가 있는 이미지에 대해서만 보안 권장 사항을 생성합니다. 보안 센터는 문제가 있을 때만 알림을 통해 원치 않는 정보 경고를 줄일 수 있습니다.

## <a name="environment-hardening"></a>환경 경화

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 구성의 지속적인 모니터링
Azure 보안 센터는 IaaS Linux VM에서 호스팅되는 관리되지 않는 컨테이너 또는 Docker 컨테이너를 실행하는 다른 Linux 컴퓨터를 식별합니다. 보안 센터는 이러한 컨테이너의 구성을 지속적으로 평가합니다. 그런 다음 [CIS(인터넷 보안 센터) Docker 벤치마크와](https://www.cisecurity.org/benchmark/docker/)비교합니다.

보안 센터에는 CIS Docker 벤치마크의 전체 규칙 집합이 포함되며 컨테이너가 컨트롤을 충족하지 않는 경우 경고합니다. 잘못 구성이 발견되면 보안 센터에서 보안 권장 사항을 생성합니다. 권장 **사항 페이지를** 사용하여 권장 사항을 보고 문제를 해결합니다. 또한 Docker와 함께 배포된 모든 가상 컴퓨터를 표시하는 **컨테이너** 탭에 권장 사항이 표시됩니다. 

이 기능에 대해 나타날 수 있는 관련 보안 센터 권장 사항에 대한 자세한 내용은 권장 사항 참조 테이블의 [컨테이너 섹션을](recommendations-reference.md#recs-containers) 참조하십시오.

VM의 보안 문제를 탐색할 때 보안 센터에서는 컴퓨터의 컨테이너에 대한 추가 정보를 제공합니다. 이러한 정보에는 Docker 버전과 호스트에서 실행되는 이미지 수가 포함됩니다. 

>[!NOTE]
> 이러한 CIS 벤치마크 검사는 AKS 관리 인스턴스 또는 Databricks 관리 VM에서 실행되지 않습니다.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes 클러스터의 지속적인 모니터링
보안 센터는 컨테이너화된 응용 프로그램을 개발, 배포 및 관리하기 위해 Microsoft의 관리되는 컨테이너 오케스트레이션 서비스인 Azure Kubernetes 서비스(AKS)와 함께 작동합니다.

AKS는 클러스터의 보안 태세에 대한 보안 제어 및 가시성을 제공합니다. 보안 센터는 다음 기능을 사용합니다.
* AKS 클러스터 구성을 지속적으로 모니터링
* 업계 표준에 부합하는 보안 권장 사항 생성

이 기능에 대해 나타날 수 있는 관련 보안 센터 권장 사항에 대한 자세한 내용은 권장 사항 참조 테이블의 [컨테이너 섹션을](recommendations-reference.md#recs-containers) 참조하십시오.

## <a name="run-time-protection---real-time-threat-detection"></a>런타임 보호 - 실시간 위협 탐지

보안 센터는 컨테이너화된 환경에 대한 실시간 위협 탐지를 제공하고 의심스러운 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

호스트 및 AKS 클러스터 수준에서 위협을 감지합니다. 자세한 내용은 [Azure 컨테이너에 대한 위협 검색을](threat-protection.md#azure-containers)참조하십시오.


## <a name="container-security-faq"></a>컨테이너 보안 자주 묻는 질문

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Azure 보안 센터에서 스캔할 수 있는 이미지 유형은 무엇입니까?
보안 센터는 셸 액세스를 제공하는 Linux OS 기반 이미지를 검사합니다. 

Qualys 스캐너는 [Docker 스크래치](https://hub.docker.com/_/scratch/) 이미지와 같은 매우 미니멀한 이미지 또는 패키지 관리자, 셸 또는 OS없이 응용 프로그램 및 런타임 종속성만 포함하는 "배포자없는" 이미지를 지원하지 않습니다.

### <a name="how-does-azure-security-center-scan-an-image"></a>Azure 보안 센터에서 이미지를 검색하는 방법은 무엇입니까?
이미지가 레지스트리에서 가져옵니다. 그런 다음 알려진 취약점 목록을 추출하는 Qualys 스캐너를 통해 격리된 샌드박스에서 실행됩니다.

보안 센터는 스캐너에서 결과를 필터링하고 분류합니다. 이미지가 정상이면 보안 센터에서 이미지로 표시합니다. 보안 센터는 해결해야 할 문제가 있는 이미지에 대해서만 보안 권장 사항을 생성합니다. 보안 센터는 문제가 있을 때만 알림을 통해 원치 않는 정보 경고를 줄일 수 있습니다.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Azure 보안 센터에서 내 이미지를 얼마나 자주 스캔합니까?
이미지 스캔은 모든 푸시에서 트리거됩니다.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API를 통해 검사 결과를 얻을 수 있습니까?
예. 결과는 하위 [평가 나머지 API아래에](/rest/api/securitycenter/subassessments/list/)있습니다. 또한 모든 리소스에 대해 Kusto와 유사한 API인 AZURE 리소스 그래프(ARG)를 사용할 수 있습니다.
 

## <a name="next-steps"></a>다음 단계

Azure 보안 센터에서 컨테이너 보안에 대해 자세히 알아보려면 다음 관련 문서를 참조하세요.

* 컨테이너 관련 리소스의 보안 상태를 보려면 컴퓨터 및 [응용 프로그램 보호의](security-center-virtual-machine-protection.md#containers)컨테이너 섹션을 참조하십시오.

* [Azure Kubernetes 서비스와의 통합에](azure-kubernetes-service-integration.md) 대한 세부 정보

* [Azure 컨테이너 레지스트리와의 통합에](azure-container-registry-integration.md) 대한 세부 정보