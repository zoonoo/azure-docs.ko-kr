이 항목에서는 앱을 모바일 및 웹 클라이언트 모두로 만드는 방법을 보여줍니다. 모바일 앱 및 웹앱을 만들고 모두에 대해 동일한 기본 데이터베이스를 사용합니다.

먼저, 새 모바일 앱 백엔드 및 새 모바일 앱 백엔드에 앱 데이터를 저장하는 간단한 *할 일 모음* 앱 모두를 만듭니다. 모바일 앱 백엔드는 서버쪽 비즈니스 논리를 지원하는 .NET 언어를 사용합니다. 클라이언트 앱에서 iOS, Windows, Xamarin iOS 및 Xamarin Android를 포함하여 모바일 앱에서 지원하는 모든 클라이언트 플랫폼을 사용할 수 있습니다.

그런 다음 모바일 앱과 동일한 데이터베이스를 사용하여 웹앱을 만듭니다. 이 자습서 끝에 동일한 데이터와 작업하는 웹 클라이언트 및 모바일 클라이언트가 있습니다.

> [!NOTE]
> Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
> 
> 

## 새 모바일 앱 백엔드 및 클라이언트 만들기
* 이 자습서의 [모바일 앱을 만들기] 단계를 따라 모바일 앱 백엔드 및 클라이언트를 모두 만들 수 있습니다. iOS, Windows, Xamarin iOS 및 Xamarin Android를 포함하여 모바일 앱에서 지원하는 모든 클라이언트 플랫폼을 사용할 수 있습니다.
* Azure에 모바일 앱 백엔드를 배포하고 모바일 클라이언트 응용 프로그램을 호스팅된 백엔드에 연결할 수 있는지 확인합니다. 모바일 앱 코드 프로젝트는 Entity Framework Code First를 사용하고 모바일 클라이언트 앱에서 첫 번째 REST 요청 이후 데이터베이스를 초기화합니다.

## Visual Studio에서 TodoList Web API를 게시합니다.
이 섹션에서는 샘플 웹 응용 프로그램 솔루션을 사용하여 새 웹앱을 만듭니다. 모바일 앱과 동일한 데이터베이스 스키마 이름 및 동일한 연결 문자열을 사용하여 샘플을 수정합니다.

> [!NOTE]
> 이 단계를 완료하기 전에 클라이언트에 연결하여 모바일 앱 백엔드 데이터베이스를 초기화했는지 확인합니다. 그렇지 않은 경우 웹앱에서 동일한 데이터베이스에 연결할 수 없습니다.
> 
> 

1. [Azure 포털](https://portal.azure.com/)에서, 모바일 앱과 동일한 리소스 그룹 및 호스팅 계획을 사용하여 새 웹앱을 만듭니다.
2. 샘플 솔루션 [MultiChannelToDo]를 다운로드하고 Visual Studio에서 엽니다. 솔루션은 웹 클라이언트 UI에 대한 웹 API 프로젝트와 웹 응용 프로그램 프로젝트를 모두 포함합니다.
3. 웹 API 프로젝트에서 MultiChannelToDoContext.cs를 편집 합니다. `OnModelCreating`에서, 모바일 앱 이름과 동일하도록 스키마 이름을 업데이트합니다.
   
        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore
4. 다음으로 모바일 앱 연결 문자열을 Azure 포털에서 가져옵니다.
   
   * 포털에서 모바일 앱을 선택하고 **사용자 코드**로 레이블이 지정된 파트를 클릭합니다.
   * 열리는 블레이드에서 **모든 설정**을 선택한 다음 **응용 프로그램 설정**을 선택합니다.
   * **연결 문자열**에서 **연결 문자열 표시**를 클릭합니다. 설정 **MS\_TableConnectionString**에 대한 값을 복사합니다. SQL 데이터베이스에 연결하기 위해 모바일 앱에서 사용되는 연결 문자열입니다.
5. Visual Studio에서 웹 API 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다. **Azure 웹앱**을 게시 대상으로 선택하고 위에서 만든 웹앱을 선택합니다. 게시 웹 마법사의 **설정**으로 이동할 때까지 **다음**을 클릭합니다.
6. **데이터베이스** 섹션에서 모바일 앱 연결 문자열을 **MultiChannelToDoContext**의 값으로 붙여넣습니다. **런타임 시 이 연결 문자열 사용** 확인란만을 선택합니다.
7. Web API가 Azure에 성공적으로 게시된 후 확인 페이지가 표시됩니다. 게시된 서비스에 대한 URL을 복사합니다.

## Visual Studio에서 TodoList 웹 클라이언트 UI를 게시합니다.
이 섹션에서는 AngularJS를 사용하여 구현된 샘플 웹 클라이언트 응용 프로그램을 사용합니다. 그런 다음 Azure에서 호스팅된 새 앱 서비스 웹앱으로 프로젝트를 게시하려면 Visual Studio를 사용합니다.

1. Visual Studio에서 프로젝트 **MultiChannelToDo.Web**을 엽니다. 파일 `js/service/ToDoService.js`를 편집하여 URL을 방금 게시한 웹 API에 추가합니다.
   
        var apiPath = "https://your-web-api-site-name.azurewebsites.net";
2. 프로젝트 **MultiChannelToDo.Web**을 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.
3. **웹 게시** 마법사에서 **Azure 웹앱**을 게시 대상으로 선택하고 데이터베이스 없이 새 웹앱을 만듭니다.
4. 프로젝트가 게시되면 브라우저에서 웹 UI가 표시됩니다.

## 모바일 및 웹앱 테스트
1. 웹 UI에서 일부 할 일 항목을 추가하거나 편집합니다.
   
    ![브라우저에서 웹 응용 프로그램의 보기](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)
2. [모바일 앱 만들기] 자습서에서 만든 모바일 앱을 실행합니다. 웹앱에서와 같이 동일한 할 일 항목이 표시됩니다.
   
    ![Xamarin 모바일 앱의 보기](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [Azure 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[모바일 앱 만들기]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md
[모바일 앱을 만들기]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->