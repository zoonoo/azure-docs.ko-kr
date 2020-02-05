---
title: 환경에 대 한 보안 경고 Azure DevTest Labs
description: 이 문서에서는 DevTest Labs에서 환경에 대 한 보안 경고를 확인 하 고 적절 한 조치를 취하는 방법을 보여 줍니다.
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
ms.openlocfilehash: 4ca17bece33107de756eb221e14eaab851660a99
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992236"
---
# <a name="security-alerts-for-environments-in-azure-devtest-labs"></a>환경에 대 한 보안 경고 Azure DevTest Labs
이 문서에서는 Azure DevTest Labs 환경에 대 한 보안 경고를 확인 하는 방법을 보여 줍니다. 

## <a name="prerequisites"></a>필수 조건
현재 랩에 배포 된 환경에 대 한 보안 경고만 볼 수 있습니다. 이 기능을 테스트 하거나 사용 하려면 랩에 환경을 배포 합니다. 

## <a name="view-security-alerts-for-an-environment"></a>환경에 대 한 보안 경고 보기

1. 랩의 홈 페이지에서 왼쪽 메뉴의 **보안 경고** 를 선택 합니다. 보안 경고 (높음, 중간 및 낮음) 수가 표시 되어야 합니다.

    ![보안 경고-개요](./media/environment-security-alerts/security-alerts-overview-page.png)
2. 마지막 열에서 세 개의 점 (...)을 마우스 오른쪽 단추로 클릭 하 고 **보안 경고 보기**를 선택 합니다. 

    ![보안 경고 보기](./media/environment-security-alerts/view-security-alerts-menu.png)
3. 경고 및 advisor 권장 사항에 대 한 자세한 내용이 표시 됩니다. 

    ![보안 경고 보기](./media/environment-security-alerts/advisor-recommendations.png)


## <a name="next-steps"></a>다음 단계
환경에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [Azure Resource Manager 템플릿으로 다중 vm 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md)
- [공용 환경 구성 및 사용](devtest-lab-configure-use-public-environments.md)
