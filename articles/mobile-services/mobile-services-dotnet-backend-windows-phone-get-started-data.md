<properties 
	pageTitle="기존 앱에 모바일 서비스 추가(Windows Phone) | 모바일 개발자 센터" 
	description="모바일 서비스를 사용하여 Windows Phone 앱에서 데이터를 활용하는 방법을 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/12/2015" 
	ms.author="wesmc"/>

# 기존 앱에 모바일 서비스 추가

##개요

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../../includes/mobile-services-selector-get-started-data-legacy.md)]

이 항목에서는 Azure 모바일 서비스를 Windows Phone 8.1 Silverlight 앱에 대한 백 엔드 데이터 소스로 추가하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱용 Visual Studio 프로젝트를 다운로드하고, 새 모바일 서비스를 만들고, 모바일 서비스를 앱과 통합하고, 앱을 실행할 때 변경된 데이터를 확인합니다. Windows Phone 스토어 8.1 앱에 모바일 서비스를 추가하려면 [자습서의 이 섹션](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md)을 참조하세요.

이 자습서에서 만드는 모바일 서비스는 모바일 서비스의 .NET 런타임을 지원합니다. 따라서 사용자는 모바일 서비스에서 서버 쪽 비즈니스 논리에 .NET 언어 및 Visual Studio를 사용할 수 있습니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전](mobile-services-windows-phone-get-started-data.md)을 참조하세요.


##필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

+ Visual Studio 2013 업데이트 2 이상 버전
+ Microsoft Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요. 

##GetStartedWithData 프로젝트 다운로드

이 자습서는 Visual Studio 2013용 Windows Phone Silverlight 8.1 앱 프로젝트인 [GetStartedWithMobileServices 앱](https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72)을 기반으로 합니다.

1. [개발자 코드 샘플 사이트](https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72)(영문)에서 GetStartedWithMobileServices 샘플 앱의 C# 버전을 다운로드합니다. 

	>[AZURE.NOTE]Windows Phone Silverlght 8.1 앱을 만들려면 다운로드한 Windows Phone Silverlight 8 앱 프로젝트의 대상 OS를 Windows Phone 8.1로 변경하기만 하면 됩니다. Windows Phone 스토어 앱을 만들려면 GetStartedWithData 샘플 앱 프로젝트의 [Windows Phone 스토어 앱 버전](http://go.microsoft.com/fwlink/p/?LinkId=397372)을 다운로드합니다.

2. Visual Studio를 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 클릭하여 Visual Studio를 관리자 권한으로 실행합니다.

3. Visual Studio에서 다운로드한 프로젝트를 열고 MainPage.xaml.cs 파일을 검사합니다.

   	추가된 **TodoItem** 개체가 메모리 내 **ObservableCollection&lt;TodoItem&gt;**에 저장됩니다.

4. Visual Studio에서 앱의 배포 대상을 선택합니다. Windows Phone 장치에 또는 Windows Phone SDK에 포함된 에뮬레이터 중 하나에 배포할 수 있습니다. 이 자습서에서는 에뮬레이터에 배포합니다.

5. **F5** 키를 누릅니다. 디버깅용 앱이 빌드되고 배포되어 실행됩니다.

6. 앱에서 텍스트 상자에 텍스트를 입력하고 **저장**을 클릭하여 앱의 메모리에 몇몇 항목을 저장합니다.

   	![메모리 내 저장소가 있는 앱](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png)

   	각 `TodoItem`에 대한 텍스트가 항목 완료를 표시할 수 있는 확인란과 함께 새로 고침 단추 아래에 표시됩니다.

##새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]


##모바일 서비스 프로젝트 다운로드 및 솔루션에 추가

1. 아직 하지 않은 경우 [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934) 이상 버전을 다운로드하여 설치합니다.
 
2. [Azure 관리 포털](https://manage.windowsazure.com/)에서 새 모바일 서비스를 클릭하고 빠른 시작 페이지에서 **Windows** 플랫폼을 클릭하고 나서 **시작** 아래에서 **기존 Windows 또는 Windows Phone 앱 연결**을 확장합니다.

    ![모바일 서비스 프로젝트 다운로드](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png)

4. **서비스를 다운로드하고 클라우드에 게시** 아래에서 **다운로드**를 클릭합니다.

	모바일 서비스를 구현하는 Visual Studio 프로젝트가 다운로드됩니다.

4. 다운로드한 개인 설정된 서비스 시작 솔루션의 압축을 풀고 Zip 파일에 있던 폴더를 Getting Started with Data 솔루션 파일(.sln)이 있는 동일한 **C#** 디렉터리로 복사합니다. 이렇게 하면 NuGet 패키지 관리자에서 모든 패키지의 동기화를 더욱 쉽게 유지할 수 있습니다.

5. Visual Studio의 솔루션 탐색기에서 Getting Started with Data Windows 스토어 앱용 솔루션을 마우스 오른쪽 단추로 클릭합니다. **추가**를 클릭한 후 **기존 프로젝트**를 클릭합니다.

6. **기존 프로젝트 추가** 대화 상자에서, **C#** 디렉터리로 이동한 모바일 서비스 프로젝트 폴더로 이동하고, 서비스 하위 디렉터리에서 C# 프로젝트 파일(.csproj)을 선택하고, **열기**를 클릭하여 프로젝트를 솔루션에 추가합니다.

7. Visual Studio의 솔루션 탐색기에서, 방금 추가한 서비스 프로젝트를 마우스 오른쪽 단추로 클릭하고 **빌드**를 클릭하여 오류 없이 빌드되는지 확인합니다. 빌드 중에, 프로젝트에서 참조된 일부 NuGet 패키지를 NuGet 패키지 관리자에서 복원해야 할 수 있습니다.

8. 서비스 프로젝트를 다시 마우스 오른쪽 단추로 클릭합니다. 이번에는 **디버그** 상황에 맞는 메뉴에서 **새 인스턴스 시작**을 클릭합니다.

    Visual Studio에서 서비스용 기본 웹 페이지가 열립니다. 기본 웹 페이지에서 모바일 서비스의 메서드를 테스트하려면 **지금 시도**를 클릭할 수 있습니다.

    ![모바일 서비스 지금 시도 페이지](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png)

    Visual Studio에서는 기본적으로 IIS Express에 로컬로 모바일 서비스를 호스트합니다. 작업 표시줄에서 IIS Express용 트레이 아이콘을 마우스 오른쪽 단추로 클릭하여 확인할 수 있습니다.


##모바일 서비스를 사용하도록 Windows Phone 앱 업데이트

이 섹션에서는 모바일 서비스를 응용 프로그램용 백 엔드 서비스로 사용하기 위해 Windows Phone 앱을 업데이트합니다.


1. Visual Studio의 솔루션 탐색기에서 Windows Phone 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다.

2. NuGet 패키지 관리 대화 상자의 온라인 패키지 컬렉션에서 **WindowsAzure.MobileServices**를 검색하고 클릭하여 Azure 모바일 서비스 Nuget 패키지를 설치합니다. 그런 다음 대화 상자를 닫습니다.

    ![NuGet 패키지 설치](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png)

3. 다시 Azure 관리 포털로 돌아와서 **앱 연결 및 서비스에 데이터 저장**이라는 레이블의 단계를 찾습니다. `MobileServiceClient` 연결을 만드는 코드 조각을 복사합니다.

    ![앱 조각 연결](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png)

4. Visual Studio에서 App.xaml.cs를 엽니다. `App` 클래스 정의의 시작 부분에 코드 조각을 붙여넣습니다. 또한 다음 `using` 문을 이 파일 위에 추가하고 파일을 저장합니다.

		using Microsoft.WindowsAzure.MobileServices;

5. Visual Studio에서 MainPage.xaml.cs를 열고 파일 맨 위에 다음 using 문을 추가합니다.

		using Microsoft.WindowsAzure.MobileServices;

6. Visual Studio의 MainPage.xaml.cs에서 `MainPage` 클래스 정의를 다음 정의와 바꾸고 파일을 저장합니다.

    이 코드는 앱이 데이터를 로컬의 메모리 내에 저장하는 대신 서비스에서 제공하는 테이블에 저장하도록 모바일 서비스 SDK를 사용합니다. 세 가지 기본 메서드는 `InsertTodoItem`. `RefreshTodoItems` 및 `UpdateCheckedTodoItem`입니다. 이 세 가지 메서드를 사용하면 데이터 컬렉션을 Azure의 테이블에 비동기로 삽입하고 쿼리하며 업데이트할 수 있습니다.

        public sealed partial class MainPage : PhoneApplicationPage
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable 
                    .ToCollectionAsync(); 
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }


##로컬로 호스트한 서비스에 대해 Windows Phone 앱 테스트</h2>

이 섹션에서는 Visual Studio를 사용하여 개발용 워크스테이션에서 로컬로 앱과 모바일 서비스를 테스트합니다. IIS Express에 로컬로 호스트한 모바일 서비스를 Windows Phone 장치 또는 Windows Phone 에뮬레이터 중 하나에서 테스트하려면 워크스테이션의 IP 주소 및 포트에 연결할 수 있도록 IIS Express와 워크스테이션을 구성해야 합니다. Windows Phone 장치와 에뮬레이터는 비 로컬 네트워크 클라이언트로서 연결됩니다.

#### 원격 연결이 가능하도록 IIS Express 구성

[AZURE.INCLUDE [mobile-services-how-to-configure-iis-express](../../includes/mobile-services-how-to-configure-iis-express.md)]

#### IIS Express에서 모바일 서비스에 대해 앱 테스트

6. Visual Studio에서 App.xaml.cs 파일을 열고 방금 파일에 붙여 넣은 `MobileService` 정의를 주석으로 처리합니다. 워크스테이션에서 구성한 IP 주소와 포트를 기반으로 연결할 수 있도록 새 정의를 추가합니다. 그런 다음 파일을 저장합니다. 코드는 다음과 유사합니다.

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        


7. Visual Studio에서 F7 키를 누르거나 빌드 메뉴에서 **솔루션 빌드**를 클릭하여 Windows Phone 앱과 모바일 서비스를 모두 빌드합니다. Visual Studio의 출력 창에서 두 프로젝트가 오류 없이 빌드되는지 확인합니다.

8. Visual Studio에서 F5 키를 누르거나 디버그 메뉴에서 **디버깅 시작**을 클릭하여 IIS Express에서 로컬로 앱을 실행하고 모바일 서비스를 호스트합니다.

    >[AZURE.NOTE]**관리자 권한으로 실행** 옵션을 사용하여 Visual Studio를 실행해야 합니다. 그렇지 않으면 IIS Express에서 applicationhost.config 변경 사항을 로드하지 못할 수 있습니다.

9. 새 todoitem의 텍스트를 입력합니다. 그런 다음 **Save**를 클릭합니다. IIS Express에서 로컬로 호스트한 모바일 서비스에서 만든 데이터베이스에 새 todoItem이 삽입됩니다. 항목 중 하나의 확인란을 클릭하여 완료로 표시합니다.

10. Visual Studio에서 앱의 디버깅을 중지합니다. 서버 탐색기를 열고 데이터 연결을 확장하여 백 엔드 서비스에 대해 만든 데이터베이스에서 변경 사항을 확인합니다. **MS_TableConnectionString** 아래에서 TodoItems 테이블을 마우스 오른쪽 단추로 클릭하고 **테이블 데이터 표시**를 클릭합니다.

    ![테이블의 데이터 표시]([14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png)

11. 로컬에서 호스트한 모바일 서비스의 테스트를 완료했으면 전에 만든, 워크스테이션에서 포트를 여는 Windows 방화벽 규칙을 삭제합니다.


##Azure에 모바일 서비스 게시

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Azure에 게시된 모바일 서비스 테스트

1. Visual Studio에서 App.xaml.cs를 엽니다. 로컬로 호스팅된 모바일 서비스에 연결하는 `MobileServiceClient`을(를) 만드는 코드를 주석으로 처리합니다. Azure의 서비스에 연결되는 `MobileServiceClient`을(를) 만드는 코드의 주석 처리를 제거합니다. 파일 변경 사항을 저장합니다.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2. Visual Studio에서 F5 키를 누르거나 디버그 메뉴에서 **디버깅 시작**을 클릭합니다. Azure에서 원격으로 호스트한 모바일 서비스에 연결하기 위해 앱을 실행하기 전에, 이전 변경 사항으로 앱이 다시 빌드됩니다.

3. 새 todoitem을 몇 개 입력하고 각각에 대해 **저장**을 클릭합니다. 확인란을 클릭하여 몇몇 새 항목을 완료합니다. 각각의 새 todoItem이 저장되고, Azure 관리 포털에서 모바일 서비스에 대해 전에 구성한 SQL 데이터베이스에서 업데이트됩니다.

    변경 사항이 Azure의 데이터베이스에서 유지되었는지 확인하려면 앱을 다시 시작할 수 있습니다. Azure 관리 포털 또는 Visual Studio의 SQL Server 개체 탐색기를 사용하여 데이터베이스를 검토할 수도 있습니다. 다음의 두 단계에서는 Azure 관리 포털을 사용하여 데이터베이스의 변경 사항을 확인합니다.


4. Azure 관리 포털에서 모바일 서비스와 연결된 데이터베이스에 대해 관리를 클릭합니다.

    ![SQL 데이터베이스 관리](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png)

5. 관리 포털에서 앱에서 변경한 내용을 표시하는 쿼리를 실행합니다. 쿼리는 다음 쿼리와 유사하지만 `todolist` 대신 데이터베이스 이름을 사용하게 됩니다.

        SELECT * FROM [todolist].[todoitems]

    ![SQL 데이터베이스 쿼리](./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png)

이제 **데이터 시작** 자습서를 마쳤습니다.

##다음 단계

이 자습서에서는 Windows Phone 8 앱이 .Net 런타임을 사용해 빌드한 모바일 서비스의 데이터로 작업하도록 하는 과정의 기본 사항을 설명했습니다. 다른 자습서도 시도해보세요.

* [인증 시작] <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 푸시 알림 추가]() <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 방법 개념 참조] <br/>.NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.
  


<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[인증 시작]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: https://code.msdn.microsoft.com/Add-Azure-Mobile-to-a-8b906f72
[모바일 서비스 .NET 방법 개념 참조]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient class]: http://go.microsoft.com/fwlink/p/?LinkId=302030
[How to add a new Windows Firewall port rule]: http://go.microsoft.com/fwlink/?LinkId=392240
   

<!---HONumber=July15_HO2-->