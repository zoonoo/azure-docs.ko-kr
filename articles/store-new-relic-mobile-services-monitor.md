<properties 
	pageTitle="소스 제어에 서버 스크립트 저장 - Azure 모바일 서비스" 
	description="New Relic 추가 기능을 사용하여 모바일 서비스를 모니터링하는 방법에 대해 알아봅니다." 
	documentationCenter="" 
	authors="stepsic-microsoft-com" 
	manager="carolz" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="stepsic"/>

# New Relic을 사용하여 모바일 서비스 모니터링

이 항목에서는 모바일 서비스의 향상된 모니터링을 제공하기 위해 타사 New Relic 추가 기능을 Azure 모바일 서비스와 작동하도록 구성하는 방법을 보여 줍니다. 

이 자습서에서는 다음 단계를 안내합니다.

1. [Azure 스토어를 사용하여 New Relic에 등록]
2. [New Relic 모듈 설치]
3. [모바일 서비스에 New Relic 개발자 분석 사용]
4. [New Relic 대시보드에서 모바일 서비스 모니터링]

이 자습서를 완료하려면 [모바일 서비스 시작] 또는 [데이터 시작] 자습서를 완료하여 모바일 서비스를 만들어 둬야 합니다.

##<a name="sign-up"></a>Azure 스토어를 사용하여 New Relic에 등록

첫 번째 단계는 New Relic 서비스를 구입하는 것입니다. 이 자습서에서는 Azure 스토어에서 이 서비스를 구입하는 방법을 보여 줍니다. 모바일 서비스는 Azure 스토어 외부에서 구입한 New Relic 구독을 지원합니다.

1. [Azure 관리 포털](https://manage.windowsazure.com)에 로그인합니다.

2. 관리 포털의 아래쪽 창에서 **새로 만들기**를 클릭합니다.

3. **스토어**를 클릭합니다.

4. **추가 기능 선택** 대화 상자에서 **New Relic**을 선택하고 **다음**을 클릭합니다.

5. **추가 기능 개인 설정** 대화 상자에서 원하는 New Relic 플랜을 선택합니다.

7. Azure 설정에 New Relic 서비스를 표시할 이름을 입력하거나 기본값인 **NewRelic**을 사용합니다. 이 이름은 가입한 Azure 스토어 항목 목록에서 고유해야 합니다.

8. 지역 값(예: **미국 서부**)을 선택합니다.

9. **다음**을 클릭합니다.

10. **구입 검토** 대화 상자에서 플랜 및 가격 정보를 검토하고 약관을 확인합니다. 약관에 동의하는 경우 **구입**을 클릭합니다.

11. **구입**을 클릭하면 New Relic 계정을 만드는 프로세스가 시작됩니다. 이제 Azure 관리 포털에서 상태를 모니터링할 수 있습니다.

##<a name="install-module"></a>New Relic 모듈 설치

New Relic 서비스에 등록한 후에는 모바일 서비스에 New Relic Node.js 모듈을 설치해야 합니다. 모바일 서비스에 대해 소스 제어를 사용하도록 설정되어 있어야 이 모듈을 업로드할 수 있습니다.

1. 이렇게 하지 않은 경우에는 [소스 제어의 스토어 서버 스크립트] 자습서에 나온 단계를 따라 모바일 서비스에 대해 소스 제어를 사용하도록 설정하고 리포지토리를 복제하고 <a href="http://nodejs.org/" target="_blank">NPM(Node Package Manager)</a>을 설치합니다.

2. 로컬 Git 리포지토리의  `.\service` 폴더로 이동한 후 명령 프롬프트에서 다음 명령을 실행합니다.

		npm install newrelic

	NPM은  `\newrelic` 하위 디렉터리에 [New Relic 모듈][newrelic]을 설치합니다. 

3.  **GitBash**(Windows) 또는 **Bash** (Unix Shell)와 같은 Git 명령줄 도구를 열고 Git 명령 프롬프트에 다음 명령을 입력합니다. 

		$ git add .
		$ git commit -m "added newrelic module"
		$ git push origin master
		
	이 코드는 새  `newrelic` 모듈을 모바일 서비스에 업로드합니다. 

그런 다음 [관리 포털][Azure 관리 포털]에서 모바일 서비스의 New Relic 모니터링을 사용할 수 있도록 설정합니다. 

##<a name="enable-service"></a>모바일 서비스에 New Relic 개발자 분석 사용

1. [관리 포털][Azure 관리 포털]에서 모바일 서비스를 선택한 다음 **구성** 탭을 클릭합니다.

	![][0]

2. **개발자 분석**까지 아래로 스크롤하고 New Relic 구독을 구입한 방법에 따라 다음 중 하나를 수행합니다.

	+ Azure 스토어에서 구입:

		**추가 기능**을 클릭하고 **추가 기능 선택**에서 New Relic 추가 기능을 선택한 다음 **저장**을 클릭합니다.

		![][1]

	+ New Relic에서 직접 구입: 

		**사용자 지정**을 클릭하고 **공급자**에서 New Relic을 선택한 다음 키를 입력하고 **저장**을 클릭합니다.

		![][2]

		New Relic 대시보드에서 키를 가져올 수 있습니다.

3. 등록이 완료되면 **앱 설정**에 새 값이 표시됩니다.

	![][3] 

##<a name="monitor"></a>New Relic 대시보드에서 모바일 서비스 모니터링

1. 클라이언트 앱을 실행하여 모바일 서비스에 대한 읽기, 만들기, 업데이트 및 삭제 요청을 생성합니다.

2. 잠시 기다렸다가 데이터가 처리된 다음 New Relic 대시보드로 이동합니다.

	New Relic 구독을 추가 기능으로 구입했으면 [관리 포털][Azure 관리 포털]에서 해당 구독을 선택하고 **관리**를 클릭합니다.

3. New Relic에서 **응용 프로그램**을 클릭한 후 해당 모바일 서비스를 클릭합니다.

	![][4]

4. **웹 트랜잭션**을 클릭하여 모바일 서비스에 만든 최근 요청을 확인합니다.

	![][5]

##<a name="next-steps"> </a>다음 단계

+ **iOS**/**Android** 모바일 앱 성능을 최적화하려면 [New Relic Mobile]을 참조하세요.
+ 가격 책정 정보는 [Azure 스토어의 New Relic 페이지]를 참조하세요.
+ New Relic을 사용하는 방법에 대한 자세한 내용은 New Relic 설명서의 [Applications Overview]를 참조하세요. 

<!-- Anchors. -->
[Azure 스토어를 사용하여 New Relic에 등록]: #sign-up
[New Relic 모듈 설치]: #install-module
[모바일 서비스에 New Relic 개발자 분석 사용]: #enable-service
[New Relic 대시보드에서 모바일 서비스 모니터링]: #monitor
[다음 단계]: #next-steps

<!-- Images. -->
[0]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-tab.png
[1]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring.png
[2]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-custom.png
[3]: ./media/store-new-relic-mobile-services-monitor/mobile-configure-new-relic-monitoring-complete.png
[4]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard.png
[5]: ./media/store-new-relic-mobile-services-monitor/mobile-new-relic-dashboard-2.png

<!-- URLs. -->
[소스 제어]: http://msdn.microsoft.com/library/windowsazure/c25aaede-c1f0-4004-8b78-113708761643
[모바일 서비스에서 서버 스크립트 작업]: /ko-kr/develop/mobile/how-to-guides/work-with-server-scripts.md

[Azure 관리 포털]: https://manage.windowsazure.com/
[Node.js API 설명서: 모듈]: http://nodejs.org/api/modules.html
[소스 제어에 서버 스크립트 저장]: /ko-kr/develop/mobile/tutorials/store-scripts-in-source-control/
[newrelic]: https://npmjs.org/package/newrelic
[Azure 스토어의 New Relic 페이지]: /ko-kr/gallery/store/new-relic/new-relic/
[Applications Overview]: https://docs.newrelic.com/docs/applications-dashboards/applications-overview
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started/
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-dotnet
[New Relic Mobile]: http://newrelic.com/mobile-monitoring




<!--HONumber=42-->
