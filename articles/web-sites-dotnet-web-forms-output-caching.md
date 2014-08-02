<properties linkid="video-center-detail" urlDisplayName="details" pageTitle="Video Center Details" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="How to Use ASP.NET Web Forms Output Caching with Azure Web Sites" authors="jroth" solutions="" manager="" editor="" />

Azure 웹 사이트에서 ASP.NET 웹 양식 출력 캐싱을 사용하는 방법
=============================================================

이 항목에서는 Azure 캐시 서비스(사전 검토)를 사용하여 ASP.NET 웹 양식에 ASP.NET 페이지 출력 캐싱을 지원하는 방법을 설명합니다. 페이지 출력 캐싱은 캐시된 렌더링 페이지를 특정 기간 동안 직접 반환하는 최적화입니다. 이 기능은 페이지의 일반적인 변경 빈도보다 페이지에 액세스하는 빈도가 높은 상황에서 유용합니다. ASP.NET MVC 응용 프로그램에는 페이지 출력 캐싱이 지원되지 않습니다.

캐시 서비스(사전 검토)는 웹 사이트 외부의 분산 캐싱 서비스를 제공합니다. 이에 따라 웹 사이트의 모든 인스턴스가 동일한 캐시된 페이지 렌더링에 액세스할 수 있습니다.

페이지 출력 캐싱에 캐시 서비스(사전 검토)를 사용하는 기본 단계는 다음과 같습니다.

-   [캐시를 만듭니다.](#createcache)
-   [Azure 캐시를 사용하도록 ASP.NET 프로젝트를 구성합니다.](#configureproject)
-   [web.config 파일을 수정합니다.](#configurewebconfig)
-   [출력 캐싱을 사용하여 캐시된 버전의 페이지를 일시적으로 반환합니다.](#useoutputcaching)

캐시 만들기
-----------

1.  Azure 관리 포털 맨 아래에 있는 **새로 만들기** 아이콘을 클릭합니다.

    ![새로 만들기 아이콘](./media/web-sites-web-forms-output-caching/CacheScreenshot_NewButton.PNG)

2.  **데이터 서비스**, **캐시**를 차례로 선택한 후 **빨리 만들기**를 클릭합니다.

    ![새 캐시 대화 상자](./media/web-sites-web-forms-output-caching/CachingScreenshot_CreateOptions.PNG)

3.  **끝점** 입력란에 캐시의 고유 이름을 입력합니다. 다음으로 다른 캐시 속성에 대해 적합한 값을 선택하고 **Create a New Cache**를 클릭합니다.

4.  관리 포털의 **캐시** 아이콘을 선택하여 모든 캐시 서비스 끝점을 봅니다.

    ![캐시 아이콘](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheIcon.PNG)

5.  그런 다음 캐시 서비스 끝점 중 하나를 선택하여 해당 속성을 봅니다. 다음 섹션에서는 **대시보드** 탭의 설정을 사용하여 ASP.NET 프로젝트의 캐싱을 구성합니다.

ASP.NET 프로젝트 구성
---------------------

1.  먼저, [최신](http://www.windowsazure.com/en-us/downloads/?sdk=net) **Azure SDK for .NET**이 설치되어 있어야 합니다.

2.  Visual Studio의 **솔루션 탐색기**에서 ASP.NET 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. WebMatrix를 사용하는 경우 도구 모음의 **NuGet** 단추를 대신 클릭합니다.

3.  **온라인 검색** 편집 상자에 **WindowsAzure.Caching**을 입력합니다.

    ![NuGet 대화 상자](./media/web-sites-web-forms-output-caching/CachingScreenshot_NuGet.PNG)

4.  **Azure 캐싱** 패키지를 선택한 후 **설치** 단추를 클릭합니다.

Web.Config 파일 수정
--------------------

NuGet 패키지는 캐시에 대한 어셈블리 참조를 만들 뿐 아니라 web.config 파일에 스텁 항목을 추가합니다. ASP.NET 페이지 출력 캐싱에 캐시를 사용하려면 web.config에서 여러 항목을 수정해야 합니다.

1.  ASP.NET 프로젝트에 대한 **web.config** 파일을 엽니다.

2.  기존 **caching** 및 **outputCache** 요소가 있는 경우 이 요소를 주석으로 처리(또는 제거)합니다.

3.  그런 다음 Azure 캐싱 NuGet 패키지로 추가되었던 **caching** 요소에 달린 주석을 제거합니다. 최종 결과는 다음 스크린샷과 유사해야 합니다.

    ![OutputConfig](./media/web-sites-web-forms-output-caching/CachingScreenshot_OC_WebConfig.PNG)

4.  다음으로 **dataCacheClients** 섹션을 찾습니다. **securityProperties** 하위 요소에 달린 주석을 제거합니다.

    ![캐시 구성](./media/web-sites-web-forms-output-caching/CachingScreenshot_CacheConfig.PNG)

5.  **autoDiscover** 요소에서 캐시의 끝점 URL에 **identifier** 특성을 설정합니다. 끝점 URL을 찾으려면 Azure 관리 포털에서 캐시 속성으로 이동합니다. **대시보드** 탭에서 **간략 상태** 섹션에 **끝점 URL** 값을 복사합니다.

    ![끝점 URL](./media/web-sites-web-forms-output-caching/CachingScreenshot_EndpointURL.PNG)

6.  **messageSecurity** 요소에서 캐시의 액세스 키에 **authorizationInfo** 특성을 설정합니다. 액세스 키를 찾으려면 Azure 관리 포털에서 캐시를 선택합니다. 그런 다음 맨 아래 표시줄에서 **키 관리** 아이콘을 클릭합니다. **기본 액세스 키** 입력란 옆에 복사 단추를 클릭합니다.

    ![키 관리](./media/web-sites-web-forms-output-caching/CachingScreenshot_ManageAccessKeys.PNG)

출력 캐싱 사용
--------------

마지막 단계는 ASP.NET Web Forms 응용 프로그램에서 출력 캐싱을 사용하도록 페이지를 구성하는 것입니다. .ASPX 소스의 시작 부분에 **OutputCache** 특성을 추가하면 됩니다. 예를 들면 다음과 같습니다.

    <%@ OutputCache Duration="45" VaryByParam="*" %>

위의 예제는 45초 동안 페이지를 캐시합니다. **VaryByParam**이 "\*"로 설정되었으므로 이 캐시된 페이지 출력은 다른 쿼리 매개 변수가 전달되더라도 변경되지 않습니다. 하지만 다음 예는 "UserId" 매개 변수의 각 값에 대해 다른 버전의 페이지를 캐시합니다.

    <%@ OutputCache Duration="45" VaryByParam="UserId" %>  
