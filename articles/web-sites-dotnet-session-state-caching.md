<properties linkid="video-center-index" urlDisplayName="index" pageTitle="Video Center Index" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Session State with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Azure 웹 사이트에 ASP.NET 세션 상태 사용 방법
=============================================

이 항목에서는 Azure 캐시 서비스(사전 검토)를 사용하여 ASP.NET 세션 상태 캐싱을 지원하는 방법을 설명합니다.

외부 공급자가 없는 경우 세션 상태는 사이트를 호스팅하는 웹 서버에 In Process로 저장됩니다. Azure 웹 사이트의 경우 In Process 세션 상태에 다음 두 가지 문제가 있습니다. 첫째로, 여러 인스턴스가 있는 사이트의 경우 한 인스턴스에 저장된 세션 상태는 다른 인스턴스에 액세스할 수 없습니다. 사용자 요청은 어느 인스턴스로든 라우팅될 수 있으므로 세션 정보가 해당 인스턴스에 있다는 보장이 없습니다. 둘째로, 구성의 변경 내용으로 인해 웹 사이트가 완전히 다른 서버에서 실행될 수 있습니다.

캐시 서비스(사전 검토)는 웹 사이트 외부의 분산 캐싱 서비스를 제공합니다. 이를 통해 In Process 세션 상태의 문제가 해결됩니다. 세션 상태 사용 방법에 대한 자세한 내용은 [ASP.NET 세션 상태 개요](http://msdn.microsoft.com/ko-kr/library/ms178581.aspx)를 참조하십시오.

세션 상태 캐싱에 캐시 서비스(사전 검토)를 사용하는 기본 단계는 다음과 같습니다.

-   [캐시를 만듭니다.](#createcache)
-   [Azure 캐시를 사용하도록 ASP.NET 프로젝트를 구성합니다.](#configureproject)
-   [web.config 파일을 수정합니다.](#configurewebconfig)
-   [Session 개체를 사용하여 캐시된 항목을 저장 및 검색합니다.](#usesessionobject)

캐시 만들기
-----------

1.  Azure 관리 포털 맨 아래에 있는 **새로 만들기** 아이콘을 클릭합니다.

    ![새로 만들기 아이콘](./media/web-sites-dotnet-session-state-caching/CacheScreenshot_NewButton.png)

2.  **데이터 서비스**, **캐시**를 차례로 선택한 후 **빨리 만들기**를 클릭합니다.

    ![새 캐시 대화 상자](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CreateOptions.png)

3.  **끝점** 입력란에 캐시의 고유 이름을 입력합니다. 다음으로 다른 캐시 속성에 대해 적합한 값을 선택하고 **Create a New Cache**를 클릭합니다.

4.  관리 포털의 **캐시** 아이콘을 선택하여 모든 캐시 서비스 끝점을 봅니다.

    ![캐시 아이콘](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheIcon.png)

5.  그런 다음 캐시 서비스 끝점 중 하나를 선택하여 해당 속성을 봅니다. 다음 섹션에서는 **대시보드** 탭의 설정을 사용하여 ASP.NET 프로젝트의 캐싱을 구성합니다.

ASP.NET 프로젝트 구성
---------------------

1.  먼저, [최신](http://www.windowsazure.com/ko-kr/downloads/?sdk=net) **Azure SDK for .NET**이 설치되어 있어야 합니다.

2.  Visual Studio의 **솔루션 탐색기**에서 ASP.NET 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. WebMatrix를 사용하는 경우 도구 모음의 **NuGet** 단추를 대신 클릭합니다.

3.  **온라인 검색** 편집 상자에 **WindowsAzure.Caching**을 입력합니다.

    ![NuGet 대화 상자](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_NuGet.png)

4.  **Azure 캐싱** 패키지를 선택한 후 **설치** 단추를 클릭합니다.

Web.Config 파일 수정
--------------------

NuGet 패키지는 캐시에 대한 어셈블리 참조를 만들 뿐 아니라 web.config 파일에 스텁 항목을 추가합니다. 세션 상태에 캐시를 사용하려면 web.config에서 여러 항목을 수정해야 합니다.

1.  ASP.NET 프로젝트에 대한 **web.config** 파일을 엽니다.

2.  기존 **sessionState** 요소를 찾고 주석으로 처리하거나 제거합니다.

3.  그런 다음 Azure 캐싱 NuGet 패키지로 추가되었던 **sessionState** 요소의 주석 처리를 제거합니다. 최종 결과는 다음 스크린샷과 유사해야 합니다.

    ![SessionStateConfig][SessionStateConfig]

4.  다음으로 **dataCacheClients** 섹션을 찾습니다. **securityProperties** 하위 요소에 달린 주석을 제거합니다.

    ![캐시 구성](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_CacheConfig.png)

5.  **autoDiscover** 요소에서 캐시의 끝점 URL에 **identifier** 특성을 설정합니다. 끝점 URL을 찾으려면 Azure 관리 포털에서 캐시 속성으로 이동합니다. **대시보드** 탭에서 **간략 상태** 섹션에 **끝점 URL** 값을 복사합니다.

    ![끝점 URL](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_EndpointURL.png)

6.  **messageSecurity** 요소에서 캐시의 액세스 키에 **authorizationInfo** 특성을 설정합니다. 액세스 키를 찾으려면 Azure 관리 포털에서 캐시를 선택합니다. 그런 다음 맨 아래 표시줄에서 **키 관리** 아이콘을 클릭합니다. **기본 액세스 키** 입력란 옆에 복사 단추를 클릭합니다.

    ![키 관리](./media/web-sites-dotnet-session-state-caching/CachingScreenshot_ManageAccessKeys.png)

코드에 Session 개체 사용
------------------------

마지막 단계는 ASP.NET 코드에서 Session 개체 사용을 시작하는 것입니다. **Session.Add** 메서드를 사용하여 세션 상태에 개체를 추가합니다. 이 메서드는 키-값 쌍을 사용하여 세션 상태 캐시에 항목을 저장합니다.

    string strValue = "yourvalue";
    Session.Add("yourkey", strValue);

다음 코드를 사용하면 세션 상태에서 이 값이 검색됩니다.

    object objValue = Session["yourkey"];
    if (objValue != null)
       strValue = (string)obj;  

ASP.NET 세션 상태 사용 방법에 대한 자세한 내용은 [ASP.NET 세션 상태 개요](http://msdn.microsoft.com/ko-kr/library/ms178581.aspx)를 참조하십시오.

