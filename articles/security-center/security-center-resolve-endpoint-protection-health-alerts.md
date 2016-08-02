<properties
   pageTitle="Azure 보안 센터에서 Endpoint Protection 상태 경고 해결| Microsoft Azure"
   description="이 문서에서는 Azure 보안 센터 권장 사항 **Endpoint Protection 상태 경고 해결**을 구현하는 방법을 보여 줍니다."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/20/2016"
   ms.author="terrylan"/>

# Azure 보안 센터에서 Endpoint Protection 상태 경고 해결

Azure 보안 센터는 검색된 Endpoint Protection 상태 경고를 해결할 것을 권장합니다. 보안 센터에서 Endpoint Protection 오류가 있는 가상 컴퓨터(VM) 및 오류 횟수를 확인할 수 있습니다.

> [AZURE.NOTE] 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.

## 권장 사항 구현

1. **권장 사항 블레이드**에서 **Endpoint Protection 상태 경고 해결**을 선택합니다. ![Endpoint Protection 상태 경고 해결][1]

2. 오류가 있는 VM 및 각 VM에 대한 오류 횟수를 나열하는 **Endpoint Protection 오류** 블레이드가 열립니다. 목록에서 VM을 선택합니다. ![Endpoint Protection 오류][2]

3. 선택한 VM에 대해 오류 목록을 표시하는 **오류 목록** 블레이드가 열립니다. 자세한 내용을 알아보려면 목록에서 오류를 선택합니다. 선택한 오류에 대한 정보가 포함된 블레이드가 열립니다. ![오류 목록][3] ![오류 이벤트][4]

## 참고 항목

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
- [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법을 알아봅니다.
- [Azure 보안 센터를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
- [Azure 보안 센터 FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) -- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-resolve-endpoint-protection/resolve-endpoint-protection.png
[2]: ./media/security-center-resolve-endpoint-protection/endpoint-protection-failure.png
[3]: ./media/security-center-resolve-endpoint-protection/failure-list.png
[4]: ./media/security-center-resolve-endpoint-protection/failure-event.png

<!---HONumber=AcomDC_0720_2016-->