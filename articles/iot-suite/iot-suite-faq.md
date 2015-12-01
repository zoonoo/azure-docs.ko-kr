<properties
  pageTitle="Azure IoT Suite FAQ | Microsoft Azure"
  description="IoT Suite에 대한 FAQ"
  services=""
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="na"
  ms.devlang="na"
  ms.topic="hero-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="11/17/2015"
  ms.author="araguila"/>
   
# 질문과 대답

### 하나의 구독에 프로비전할 수 있는 DocumentDB 인스턴스는 몇 개인가요?

5개입니다. 지원 티켓을 만들어서 이 한도를 높일 수 있지만, 기본적으로 구독 당 DocumentDB 인스턴스를 5개만 프로비전할 수 있습니다. 결과적으로, 지정된 구독에 원격 모니터링 미리 구성된 솔루션을 5개까지만 프로비전할 수 있습니다.

### 하나의 구독에 프로비전할 수 있는 무료 Bing 지도 API는 몇 개인가요?

2개입니다. 구독에 무료 Bing 지도 API는 2개만 만들 수 있습니다. 원격 모니터링 솔루션이 무료 Bing 지도 API와 함께 기본으로 프로비전됩니다. 결과적으로 구독에 원격 모니터링 솔루션을 가감 없이 2개까지만 프로비전할 수 있습니다.

### Azure Preview 포털에서 리소스 그룹을 삭제하는 것과 azureiotsuite.com의 미리 구성된 솔루션에서 삭제를 클릭하는 것의 차이는 무엇인가요?

- [azureiotsuite.com][lnk-azureiotsuite]에서 미리 구성된 솔루션을 삭제하면, 미리 구성된 솔루션을 만들 때 프로비전된 모든 리소스가 삭제됩니다. 리소스 그룹에 리소스를 추가하면, 이들 역시 삭제됩니다. 

- [Azure Preview 포털][lnk-azure-portal]에서 리소스 그룹을 삭제하면 해당 리소스 그룹에 포함된 리소스만 삭제됩니다. [Azure 관리 포털][lnk-classic-portal]의 미리 구성된 솔루션과 연결된 Azure Active Directory 응용 프로그램도 삭제해야 합니다.

### AAD 테넌트를 어떻게 삭제하나요?

Eric Golpe의 블로그 게시물 [Azure AD 테넌트 삭제 연습(영문)][lnk-delete-aad-tennant]을 참조하세요.


[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx

<!---HONumber=AcomDC_1125_2015-->