<properties
	pageTitle="기존 앱에 모바일 서비스 추가(Xamarin.iOS) | Azure 모바일 서비스"
	description="Azure 모바일 서비스 Xamarin.iOS 앱에서 데이터를 저장하고 액세스하는 방법에 대해 알아봅니다."
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# 기존 앱에 모바일 서비스 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

이 항목에서는 Azure 모바일 서비스를 사용하여 Xamarin.iOS 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

> [AZURE.NOTE]이 자습서는 Xamarin.iOS 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 빠른 시작에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작](/develop/mobile/tutorials/get-started-xamarin-ios) 자습서를 완료하는 것이 좋습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [Xamarin.iOS 앱 프로젝트 다운로드][GitHub]
2. [모바일 서비스 만들기]
3. [저장소로 사용할 데이터 테이블 추가]
4. [모바일 서비스를 사용하도록 앱 업데이트]
5. [모바일 서비스에 대해 앱 테스트]

이 자습서에는 [Azure 모바일 서비스 구성 요소], [XCode 6.0][Install Xcode], [Xamarin.iOS] 및 iOS 7.0 이상 버전이 필요합니다.

> [AZURE.IMPORTANT]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank)을 참조하십시오.

## <a name="download-app"></a>GetStartedWithData 프로젝트 다운로드

이 자습서는 Xamarin.iOS 앱인 [GetStartedWithData 앱][GitHub]을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 Xamarin.iOS 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1. [GetStartedWithData 앱][GitHub]을 다운로드합니다.

2. Xamarin Studio에서 다운로드한 프로젝트를 열고 **TodoService** 클래스를 검사합니다.

   	이 앱이 모바일 서비스에서 작동하기 위해 수행해야 하는 단계를 지정하는 여러 개의 **// TODO** 주석이 있습니다.

3. **Run** 메뉴로 이동한 후 **Start Without Debugging**을 선택하여 앱을 시작합니다.

4. 앱에서 텍스트 상자에 일부 텍스트를 입력한 후 **+** 단추를 클릭합니다.

   	![][0]

   	저장된 텍스트가 아래 목록에 표시됩니다.

## <a name="create-service"></a>관리 포털에서 새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>모바일 서비스에 새 테이블 추가

새 모바일 서비스에서 앱 데이터를 저장하려면 먼저 연결된 SQL 데이터베이스 인스턴스에 새 테이블을 만들어야 합니다.

1. 관리 포털에서 **Mobile Services**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

   	![][5]

   	**Create new table** 대화 상자가 표시됩니다.

3. **Table name**에 _TodoItem_을 입력한 후 확인 단추를 클릭합니다.

  	![][6]

  	기본 사용 권한 집합이 설정된 새 저장소 테이블 **TodoItem**이 생성됩니다. 앱의 모든 사용자가 테이블의 데이터에 액세스하고 변경할 수 있습니다.

    > [AZURE.NOTE]모바일 서비스 빠른 시작에 같은 테이블 이름이 사용됩니다. 하지만 각 테이블은 주어진 모바일 서비스에 특정된 스키마에 생성됩니다. 이렇게 하면 여러 모바일 서비스가 같은 데이터베이스를 사용할 때 발생하는 데이터 충돌을 예방할 수 있습니다.

4. 새 **TodoItem** 테이블을 클릭하고 데이터 행이 없는 것을 확인합니다.

5. **열** 탭을 클릭하고 자동으로 생성된 **id** 열 하나만 있는지 확인합니다.

	  이 부분이 모바일 서비스의 테이블 최소 요구 사항입니다.

    > [AZURE.NOTE]모바일 서비스에서 동적 스키마를 사용하면 삽입 또는 업데이트 작업에서 JSON 개체를 모바일 서비스에 보낼 때 새 열이 자동으로 생성됩니다.

이제 새 모바일 서비스를 앱의 데이터 저장소로 사용할 준비가 되었습니다.

## <a name="update-app"></a>데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1. **Azure 모바일 서비스**가 Components 폴더에 나열되지 않는 경우 **구성 요소**를 마우스 오른쪽 단추로 클릭하고 **추가 구성 요소 가져오기**를 선택한 후 **Azure 모바일 서비스**를 검색하여 가져올 수 있습니다.

2. Xamarin Studio의 솔루션 보기에서 **TodoService** 클래스를 열고 다음 `using` 문의 주석 처리를 제거합니다.

        using Microsoft.WindowsAzure.MobileServices;

3. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

4. **대시보드** 탭을 클릭하여 **사이트 URL**을 기록해 두고 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록해 둡니다.

   	![][8]

5. **Constants** 클래스에서 다음 상수의 주석 처리를 제거합니다.

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. 위 상수의 **AppUrl** 및 **AppKey**를 위 관리 포털에서 검색된 값으로 바꿉니다.

7. **TodoService** 클래스에서 다음 코드 줄의 주석 처리를 제거합니다.

        private MobileServiceClient client;

   	서비스에 연결하는 MobileServiceClient를 나타내는 속성이 생성됩니다.

8. **TodoService** 클래스에서 다음 코드 줄의 주석 처리를 제거합니다.

        private IMobileServiceTable<TodoItem> todoTable;  

   	이렇게 하면 모바일 서비스 테이블을 나타내는 속성이 생성됩니다.

9. **TodoService** 생성자에서 다음 줄의 주석 처리를 제거합니다.

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this);
		todoTable = client.GetTable<TodoItem>();

    모바일 서비스 클라이언트 인스턴스 및 TodoItem 테이블 인스턴스가 생성됩니다.

10. **TodoService**의 **RefreshDataAsync** 메서드에서 다음 줄의 주석 처리를 제거합니다.

			// TODO:: Uncomment these lines to use Mobile Services
			try
	    {
				// This code refreshes the entries in the list view by querying the TodoItems table.
				// The query excludes completed TodoItems
				Items = await todoTable
					.Where (todoItem => todoItem.Complete == false).ToListAsync();
			}
	    catch (MobileServiceInvalidOperationException e)
	    {
				Console.Error.WriteLine (@"ERROR {0}", e.Message);
				return null;
			}

    이렇게 하면 아직 완료되지 않은 모든 작업을 반환하는 쿼리가 생성됩니다.

11. **InsertTodoItemAsync** 메서드를 찾은 후 다음 줄의 주석 처리를 제거합니다.

		await todoTable.InsertAsync(todoItem);

    이 코드는 삽입 요청을 모바일 서비스에 보냅니다.

13. **CompleteItemAsync** 메서드를 찾은 후 다음 줄의 주석 처리를 제거합니다.

		await todoTable.UpdateAsync(item);

   	이 코드는 완료로 표시된 TodoItems를 제거합니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

## <a name="test-app"></a>새 모바일 서비스에 대해 앱 테스트

1. Xamarin Studio의 기본 콤보 상자 중 하나에서 배포할 에뮬레이터 또는 장치를 선택하고 **Run** 메뉴로 이동한 후 **Start WithoutDebugging**을 선택하여 앱을 시작합니다.

   	Xamarin.iOS로 빌드되었으며 모바일 서비스에서 항목을 쿼리하는 Azure 모바일 서비스 클라이언트가 실행됩니다.

2. 앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **+** 단추를 클릭합니다.

   	그러면 새 항목이 모바일 서비스에 삽입으로 전송됩니다.

3. [관리 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4. **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![][9]

   	이제 **TodoItem** 테이블에 모바일 서비스에서 생성된 id 값을 가진 데이터가 포함되었으며 해당 열이 앱의 TodoItem 클래스와 일치하도록 테이블에 자동으로 추가되었습니다.

이제 Xamarin.iOS용 **데이터 시작** 자습서를 마쳤습니다.

## 완성된 예 가져오기
[완성된 예제 프로젝트]를 다운로드합니다. 고유한 Azure 설정으로 **applicationURL** 및 **applicationKey** 변수를 업데이트해야 합니다.

## <a name="next-steps"> </a>다음 단계

이 자습서에서는 iOS 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다.

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보십시오.

* [스크립트를 사용하여 데이터 유효성 검사 및 수정] <br/>모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

* [페이징을 사용하여 쿼리 구체화] <br/>쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다른 iOS 자습서 중 하나를 시도해 보십시오.

* [인증 시작] <br/>앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] <br/>모바일 서비스를 사용하여 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[모바일 서비스 만들기]: #create-service
[저장소로 사용할 데이터 테이블 추가]: #add-table
[모바일 서비스를 사용하도록 앱 업데이트]: #update-app
[모바일 서비스에 대해 앱 테스트]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[스크립트를 사용하여 데이터 유효성 검사 및 수정]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[페이징을 사용하여 쿼리 구체화]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[인증 시작]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[푸시 알림 시작]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[관리 포털]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/

[완성된 예제 프로젝트]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Xamarin.iOS]: http://xamarin.com/download
 

<!---HONumber=July15_HO3-->