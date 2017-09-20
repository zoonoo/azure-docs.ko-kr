---
title: "Azure Security Center에서 ID 및 액세스 모니터링 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center의 ID 및 액세스 기능을 사용하여 사용자의 액세스 활동과 문제와 관련된 ID를 모니터링하는 방법을 설명합니다."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9f04e730-4cfa-4078-8eec-905a443133da
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/13/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 2e351cd38e7baefc09fa36ceabffec92de22433b
ms.contentlocale: ko-kr
ms.lasthandoff: 09/14/2017

---
# <a name="monitoring-identity-and-access-in-azure-security-center"></a>Azure Security Center에서 ID 및 액세스 모니터링
이 문서에서는 Azure Security Center를 사용하여 사용자의 ID를 모니터링하고 작업에 액세스하는 방법을 설명합니다.

## <a name="why-monitor-identity-and-access"></a>ID 및 액세스 모니터링하는 이유는 무엇입니까?
ID는 엔터프라이즈의 제어 평면으로서 ID 보호가 최우선순위가 되어야 합니다. 과거에는 조직 주변에 경계가 있었고 그러한 경계가 기본적인 방어막 중 하나였지만 오늘날에는 점점 더 많은 앱이 클라우드로 이동함에 따라 ID가 새로운 경계가 되고 있습니다.

ID 활동을 모니터링함으로써 문제가 발생하기 전에 사전 대응 조치를 취하거나 사후 대응을 통해 공격 시도를 중단할 수 있습니다. ID 및 액세스 대시보드는 실패한 로그온 시도 수, 실패한 로그온에 사용된 사용자 계정, 잠긴 계정, 비밀번호가 변경 또는 초기화된 계정, 현재 로그인된 계정 수를 포함하여 ID 상태를 개략적으로 표시합니다.

## <a name="how-to-monitor-identity-and-access-activities"></a>ID를 모니터링하고 작업에 액세스 하는 방법은 무엇입니까?
현재 활동 관련 ID 및 액세스를 시각화하려면 아래 단계에 따라 **ID 및 액세스** 대시보드에 액세스해야 합니다.

1.  **Security Center** 대시보드를 엽니다.
2.  왼쪽 창의 **방지** 아래에서 **ID 및 액세스**를 클릭합니다. 여러 작업 영역에 있는 경우 작업 영역 선택기가 나타납니다.

    ![작업 영역 선택](./media/security-center-identity-access\security-center-identity-access-fig1.png)

    > [!NOTE]
    > 마지막 열에 **업그레이드 계획**이 표시되는 경우는 이 작업 영역이 무료 구독을 사용하기 때문이며, 이 기능을 사용하려면 표준으로 업그레이드해야 합니다. 업데이트 필요가 표시된 경우는 이 기능을 사용하기 위해 [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)를 업데이트해야 하기 때문입니다. 가격 계획에 대한 자세한 내용은 Azure Security Center 가격 책정을 참조하세요. 
    > 
3. 조사할 작업 영역이 둘 이상인 경우 이 작업 영역에 있는 현재 성공하지 못한 로그온 시도 횟수를 보여주는 **실패한 로그온** 열에 따라 조사 우선 순위를 지정할 수 있습니다. 사용하려는 작업 영역을 선택한 다음 **ID 및 액세스** 대시보드가 나타납니다.

    ![ID 및 액세스](./media/security-center-identity-access\security-center-identity-access-fig2.png)

4. 이 대시보드에 표시되는 정보를 통해 잠재적으로 의심스러운 활동을 즉시 식별할 수 있습니다. 이 대시보드는 세 가지 주요 영역으로 구성되어 있습니다.
    * **ID 포스처**: 이 작업 영역에서 수행 중인 ID와 관련된 활동을 요약합니다.
    * **실패한 로그온**: 실패한 로그온 시도에 대한 주요 원인을 신속하게 식별하도록 도와주고 로그온에 가장 많이 실패한 상위 10개의 계정 목록을 표시합니다.
    * **로그온 초과 시간**: 로그온 초과 시간 양을 빠르게 식별하고 로그온을 시도한 상위 컴퓨터 계정 목록을 표시합니다.
    
선택한 타일에 관계 없이 표시되는 대시보드는 [로그 검색 쿼리](https://docs.microsoft.com/azure/security-center/security-center-search)에 따라 나타나며, 유일한 차이점은 쿼리 및 결과의 형식입니다. 여전히 컴퓨터와 같은 항목을 선택할 수 있으며 클릭하면 관련 데이터를 볼 수 있습니다. 

## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center에서 ID 및 액세스를 모니터링하는 방법을 살펴보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다. 
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.


