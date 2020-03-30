---
title: Azure DevTest 랩의 환경에 대한 보안 경고
description: 이 문서에서는 DevTest Labs의 환경에 대한 보안 경고를 보고 적절한 조치를 취하는 방법을 보여 주며 이 문서에서는 이러한 작업을 수행합니다.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2020
ms.author: spelluru
ms.openlocfilehash: fbac5a2fab91cdac8ebf626e324f12f209cfade5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588708"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>Azure DevTest 랩의 환경에 대한 보안 경고
이제 랩 사용자로서 랩 환경에 대한 Azure 보안 센터 경고를 볼 수 있습니다. 보안 센터는 방화벽 및 엔드포인트 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결된 파트너 솔루션의 로그 데이터를 자동으로 수집하고 분석하며 통합하여 실제 위협을 감지하고 가양성을 줄입니다. 우선 순위가 지정된 보안 경고의 목록은 문제를 신속하게 조사해야 하는 정보 및 공격을 해결하는 방법에 대한 권장 사항과 함께 보안 센터에 표시됩니다. [Azure 보안 센터에서 보안 경고에 대해 자세히 알아봅니다.](../security-center//security-center-alerts-overview.md)  


## <a name="prerequisites"></a>사전 요구 사항
현재 랩에 배포된 PaaS(서비스) 환경으로 플랫폼에 대한 보안 경고만 볼 수 있습니다. 이 기능을 테스트하거나 사용하려면 [환경을 랩에 배포합니다.](devtest-lab-create-environment-from-arm.md) 

## <a name="view-security-alerts-for-an-environment"></a>환경에 대한 보안 경고 보기

1. 랩의 홈 페이지에서 왼쪽 메뉴에서 **보안 경고를 선택합니다.** 보안 경고 수(높음, 중간 및 낮음)가 표시됩니다. [경고가 분류되는 방법에](../security-center/security-center-alerts-overview.md#how-are-alerts-classified)대해 자세히 알아보십시오.

    ![보안 경고 - 개요](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 마지막 열에서 세 점(...)을 마우스 오른쪽 단추로 클릭하고 **보안 경고 보기를 선택합니다.** 

    ![보안 경고 보기](./media/environment-security-alerts/view-security-alerts-menu.png)
    
3. 경고 및 어드바이저 권장 사항에 대한 자세한 내용을 볼 수 있습니다. [Azure 보안 센터에서 보안 경고 관리 및 응답에](../security-center/security-center-managing-and-responding-alerts.md)대해 자세히 알아봅니다.

    ![보안 경고 보기](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>다음 단계
환경에 대한 자세한 내용은 다음 문서를 참조하십시오.

- [Azure 리소스 관리자 템플릿을 사용하여 다중 vm 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
