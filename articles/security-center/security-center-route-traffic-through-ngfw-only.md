<properties
   pageTitle="Azure 보안 센터에서 차세대 방화벽을 통해서만 트래픽 라우팅 | Microsoft Azure"
   description="이 문서에서는 Azure 보안 센터 권장 사항 **NGFW를 통해서만 트래픽 라우팅**을 구현하는 방법을 보여 줍니다."
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

# Azure 보안 센터에서 차세대 방화벽을 통해서만 트래픽 라우팅

Azure 보안 센터는 차세대 방화벽(NGFW)을 배포한 시기를 감지할 수 있습니다. 인터넷 끝점이 있는 경우 Azure 보안 센터는 NGFW를 통해 인바운드 트래픽을 가상 컴퓨터(VM)로 강제하는 네트워크 보안 그룹 규칙을 구성할 것을 권장합니다.

> [AZURE.NOTE] 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.

## 권장 사항 구현

1. **권장 사항 블레이드**에서 **NGFW를 통해서만 트래픽 라우팅**을 선택합니다. ![NGFW를 통해서만 트래픽 라우팅][1]

2. 트래픽을 라우팅할 수 있는 VM을 나열하는 **NGFW를 통해서만 트래픽 라우팅** 블레이드가 열립니다. 목록에서 VM을 선택합니다. ![VM 선택][2]

3. 선택된 VM에 대한 블레이드가 열리고 관련 인바운드 규칙을 표시합니다. 설명은 가능한 다음 단계에 대한 자세한 내용을 제공합니다. ![액세스를 제한하는 규칙 구성][3]

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
[1]: ./media/security-center-route-traffic-through-ngfw/route-traffic-through-ngfw.png
[2]: ./media/security-center-route-traffic-through-ngfw/select-vm.png
[3]: ./media/security-center-route-traffic-through-ngfw/configure-rules-to-limit-access.png

<!---HONumber=AcomDC_0720_2016-->