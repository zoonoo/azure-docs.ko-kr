---
title: 환경에 대 한 보안 경고 Azure DevTest Labs
description: 이 문서에서는 DevTest Labs에서 환경에 대 한 보안 경고를 확인 하 고 적절 한 조치를 취하는 방법을 보여 줍니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: c9f7cf9fe7ab0e3f573470228ee1962aa92ccaef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91308693"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>환경에 대 한 보안 경고 Azure DevTest Labs
랩 사용자는 이제 랩 환경에 대 한 Azure Security Center 경고를 볼 수 있습니다. 보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다. [Azure Security Center의 보안 경고에 대해 자세히 알아보세요](../security-center//security-center-alerts-overview.md).  


## <a name="prerequisites"></a>필수 구성 요소
현재 랩에 배포 된 PaaS (platform as a service) 환경에 대 한 보안 경고만 볼 수 있습니다. 이 기능을 테스트 하거나 사용 하려면 [랩에 환경을 배포](devtest-lab-create-environment-from-arm.md)합니다. 

## <a name="view-security-alerts-for-an-environment"></a>환경에 대 한 보안 경고 보기

1. 랩의 홈 페이지에서 왼쪽 메뉴의 **보안 경고** 를 선택 합니다. 보안 경고 (높음, 중간 및 낮음) 수가 표시 되어야 합니다. [경고가 분류 되는 방법](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)에 대해 자세히 알아보세요.

    ![보안 경고-개요](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 마지막 열에서 세 개의 점 (...)을 마우스 오른쪽 단추로 클릭 하 고 **보안 경고 보기**를 선택 합니다. 

    !["보안 경고 보기"가 선택 된 보안 경고 페이지를 보여 주는 스크린샷](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 경고 및 advisor 권장 사항에 대 한 자세한 내용이 표시 됩니다. [Azure Security Center에서 보안 경고 관리 및 대응](../security-center/security-center-managing-and-responding-alerts.md)에 대해 자세히 알아보세요.

    ![보안 경고 보기](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>다음 단계
환경에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Resource Manager 템플릿으로 다중 vm 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
