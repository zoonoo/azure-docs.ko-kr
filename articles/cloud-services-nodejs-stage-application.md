<properties 
	pageTitle="클라우드 서비스 배포 준비(Node.js) - Azure" 
	description="Azure 응용 프로그램을 스테이징 환경에 배포한 다음 VIP(가상 IP) 교환을 사용하여 프로덕션 환경에 배포하는 방법에 대해 알아봅니다." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>







# Azure에서 응용 프로그램 준비

응용 프로그램이 인터넷에 액세스할 수 있는 프로덕션 환경으로 이동하기 전에
패키지 응용 프로그램이 Azure 스테이지 환경에 배포되어
테스트할 수 있습니다. Azure에서
생성된 애매한 URL로 스테이징된 응용 프로그램에만 액세스할 수 있다는 점을
제외하고 스테이징 환경은 프로덕션 환경과
동일합니다. 응용 프로그램이 올바르게 작동하는지 확인한 후
VIP(가상 IP) 교환을 수행하여 프로덕션 환경에
배포할 수 있습니다.

> [AZURE.NOTE] 이 문서의 단계는 Azure 클라우드 서비스로 호스트되는 노드 응용 프로그램에만 적용됩니다.

이 작업에는 다음 단계가 포함됩니다.

-   [1단계: 응용 프로그램 준비]
-   [2단계: VIP를 교환하여 프로덕션에 응용 프로그램 배포]

<h2><a id="step1"></a>1단계: 응용 프로그램 준비</h2>

이 작업은 **Windows Azure PowerShell
Azure PowerShell**을 사용하여 응용 프로그램을 준비하는 방법에 대해 다룹니다.

1.  서비스를 게시할 때 단지 **-Slot** 매개 변수를
    **Publish-AzureServiceProject** cmdlet으로 전달합니다.

    **Publish-AzureServiceProject -스테이징 슬롯**

2.  [Azure 관리 포털]에 로그온하고 **클라우드 서비스**를 선택합니다. 클라우드 서비스가 만들어지고 **스테이징** 열 상태가 **실행** 중으로 업데이트된 후 서비스 이름을 클릭합니다.

	![portal displaying a running service][cloud-service]

3.  **대시보드**를 선택한 후 **스테이징**을 선택합니다.

	![cloud service dashboard][cloud-service-dashboard]

4. 오른쪽에 있는 **사이트 URL** 항목의 값을 기록합니다. DNS 이름은 Azure에서 생성한 임시 내부 ID입니다.

    ![site url][cloud-service-staging-url]

이제 응용 프로그램이 스테이징 사이트 URL을 사용하여 스테이징 환경에서 올바르게 작동하고 있는 것을 확인할 수 있습니다.

스테이징 응용 프로그램이
프로덕션에 이미 배포된 업그레이드된 버전인 업그레이드 시나리오의 경우
[VIP를 교환하여 프로덕션 환경에서 응용 프로그램을
업그레이드][2단계: VIP를 교환하여 프로덕션에 응용 프로그램 배포]할 수 있습니다.

<h2><a id="step2"></a>2단계: VIP를 교환하여 프로덕션에서 응용 프로그램 업그레이드</h2>

스테이징 환경에서 업그레이드된 버전의 응용 프로그램을 확인한 후
스테이징 및 프로덕션 환경의 VIP(가상 IP)를 교환하여
프로덕션에서 빠르게 사용할 수 있도록
할 수 있습니다.

> [AZURE.NOTE] 이 단계에서는 프로덕션에 응용 프로그램을 이미 배포했으며
업그레이드된 버전의 응용 프로그램을 미리 구성한 것으로
가정합니다.

1.  [Azure 관리 포털]에 로그인하고
    **클라우드 서비스**를 클릭한 후 서비스 이름을 선택합니다.

2.  **대시보드**에서 **스테이징**을 선택한 후 페이지 맨 아래에 있는 **교환**을 클릭합니다. 그러면 VIP 교환 대화 상자가 열립니다.
    대화 상자를 닫습니다.

    ![vip swap dialog][vip-swap-dialog]

3.  정보를 검토한 후 **확인**을 클릭합니다. 두 배포 후
    스테이징 배포가 프로덕션으로 전환되고
    프로덕션 배포가 스테이징으로 전환되어 업데이트됩니다.

스테이징에서 VIP를 교환하여
배포를 스테이징하고 프로덕션 배포를 성공적으로 업그레이드했습니다.

## 추가 리소스

- [Azure에서 VIP를 교환하여 프로덕션에 서비스 업그레이드를 배포하는 방법]
- [Azure의 배포 관리 개요]

  [1단계: 응용 프로그램 준비]: #step1
  [2단계: VIP를 교환하여 프로덕션에 응용 프로그램 배포]: #step2
  [Azure 관리 포털]: http://manage.windowsazure.com
[cloud-service]: ./media/cloud-services-nodejs-stage-application/staging-cloud-service-running.png
[cloud-service-dashboard]: ./media/cloud-services-nodejs-stage-application/cloud-service-dashboard-staging.png
  [cloud-service-staging-url]: ./media/cloud-services-nodejs-stage-application/cloud-service-staging-url.png
  [vip-swap-dialog]: ./media/cloud-services-nodejs-stage-application/vip-swap-dialog.png
  [Azure에서 VIP를 교환하여 프로덕션에 서비스 업그레이드를 배포하는 방법]: http://msdn.microsoft.com/library/windowsazure/ee517253.aspx
  [Azure의 배포 관리 개요]: http://msdn.microsoft.com/library/windowsazure/hh386336.aspx

<!--HONumber=45--> 
