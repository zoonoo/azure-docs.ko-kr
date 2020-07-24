---
title: Azure Security Center에서 컨테이너의 보안 모니터링
description: Azure Security Center에서 컨테이너의 보안 상태를 확인 하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: memildin
ms.openlocfilehash: 5f558aaa367d156a8a4fa8633f113c6480236f5b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038617"
---
# <a name="monitor-the-security-of-your-containers"></a>컨테이너의 보안 모니터링

이 페이지에서는 개념 섹션의 [컨테이너 보안 문서](container-security.md) 에 설명 된 컨테이너 보안 기능을 사용 하는 방법을 설명 합니다.


## <a name="scanning-your-arm-based-container-registries-for-vulnerabilities"></a>취약성에 대 한 ARM 기반 컨테이너 레지스트리 검색 

1. Azure Container Registry 이미지의 취약성 검색을 사용 하도록 설정 하려면 다음을 수행 합니다.

    1. Azure Security Center의 표준 가격 책정 계층에 있는지 확인 합니다.

    1. **가격 책정 & 설정** 페이지에서 구독에 대 한 선택적 컨테이너 레지스트리 번들을 사용 하도록 설정 합니다. ![ 컨테이너 레지스트리 번들을 사용 하도록 설정 합니다.](media/monitor-container-security/enabling-container-registries-bundle.png)

        이제 Security Center는 레지스트리에 푸시되는 이미지를 스캔할 준비가 되었습니다. 

        >[!NOTE]
        >이 기능은 이미지 별로 요금이 청구 됩니다.


1. 이미지 검사를 트리거하려면 레지스트리에 푸시합니다. 

    검색이 완료 될 때 (일반적으로 약 2 분 후에는 최대 15 분이 될 수 있음) 다음과 같은 Security Center 권장 사항으로 결과를 사용할 수 있습니다.

1. 결과를 보려면 **권장 사항** 페이지로 이동 합니다. 문제가 발견 되 면 다음과 같은 권장 사항이 표시 됩니다.

    ![문제를 해결 하기 위한 권장 사항 ](media/monitor-container-security/acr-finding.png)

1. 권장 사항을 선택 합니다. 
    추가 정보를 포함 하는 권장 사항 세부 정보 페이지가 열립니다. 이 정보에는 취약 한 이미지를 포함 하는 레지스트리 목록 ("영향을 받는 리소스") 및 수정 단계가 포함 됩니다. 

1. 특정 레지스트리를 선택 하 여 취약 한 리포지토리가 있는 리포지토리를 확인 합니다.

    ![레지스트리 선택](media/monitor-container-security/acr-finding-select-registry.png)

    영향을 받는 리포지토리 목록과 함께 레지스트리 세부 정보 페이지가 열립니다.

1. 특정 리포지토리를 선택 하 여 취약 한 이미지가 있는 리포지토리를 확인 합니다.

    ![리포지토리 선택](media/monitor-container-security/acr-finding-select-repository.png)

    리포지토리 세부 정보 페이지가 열립니다. 문제의 심각도 평가와 함께 취약 한 이미지를 나열 합니다.

1. 특정 이미지를 선택 하 여 취약성을 확인 합니다.

    ![이미지 선택](media/monitor-container-security/acr-finding-select-image.png)

    선택한 이미지에 대 한 검색 결과 목록이 열립니다.

    ![검색 결과 목록](media/monitor-container-security/acr-findings.png)

1. 찾기에 대 한 자세한 내용을 보려면 찾기를 선택 합니다. 

    검색 결과 세부 정보 창이 열립니다.

    [![검색 결과 세부 정보 창](media/monitor-container-security/acr-finding-details-pane.png)](media/monitor-container-security/acr-finding-details-pane.png#lightbox)

    이 창에는 문제에 대 한 자세한 설명과 위협을 완화 하는 데 도움이 되는 외부 리소스에 대 한 링크가 포함 되어 있습니다.

1. 이 창의 재구성 섹션에 나오는 단계를 수행 합니다.

1. 보안 문제를 해결 하는 데 필요한 단계를 수행 했으면 레지스트리에서 이미지를 바꿉니다.

    1. 업데이트 된 이미지를 푸시합니다. 그러면 검사가 트리거됩니다. 
    
    1. 권장 사항 페이지에서 "Azure Container Registry 이미지의 취약성을 수정 해야 합니다."를 확인 합니다. 
    
        권장 사항이 계속 표시 되 고 처리 된 이미지가 취약 한 이미지 목록에 계속 표시 되는 경우 수정 단계를 다시 확인 합니다.

    1. 업데이트 된 이미지가 푸시되 고 검색 되었으며 더 이상 권장 사항에 표시 되지 않으면 레지스트리에서 "오래 된" 공격 이미지를 삭제 합니다.


## <a name="hardening-your-containers-docker-hosts"></a>컨테이너의 Docker 호스트 강화

Security Center는 Docker 호스트의 구성을 지속적으로 모니터링 하 고 업계 표준을 반영 하는 보안 권장 사항을 생성 합니다.

컨테이너의 Docker 호스트에 대 한 Azure Security Center 보안 권장 사항을 보려면 다음을 수행 합니다.

1. Security Center 탐색 모음에서 **Compute & apps** 를 열고 **컨테이너** 탭을 선택 합니다.

1. 필요에 따라 컨테이너 리소스 목록을 컨테이너 호스트 호스트로 필터링 합니다.

    ![컨테이너 리소스 필터](media/monitor-container-security/container-resources-filter.png)

1. 컨테이너 호스트 컴퓨터 목록에서 하나를 선택 하 여 자세히 조사 합니다.

    ![컨테이너 호스트 권장 사항](media/monitor-container-security/container-resources-filtered-to-hosts.png)

    **컨테이너 호스트 정보 페이지** 에 호스트의 세부 정보와 권장 사항 목록이 열립니다.

1. 권장 사항 목록에서 권장 사항을 선택 하 여 추가로 조사 합니다.

    ![컨테이너 호스트 권장 사항 목록](media/monitor-container-security/container-host-rec.png)

1. 필요에 따라 설명, 정보, 위협 및 수정 단계를 읽습니다. 

1. 페이지 맨 아래에 있는 **작업 수행** 을 선택 합니다.

    [![작업 수행 단추](media/monitor-container-security/host-security-take-action-button.png)](media/monitor-container-security/host-security-take-action.png#lightbox)

    사용자 지정 작업을 실행할 준비가 되 면 Log Analytics 열립니다. 기본 사용자 지정 쿼리는 문제를 해결 하는 데 도움이 되는 지침과 함께 평가 된 모든 실패 한 규칙의 목록을 포함 합니다.

    [![Log Analytics 작업](media/monitor-container-security/log-analytics-for-action-small.png)](media/monitor-container-security/log-analytics-for-action.png#lightbox)

1. 필요한 경우 쿼리 매개 변수를 조정 합니다.

1. 명령이 적절 하 고 호스트에 대해 준비 되 면 **실행**을 선택 합니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center의 컨테이너 보안 기능을 사용 하는 방법을 알아보았습니다. 

기타 관련 자료는 다음 페이지를 참조 하세요. 

- [컨테이너에 대 한 Security Center 권장 사항](recommendations-reference.md#recs-containers)
- [AKS 클러스터 수준에 대 한 경고](alerts-reference.md#alerts-akscluster)
- [컨테이너 호스트 수준에 대 한 경고](alerts-reference.md#alerts-containerhost)
