<properties
   pageTitle="Azure 보안 센터에서 웹 응용 프로그램 방화벽 추가 | Microsoft Azure"
   description="이 문서에서는 Azure 보안 센터 권장 사항 **웹 응용 프로그램 방화벽 추가**를 구현하는 방법을 보여 줍니다."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/02/2016"
   ms.author="terrylan"/>

# Azure 보안 센터에서 웹 응용 프로그램 방화벽 추가

Azure 보안 센터에서는 웹 응용 프로그램을 보호하기 위해 Microsoft 파트너의 WAF(웹 응용 프로그램 방화벽)를 추가하도록 권장할 수 있습니다. 이 문서에서는 이를 수행하는 방법의 예를 보여 줍니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다. 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.

## 권장 사항 구현

1. **권장 사항** 블레이드에서 **웹 응용 프로그램 방화벽을 사용하여 웹 응용 프로그램 보호**를 선택합니다. ![][1]

2. **웹 응용 프로그램 방화벽을 사용하여 웹 응용 프로그램을 보호** 블레이드에서 웹 응용 프로그램을 선택합니다. **웹 응용 프로그램 방화벽 추가** 블레이드가 열립니다. ![][2]
3. 기존 웹 응용 프로그램 방화벽(사용 가능한 경우)을 사용하도록 선택하거나, 새로 만들 수 있습니다. 이 예제에서는 기존 WAF가 없으므로 새 WAF를 만들어 보겠습니다.

4. 새 WAF를 만들려면 통합 파트너 목록에서 솔루션을 선택합니다. 이 예제에서는 **Barracuda Web Application Firewall**을 선택합니다.
5. 파트너 솔루션에 대한 정보를 제공하는 **Barracuda Web Application Firewall** 블레이드가 열립니다. 정보 블레이드에서 **만들기**를 선택합니다. ![][3]

6. **새 웹 응용 프로그램 방화벽** 블레이드가 열립니다 이 블레이드에서 **VM 구성** 단계를 수행하고 **WAF 정보**를 입력할 수 있습니다. **VM 구성**을 선택합니다.

7. **VM 구성** 블레이드에서 WAF를 실행할 가상 컴퓨터를 스핀업하는 데 필요한 정보를 입력합니다. ![][4]
8. **새 웹 응용 프로그램 방화벽** 블레이드로 돌아가서 **WAF 정보**를 선택합니다. **WAF 정보** 블레이드에서 WAF 자체를 구성합니다. 7단계에서는 WAF가 실행될 가상 컴퓨터를 구성하고 8단계에서는 WAF 자체를 프로비전할 수 있습니다.

9. **권장 사항** 블레이드로 돌아갑니다. WAF를 만든 후에 **웹 응용 프로그램 방화벽 설정 완료**라고 하는 새 항목이 생성되었습니다. 새 항목을 사용하면 Azure 가상 네트워크 내에서 WAF를 연결하는 프로세스를 완료하여 응용 프로그램을 보호할 수 있다는 점을 알 수 있습니다. ![][5]

10. **웹 응용 프로그램 방화벽 설정 완료**를 선택합니다. 새 블레이드가 열립니다. 해당 트래픽 경로를 전환해야 하는 웹 응용 프로그램을 확인할 수 있습니다.
11. 웹 응용 프로그램을 선택합니다. 웹 응용 프로그램 방화벽 설정을 완료하는 단계를 알려주는 블레이드가 열립니다. 단계를 완료한 다음 **트래픽 제한**을 선택합니다. 그려면 보안 센터가 연결을 수행합니다. ![][6]

> [AZURE.NOTE] 기존 WAF 배포에 이러한 응용 프로그램을 추가하여 보안 센터에서 여러 웹 응용 프로그램을 보호할 수 있습니다. WAF 어플라이언스(리소스 관리자 배포 모델을 사용하여 만듦)는 별도의 가상 네트워크에 배포해야 합니다. WAF 어플라이언스(클래식 배포 모델을 사용하여 만듦)는 네트워크 보안 그룹 사용으로 제한됩니다. 이러한 지원은 나중에 WAF 어플라인스(클래식)의 완전 사용자 지정 배포로 확장됩니다. Azure 리소스의 [클래식 및 리소스 관리자 배포 모델](../azure-classic-rm.md)에 대해 자세히 알아봅니다.

이제 해당 WAF의 로그가 완전히 통합되었습니다. 보안 센터에서 자동으로 로그를 수집 및 분석하기 시작하여 중요한 보안 경고를 사용자에게 드러낼 수 있습니다.

## 다음 단계

이 문서에서는 보안 센터 권장 사항 "웹 응용 프로그램 추가"를 구현하는 방법을 보여 주었습니다. 웹 응용 프로그램 방화벽 구성에 대한 자세한 내용은 다음을 참조하세요.

- [앱 서비스 환경에 대한 웹 응용 프로그램 방화벽(WAF) 구성](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md)--Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) – Azure 리소스의 상태를 모니터링하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터에서 보안 권장 사항 관리](security-center-recommendations.md) –- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지 알아보기.
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0309_2016-->