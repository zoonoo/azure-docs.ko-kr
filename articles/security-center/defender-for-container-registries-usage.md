---
title: 컨테이너 레지스트리에 대해 Azure Defender를 사용 하는 방법
description: 컨테이너 레지스트리 용 Azure Defender를 사용 하 여 레지스트리의 이미지를 검색 하는 방법에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 79a0db3b9f81368fbdaace5be3fd94ad29649291
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532604"
---
# <a name="use-azure-defender-for-container-registries-to-scan-your-images-for-vulnerabilities"></a>컨테이너 레지스트리 용 Azure Defender를 사용 하 여 이미지에서 취약성 검색

이 페이지에서는 기본 제공 취약점 스캐너를 사용 하 여 Azure Resource Manager 기반 Azure Container Registry에 저장 된 컨테이너 이미지를 검색 하는 방법을 설명 합니다.

**컨테이너 레지스트리 용 Azure Defender** 를 사용 하는 경우 레지스트리로 푸시하는 이미지를 즉시 검사 합니다. 또한 지난 30 일 내에 끌어온 이미지도 검색 됩니다. 

스캐너는 Security Center에 대 한 취약성을 보고 하는 경우 결과 및 관련 정보를 권장 사항으로 제공 Security Center. 또한 결과에는 재구성 단계, 관련 CVEs, CVES 점수 등의 관련 정보가 포함 됩니다. 하나 이상의 구독 또는 특정 레지스트리에 대해 식별 된 취약성을 볼 수 있습니다.

## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**컨테이너 레지스트리에 대 한 Azure Defender** 는 [가격 책정 페이지](security-center-pricing.md) 에 표시 된 대로 청구 됩니다.|
|지원 되는 레지스트리 및 이미지:|![예 ](./media/icons/yes-icon.png) Linux에서 호스트 되는 ACR 레지스트리는 공용 인터넷에서 액세스할 수 있고 셸 액세스를 제공 합니다.<br>![](./media/icons/no-icon.png)Windows에서 호스트 되는 ACR 레지스트리 없음.<br>![](./media/icons/no-icon.png)' Private ' 레지스트리를 Security Center 하지 않으면 공용 인터넷에서 레지스트리를 액세스할 수 있어야 합니다. Security Center는 현재 방화벽, 서비스 끝점 또는 Azure 개인 링크와 같은 개인 끝점으로 제한 된 액세스 권한이 있는 레지스트리 또는 검색에 연결할 수 없습니다.<br>![](./media/icons/no-icon.png) [Docker 스크래치](https://hub.docker.com/_/scratch/) 이미지와 같은 슈퍼 전적 이미지 또는 패키지 관리자, 셸 또는 OS 없이 응용 프로그램 및 해당 런타임 종속성만 포함 하는 "distroless" 이미지를 포함 하지 않습니다.|
|필요한 역할 및 권한:|**보안 읽기 권한자** 및 [Azure Container Registry 읽기 권한자 역할](https://docs.microsoft.com/azure/container-registry/container-registry-roles)|
|클라우드:|![예](./media/icons/yes-icon.png) 상용 클라우드<br>![아니요](./media/icons/no-icon.png) 국가/소버린(미국 정부, 중국 정부, 기타 정부)|
|||


## <a name="identify-vulnerabilities-in-images-in-azure-container-registries"></a>Azure 컨테이너 레지스트리의 이미지 취약성 식별 

1. Azure Resource Manager 기반 Azure Container Registry에 저장 된 이미지의 취약성 검색을 사용 하려면 다음을 수행 합니다.

    1. 구독에 대 한 **컨테이너 레지스트리에 대해 Azure Defender를** 사용 하도록 설정 합니다.

        이제 Security Center는 레지스트리의 이미지를 스캔할 준비가 되었습니다.

        >[!NOTE]
        > 이 기능은 이미지별로 요금이 청구됩니다.

1. 이미지 검색은 모든 푸시 또는 가져오기에서 트리거되고, 지난 30 일 내에 이미지를 끌어온 경우에 발생 합니다. 

    검색이 완료 될 때 (일반적으로 약 2 분 후 최대 15 분이 될 수 있음) Security Center 권장 사항으로 제공 됩니다.

1. [아래 설명 된 대로 결과를 보고](#view-and-remediate-findings)수정 합니다.

## <a name="identify-vulnerabilities-in-images-in-other-container-registries"></a>다른 컨테이너 레지스트리에 있는 이미지의 취약성 식별 

1. ACR 도구를 사용 하 여 Docker 허브 또는 Microsoft Container Registry에서 레지스트리에 이미지를 가져옵니다.  가져오기가 완료 되 면 Azure Defender에서 가져온 이미지를 검색 합니다. 

    컨테이너 [레지스트리에 컨테이너 이미지 가져오기에 대 한](../container-registry/container-registry-import-images.md) 자세한 정보

    검색이 완료 될 때 (일반적으로 약 2 분 후 최대 15 분이 될 수 있음) Security Center 권장 사항으로 제공 됩니다.

1. [아래 설명 된 대로 결과를 보고](#view-and-remediate-findings)수정 합니다.


## <a name="view-and-remediate-findings"></a>검색 결과 보기 및 재구성

1. 결과를 보려면 **권장 사항** 페이지로 이동 합니다. 문제가 발견 되 면 **Azure Container Registry 이미지를 재구성 해야 한다는** 권장 사항을 확인할 수 있습니다.

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


## <a name="disable-specific-findings-preview"></a>특정 검색 (미리 보기) 사용 안 함

조직에서 결과를 수정하지 않고 무시해야 하는 요구 사항이 있으면 필요에 따라 이 결과를 사용하지 않도록 설정할 수 있습니다. 사용하지 않도록 설정된 결과는 보안 점수에 영향을 주거나 원치 않는 노이즈를 생성하지 않습니다.

결과가 사용 안 함 규칙에 정의한 조건과 일치하면 검색 결과 목록에 표시되지 않습니다. 일반적인 시나리오는 다음과 같습니다.

- 심각도가 보통인 낮은 결과를 사용 하지 않도록 설정
- 패치 가능한 아닌 검색 결과 사용 안 함
- 6.5 미만의 CVSS 점수를 사용 하 여 결과를 사용 하지 않도록 설정
- 보안 검사 또는 범주에 특정 텍스트가 포함 된 결과를 사용 하지 않도록 설정 합니다 (예: "RedHat", "CentOS Security Update for sudo").

> [!IMPORTANT]
> 규칙을 만들려면 Azure Policy에서 정책을 편집할 수 있는 권한이 필요 합니다.
>
> [Azure Policy에서 AZURE RBAC 사용 권한](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)에 대해 자세히 알아보세요.

다음 조건 중 하나를 사용할 수 있습니다. 

- ID 찾기 
- 범주
- 보안 검사 
- CVSS v3 점수
- 심각도 
- 패치 가능한 상태 

규칙을 만들려면 다음을 수행합니다.

1. **Azure Container Registry 이미지의 취약성**에 대 한 권장 사항 세부 정보 페이지에서 **규칙 사용 안 함**을 선택 합니다.
1. 관련 범위를 선택 합니다.
1. 조건을 정의 합니다.
1. **규칙 적용**을 선택 합니다.

    :::image type="content" source="./media/defender-for-container-registries-usage/new-disable-rule-for-registry-finding.png" alt-text="레지스트리에서 VA 검색에 대 한 사용 안 함 규칙 만들기":::

1. 규칙을 보거나 재정의 하거나 삭제 하려면 다음을 수행 합니다. 
    1. **규칙 사용 안 함**을 선택 합니다.
    1. 범위 목록에서 활성 규칙이 있는 구독은 **규칙 적용**됨으로 표시 됩니다.
        :::image type="content" source="./media/remediate-vulnerability-findings-vm/modify-rule.png" alt-text="레지스트리에서 VA 검색에 대 한 사용 안 함 규칙 만들기":::
    1. 규칙을 보거나 삭제 하려면 줄임표 메뉴 ("...")를 선택 합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Defender](azure-defender.md)에 대해 자세히 알아보세요.