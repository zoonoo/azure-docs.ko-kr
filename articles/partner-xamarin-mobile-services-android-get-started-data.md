<properties 
	pageTitle="데이터 시작(Xamarin.Android) - Azure 모바일 서비스" 
	description="Azure 모바일 서비스 Xamarin.Android 앱에서 데이터를 저장하고 액세스하는 방법에 대해 알아봅니다." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>

# 기존 앱에 모바일 서비스 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]	

<p>이 항목에서는 Azure 모바일 서비스를 사용하여 Xamarin.Android 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.</p>

> [AZURE.NOTE] 이 자습서는 Xamarin.Android 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 빠른 시작에서 완료한 여러 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작](/ko-kr/develop/mobile/tutorials/get-started-xamarin-android) 자습서를 완료하는 것이 좋습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Xamarin.Android 앱 프로젝트 다운로드][GitHub]
2. [모바일 서비스 만들기]
3. [저장소로 사용할 데이터 테이블 추가]
4. [모바일 서비스를 사용하도록 앱 업데이트]
5. [모바일 서비스에 대해 앱 테스트]

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5"%20target="_blank)을 참조하세요.

이 자습서에는 [Azure 모바일 서비스 구성 요소], [Xamarin.Android] 및 Android SDK 4.2 이상 버전이 필요합니다. 

> [AZURE.NOTE] 다운로드한 GetStartedWithData 프로젝트는 Android 4.2 이상 버전을 대상으로 지정해야 합니다. 하지만 모바일 서비스 SDK에 필요한 Android는 2.2 이상 버전이면 됩니다.

<h2><a name="download-app"></a>GetStartedWithData 프로젝트 다운로드</h2>

이 자습서는 Xamarin.Android 앱인 [GetStartedWithData 앱][GitHub]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 Android 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다. 

1.  `GetStartedWithData` 샘플 앱을 다운로드하여 컴퓨터에서 파일을 추출합니다. 

2. Xamarin Studio에서 **File**, **Open**을 차례로 클릭하고 GetStartedWithData 샘플 프로젝트의 압축을 푼 위치로 이동한 후 **XamarinTodoQuickStart.Android.sln**을 선택하여 엽니다.

3. **TodoActivity** 클래스를 찾아서 엽니다.

   	이 앱이 모바일 서비스에서 작동하기 위해 수행해야 하는 단계를 지정하는 `// TODO::` 주석이 있습니다.

5. **Run** 메뉴에서 **Start Without Debugging**을 클릭합니다. 에뮬레이터 또는 연결된 USB Android 장치를 선택하라는 메시지가 표시됩니다.

	> [AZURE.IMPORTANT] Android 휴대폰이나 Android 에뮬레이터를 사용하여 이 프로젝트를 실행할 수 있습니다. Android 휴대폰으로 실행하려면 휴대폰 전용 USB 드라이버를 다운로드해야 합니다.
	> 
	> Android 에뮬레이터에서 프로젝트를 실행하려면 AVD(Android Virtual Device)를 하나 이상 정의해야 합니다. AVD Manager를 사용하여 이러한 장치를 만들고 관리합니다.

6. 앱에서 _Complete the tutorial_ 등의 의미 있는 텍스트를 입력하고 **Add**를 클릭합니다.

   	![][13]

   	메모리 내 컬렉션에 저장된 텍스트가 저장되고 아래 목록에 표시됩니다.

<h2><a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>모바일 서비스에 새 테이블 추가</h2>

새 모바일 서비스에서 앱 데이터를 저장하려면 먼저 새 테이블을 만들어야 합니다.  

1. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

   	![][5]

   	**새 테이블 만들기** 대화 상자가 표시됩니다.

3. **테이블 이름**에 _TodoItem_을 입력한 후 확인 단추를 클릭합니다.

  	![][6]

  	기본 사용 권한 집합이 설정된 새 저장소 테이블 **TodoItem**이 생성됩니다. 앱의 모든 사용자가 테이블의 데이터에 액세스하고 변경할 수 있습니다. 

    > [AZURE.NOTE] 동일한 테이블 이름이 모바일 서비스 빠른 시작에서 사용됩니다. 하지만 각 테이블은 주어진 모바일 서비스에 한정된 스키마에 생성됩니다. 따라서 여러 모바일 서비스가 같은 데이터베이스를 사용할 때 발생하는 데이터 충돌이 방지됩니다.

4. 새 **TodoItem** 테이블을 클릭하고 데이터 행이 없는 것을 확인합니다.

5. **열** 탭을 클릭하고 자동으로 생성된 **id** 열 하나만 있는지 확인합니다.

  	이는 모바일 서비스의 테이블에 대한 최소 요구 사항입니다. 

    > [AZURE.NOTE] 모바일 서비스에서 동적 스키마를 사용하도록 설정하면 삽입 또는 업데이트 작업에서 JSON 개체를 모바일 서비스에 보낼 때 새 열이 자동으로 생성됩니다.

이제 새 모바일 서비스를 앱의 데이터 저장소로 사용할 준비가 되었습니다.

<h2><a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트</h2>

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다. 

1. **Azure 모바일 서비스**가 Components 폴더에 나열되지 않는 경우 **구성 요소**를 마우스 오른쪽 단추로 클릭하고 **추가 구성 요소 가져오기**를 선택한 후 **Azure 모바일 서비스**를 검색하여 가져올 수 있습니다.

  	모바일 서비스 SDK 구성 요소가 프로젝트에 추가됩니다.

2. **AndroidManifest.xml** 파일을 열고 다음 사용 권한 줄이 있는지 확인합니다.

		<uses-permission android:name="android.permission.INTERNET" />

  	이 줄이 있으면 앱이 Azure의 모바일 서비스에 액세스할 수 있습니다.

3. **솔루션** 창에서 **TodoActivity** 클래스를 열고 다음 코드 줄의 주석을 제거합니다. 

		using Microsoft.WindowsAzure.MobileServices;
 
4. 모바일 서비스와 바꿀 수 있도록 앱에서 현재 사용되고 있는 메모리 내 목록을 제거합니다. **TodoActivity** 클래스에서 기존 **todoItemList** 목록을 정의하는 다음 코드 줄을 주석으로 처리합니다.

		public List<TodoItem> todoItemList = new ArrayList<TodoItem>();

5. 이전 단계가 완료되면 프로젝트에서 빌드 오류가 표시됩니다.  `todoItemList` 변수가 사용되는 나머지 세 개 위치를 검색하여 표시된 섹션을 주석으로 처리합니다. 

6. 이제 모바일 서비스를 추가합니다. 다음 코드 줄의 주석 처리를 제거합니다.

        private MobileServiceClient client; // Mobile Service Client references
        private IMobileServiceTable<TodoItem> todoTable; // Mobile Service Table used to access data   

7. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

8. **대시보드** 탭을 클릭하여 **사이트 URL**을 기록한 후 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록합니다.

   	![][8]

  	앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

9. **Constants** 클래스에서 다음 멤버 변수의 주석 처리를 제거합니다.

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";
        
10. 위 변수의 **AppUrl** 및 **AppKey**를 위 관리 포털에서 검색된 값으로 바꿉니다.

11. **OnCreate** 메서드에서 **MobileServiceClient** 변수를 정의하는 다음 코드 줄의 주석 처리를 제거합니다.

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
		client = new MobileServiceClient(
			Constants.ApplicationURL,
			Constants.ApplicationKey).WithFilter(filter);

		// Get the Mobile Service Table instance to use
		todoTable = client.GetTable<TodoItem>();    

  	모바일 서비스에 액세스하는 데 사용되는 새 MobileServiceClient 인스턴스가 만들어집니다. 모바일 서비스에서 데이터 저장소에 프록시를 설정하는 데 사용되는 MobileServiceTable 인스턴스도 만들어집니다.

12. 파일 아래쪽에서 ProgressFilter 클래스를 찾아 주석 처리를 제거합니다. 이 클래스는 MobileServiceClient에서 네트워크 작업을 실행하는 동안  'loading' 표시기를 표시합니다.

13. **CheckItem** 메서드에서 다음 줄의 주석 처리를 제거합니다.

		try {
			await todoTable.UpdateAsync(item);
			if (item.Complete)
				adapter.Remove(item);
		} catch (Exception e) {
			CreateAndShowDialog(e, "Error");
		}

   	그러면 항목 업데이트가 모바일 서비스에 전송되고 선택한 항목이 어댑터에서 제거됩니다.
    
14. **AddItem** 메서드에서 다음 줄의 주석 처리를 제거합니다.
	
		try 
		{
			// Insert the new item
			await todoTable.InsertAsync(item);

			if (!item.Complete) 
				adapter.Add(item);			
		} 
		catch (Exception e) 
		{
			CreateAndShowDialog(e, "Error");
		}   		

  	이 코드는 새 항목을 만들어 원격 모바일 서비스의 테이블에 삽입합니다.

15. **RefreshItemsFromTableAsync** 메서드에서 다음 줄의 주석 처리를 제거합니다.

		try {
			// Get the items that weren't marked as completed and add them in the adapter
			var list = await todoTable.Where(item => item.Complete == false).ToListAsync ();

			adapter.Clear();

			foreach (TodoItem current in list)
				adapter.Add(current);
		} 
        catch (Exception e) 
        {
			CreateAndShowDialog(e, "Error");
		}

	그러면 모바일 서비스를 쿼리하여 완료로 표시되지 않은 모든 항목을 반환합니다. 바인딩을 위해 항목이 어댑터에 추가됩니다.
		

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

<h2><a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트</h2>

1. **Run** 메뉴에서 **Start Without Debugging**을 클릭하여 프로젝트를 시작합니다. 기존 에뮬레이터 이미지 또는 연결된 USB Android 장치를 선택하라는 메시지가 표시됩니다.

	그러면 Xamarin.Android를 사용하여 빌드된 앱이 실행되며 클라이언트 라이브러리를 사용하여 모바일 서비스의 항목을 반환합니다.

5. 이전처럼 의미 있는 텍스트를 입력한 후 **추가**를 클릭합니다.

   	This sends a new item as an insert to the mobile service.

3. [관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4. **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![][9]
  
   	이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

이제 Xamarin.Android용 **데이터 시작** 자습서를 마쳤습니다.

## 완성된 예 가져오기
[완성된 예제 프로젝트]를 다운로드합니다. 고유한 Azure 설정으로 **applicationURL** 및 **applicationKey** 변수를 업데이트해야 합니다. 

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 Xamarin.Android 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다. 

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해 보세요.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정]
  <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법에 대해 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화]
  <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다른 Xamarin.Android 자습서 중 하나를 시도해 보세요.

* [인증 시작]
	<br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>모바일 서비스를 통해 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->

[Windows 스토어 앱 다운로드]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[다음 단계]:#next-steps

<!-- Images. -->





[5]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-todoitem-data-browse.png
[13]: ./media/partner-xamarin-mobile-services-android-get-started-data/mobile-quickstart-startup-android.png

<!-- URL. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /ko-kr/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[페이징을 사용하여 쿼리 구체화]: /ko-kr/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-xamarin-android
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-xamarin-android
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-xamarin-android
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Azure 관리 포털]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/
[Android 앱 프로젝트 다운로드]: http://www.google.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=280125

[완성된 예제 프로젝트]: http://go.microsoft.com/fwlink/p/?LinkId=331302


<!--HONumber=42-->
