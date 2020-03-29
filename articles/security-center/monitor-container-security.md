---
title: Azure 보안 센터에서 컨테이너의 보안 모니터링
description: Azure 보안 센터에서 컨테이너의 보안 상태를 확인하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: memildin
ms.openlocfilehash: 330cbc3f28f5e549d5a21417c3d7ccc1e5444769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919535"
---
# <a name="monitoring-the-security-of-your-containers"></a>컨테이너 보안 모니터링

이 페이지에서는 개념 섹션의 [컨테이너 보안 문서에](container-security.md) 설명된 컨테이너 보안 기능을 사용하는 방법에 대해 설명합니다.

Azure 보안 센터에서는 컨테이너 보안의 세 가지 측면을 다룹니다.

- **취약성 관리** - 보안 센터의 표준 가격 책정 계층(가격 책정 참조)에 있는 경우 새 이미지를 푸시할 때마다 ARM 기반 Azure 컨테이너 레지스트리를 검색할 수 있습니다. [pricing](/azure/security-center/security-center-pricing) Qualys에 의해 구동되는 스캐너는 보안 센터 권장 사항으로 결과를 제시합니다.
    자세한 지침은 [아래의 취약성에 대한 컨테이너 레지스트리 검색을](#scanning-your-arm-based-container-registries-for-vulnerabilities) 참조하십시오.

- **컨테이너의 Docker 호스트 강화** - 보안 센터에서 Docker를 실행하는 IaaS Linux VM 또는 기타 Linux 컴퓨터에서 호스팅되는 관리되지 않는 컨테이너를 찾아 컨테이너의 구성을 CIS(CIS) Docker 벤치마크 센터와 지속적으로 비교합니다. 컨테이너가 컨트롤을 충족하지 않는 경우 보안 센터에서 경고합니다. 잘못된 구성으로 인한 보안 위험에 대한 지속적인 모니터링은 모든 보안 프로그램의 중요한 구성 요소입니다. 
    자세한 지침은 아래 [컨테이너의 Docker 호스트 강화를](#hardening-your-containers-docker-hosts) 참조하십시오.

- **Azure Kubernetes 서비스 클러스터 강화** - 보안 센터는 Azure Kubernetes 서비스 클러스터 구성에서 취약점을 발견할 때 권장 사항을 제공합니다. 나타날 수 있는 특정 권장 사항에 대한 자세한 내용은 [Kubernetes 서비스 권장 사항을](recommendations-reference.md#recs-containers)참조하십시오.

- **런타임 보호** - 보안 센터의 표준 가격 책정 계층에 있는 경우 컨테이너화된 환경에 대한 실시간 위협 보호를 받게 됩니다. 보안 센터는 호스트 및 AKS 클러스터 수준에서 의심스러운 활동에 대한 경고를 생성합니다. 나타날 수 있는 관련 보안 경고에 대한 자세한 내용은 [Azure Kubernetes 서비스 클러스터에 대한 경고](alerts-reference.md#alerts-akscluster) 및 [컨테이너에 대한 경고-경고](alerts-reference.md#alerts-containerhost) 참조 테이블의 호스트 수준 섹션을 참조하십시오.

## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>ARM 기반 컨테이너 레지스트리에서 취약점 검색 

1. Azure 컨테이너 레지스트리 이미지의 취약성 검사를 사용하려면 다음을 수행합니다.

    1. Azure 보안 센터의 표준 가격 책정 계층에 있는지 확인합니다.

    1. 가격 **& 설정** 페이지에서 구독에 대한 선택적 컨테이너 레지스트리 ![번들을 사용하도록 설정합니다: 컨테이너 레지스트리 번들 사용](media/monitor-container-security/enabling-container-registries-bundle.png)

        이제 보안 센터에서 레지스트리로 푸시되는 이미지를 스캔할 준비가 되었습니다. 

        >[!NOTE]
        >이 기능은 이미지당 요금이 부과됩니다.


1. 이미지 검사를 트리거하려면 레지스트리로 푸시합니다. 

    일반적으로 약 10분 후에 검색이 완료되면 보안 센터 권장 사항에서 결과를 확인할 수 있습니다.
    

1. 결과를 보려면 **권장 사항** 페이지로 이동하십시오. 문제가 발견되면 다음 권장 사항이 표시됩니다.

    ![문제 해결 권장 사항 ](media/monitor-container-security/acr-finding.png)


1. 권장 사항을 선택합니다. 
    추천 세부 정보 페이지가 추가 정보와 함께 열립니다. 이 정보에는 취약한 이미지가 있는 레지스트리 목록("영향을 받는 리소스")과 수정 단계가 포함됩니다. 

1. 특정 레지스트리를 선택하여 취약한 리포지토리가 있는 리포지토리를 확인합니다.

    ![레지스트리 선택](media/monitor-container-security/acr-finding-select-registry.png)

    영향을 받는 리포지토리 목록과 함께 레지스트리 세부 정보 페이지가 열립니다.

1. 특정 리포지토리를 선택하여 취약한 이미지가 있는 리포지토리를 확인합니다.

    ![리포지토리 선택](media/monitor-container-security/acr-finding-select-repository.png)

    저장소 세부 정보 페이지가 열립니다. 그것은 결과의 엄격의 평가와 함께 취약한 심상을 나열합니다.

1. 특정 이미지를 선택하여 취약점을 확인합니다.

    ![이미지 선택](media/monitor-container-security/acr-finding-select-image.png)

    선택한 이미지의 결과 목록이 열립니다.

    ![조사 결과 목록](media/monitor-container-security/acr-findings.png)

1. 결과에 대해 자세히 알아보려면 찾기를 선택합니다. 

    조사 결과 세부 창이 열립니다.

    [![결과 세부 정보 창](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    이 창에는 문제에 대한 자세한 설명과 위협을 완화하는 데 도움이 되는 외부 리소스에 대한 링크가 포함되어 있습니다.

1. 이 창의 수정 섹션의 단계를 따릅니다.

1. 보안 문제를 해결하는 데 필요한 단계를 수행한 경우 레지스트리의 이미지를 교체합니다.

    1. 업데이트된 이미지를 누를 수 있습니다. 그러면 스캔이 트리거됩니다. 
    
    1. 권장 사항 페이지에서 "Azure 컨테이너 레지스트리 이미지의 취약점을 수정해야 합니다"를 확인합니다. 
    
        권장 사항이 여전히 나타나고 처리한 이미지가 취약한 이미지 목록에 계속 표시되는 경우 수정 단계를 다시 확인합니다.

    1. 업데이트된 이미지가 푸시되고 스캔되었으며 더 이상 권장 사항에 표시되지 않는 경우 레지스트리에서 "이전" 취약한 이미지를 삭제합니다.


## <a name="hardening-your-containers-docker-hosts"></a>컨테이너의 Docker 호스트 강화

보안 센터는 Docker 호스트의 구성을 지속적으로 모니터링하고 업계 표준을 반영하는 보안 권장 사항을 생성합니다.

컨테이너의 Docker 호스트에 대한 Azure 보안 센터의 보안 권장 사항을 보려면 다음을 수행하십시오.

1. 보안 센터 탐색 모음에서 **계산 & 앱을** 열고 **컨테이너** 탭을 선택합니다.

1. 선택적으로 컨테이너 리소스 목록을 컨테이너 호스트 호스트로 필터링합니다.

    ![컨테이너 리소스 필터](media/monitor-container-security/container-resources-filter.png)

1. 컨테이너 호스트 컴퓨터 목록에서 하나를 선택하여 자세히 조사합니다.

    ![컨테이너 호스트 권장 사항](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    **컨테이너 호스트 정보 페이지는** 호스트에 대한 세부 정보와 권장 사항 목록으로 열립니다.

1. 권장 사항 목록에서 권장 사항을 선택하여 추가로 조사합니다.

    ![컨테이너 호스트 권장 목록](media/monitor-container-security/container-host-rec.png)

1. 선택적으로 설명, 정보, 위협 및 수정 단계를 읽습니다. 

1. 페이지 하단에서 **작업 수행을** 선택합니다.

    [![작업 단추 수행](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    로그 애널리틱스는 실행할 준비가 된 사용자 지정 작업으로 열립니다. 기본 사용자 지정 쿼리에는 문제를 해결하는 데 도움이 되는 지침과 함께 평가된 모든 실패한 규칙 목록이 포함됩니다.

    [![로그 분석 작업](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 쿼리 매개 변수를 조정하고 호스트에 대한 준비가 확실하면 **실행을** 선택합니다. 



## <a name="next-steps"></a>다음 단계

이 문서에서는 보안 센터의 컨테이너 보안 기능을 사용하는 방법을 배웠습니다. 

기타 관련 자료는 다음 페이지를 참조하십시오. 

- [컨테이너에 대한 보안 센터 권장 사항](recommendations-reference.md#recs-containers)
- [AKS 클러스터 수준에 대한 경고](alerts-reference.md#alerts-akscluster)
- [컨테이너 호스트 수준에 대한 경고](alerts-reference.md#alerts-containerhost)
