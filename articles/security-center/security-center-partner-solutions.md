---
title: "Azure Security Center에서 파트너 솔루션 관리 | Microsoft Docs"
description: "이 문서에서는 Azure 보안 센터에서, Azure 구독에 통합된 파트너 솔ㄹ션의 보안 상태를 한 눈에 모니터링하는 방법을 살펴봅니다."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 70c076ef-3ad4-4000-a0c1-0ac0c9796ff1
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/26/2016
ms.author: terrylan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f469d0e17f874244539b05579598d35aa7dd01d5


---
# <a name="monitoring-partner-solutions-with-azure-security-center"></a>Azure 보안 센터를 사용하여 파트너 솔루션 모니터링
이 문서에서는 Azure 보안 센터에서 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.
> 
> 

## <a name="monitoring-partner-solutions"></a>파트너 솔루션 모니터링
**Security Center** 블레이드의 **파트너 솔루션** 타일을 통해 Azure 구독과 통합된 파트너 솔루션의 상태를 한 눈에 모니터링할 수 있습니다.
![파트너 솔루션 타일][1]

**파트너 솔루션** 타일은 파트너 솔루션의 수와 해당 솔루션의 상태 요약을 표시합니다.

파트너 솔루션의 **상태** 는 다음과 같을 수 있습니다.

* 보호됨(녹색) - 상태 문제가 없습니다.
* 비정상(빨강) - 즉각적인 주의가 필요한 상태 문제가 있습니다.
* 중지된 보고(주황) - 솔루션이 상태의 보고를 중지했습니다.
* 알 수 없는 보호 상태(주황) - 이 때에는 기존 솔루션에 새 리소스를 추가하는 실패한 프로세스로 인해 솔루션의 상태를 알 수 없습니다.
* 보고되지 않음(회색) - 솔루션이 아무 것도 보고하지 않았습니다. 솔루션이 방금 연결되었고 여전히 배포 중인 경우 솔루션의 상태를 보고하지 않을 수 있습니다.

구독과 통합된 솔루션이 없는 경우 이 타일은 솔루션이 없음을 표시합니다. **파트너 솔루션** 타일을 선택하면 **권장 사항** 블레이드를 열어 파트너 보안 솔루션을 배포할 수 있습니다.
![파트너 솔루션 없음][2]

파트너 솔루션의 상태를 보려면:

1. **파트너 솔루션** 타일을 선택합니다. 블레이드가 열리고 보안 센터에 연결된 파트너 솔루션의 목록을 표시합니다.
   ![파트너 솔루션][3]
2. 파트너 솔루션을 선택합니다. 이 예제에서는 **F5 WAF2** 솔루션을 선택하도록 합니다.  블레이드가 열리고 파트너 솔루션의 상태 및 솔루션의 관련 리소스를 표시합니다. **솔루션 콘솔** 을 선택하여 이 솔루션에 대한 파트너 관리 환경을 엽니다.
   ![파트너 솔루션 세부 정보][4]
3. **F5-WAF2** 블레이드로 돌아가 **앱 연결**을 선택합니다. **응용 프로그램 연결** 블레이드가 열립니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   ![리소스를 파트너 솔루션에 연결][5]

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터의 **파트너 솔루션** 타일을 소개했습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) - 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) - Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure 보안 센터 FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) - 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-partner-solutions/partner-solutions-tile.png
[2]: ./media/security-center-partner-solutions/no-partner-solutions-to-display.png
[3]: ./media/security-center-partner-solutions/partner-solutions.png
[4]: ./media/security-center-partner-solutions/partner-solutions-detail.png
[5]: ./media/security-center-partner-solutions/link-applications.png



<!--HONumber=Nov16_HO3-->


