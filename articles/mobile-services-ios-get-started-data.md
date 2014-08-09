<properties linkid="develop-mobile-tutorials-get-started-with-data-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (iOS) | Mobile Dev Center" metaKeywords="Azure iOS data, Azure mobile services data, " description="Learn how to get started using Mobile Services to leverage data in your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스에서 데이터 시작
=============================

[Windows 스토어 C#](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-data-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에서 데이터를 활용하는 방법을 보여 줍니다. 이 자습서에서는 데이터를 메모리에 저장하는 앱을 다운로드하여 새 모바일 서비스를 만들고 모바일 서비스를 앱과 통합한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 내용을 확인합니다.

**참고**

이 자습서는 iOS 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 빠른 시작에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-ios) 자습서를 완료하는 것이 좋습니다.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1.  [iOS 앱 프로젝트 다운로드](#download-app)
2.  [모바일 서비스 만들기](#create-service)
3.  [저장소로 사용할 데이터 테이블 추가](#add-table)
4.  [모바일 서비스를 사용하도록 앱 업데이트](#update-app)
5.  [모바일 서비스에 대해 앱 테스트](#test-app)

이 자습서에서는 [모바일 서비스 iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)와 [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532) 및 iOS 5.0 이상 버전이 필요합니다.

**참고**

이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A756A2826&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-ios%2F)을 참조하십시오.

프로젝트 다운로드GetStartedWithData 프로젝트 다운로드
-----------------------------------------------------

이 자습서는 iOS 앱인 [GetStartedWithData 앱](http://go.microsoft.com/fwlink/p/?LinkId=268622)을 기반으로 합니다. 이 앱의 UI는 모바일 서비스 iOS 빠른 시작에서 생성된 앱과 동일합니다. 단, 추가된 항목이 로컬에서 메모리에 저장된다는 점은 예외입니다.

1.  GetStartedWithData [샘플 앱](http://go.microsoft.com/fwlink/p/?LinkId=268622)을 다운로드합니다.

2.  다운로드한 프로젝트를 Xcode에서 열고 QSTodoService.m 파일을 검사합니다.

	Notice that there are eight **// TODO** comments that specify the steps you must take to make this app work with your mobile service.

3.  **Run** 단추(또는 Command+R 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

4.  앱에서 텍스트 상자에 일부 텍스트를 입력한 후 **+** 단추를 클릭합니다.

   	![](./media/mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png)

   	저장된 텍스트가 아래 목록에 표시됩니다.

모바일 서비스 만들기관리 포털에서 새 모바일 서비스 만들기
---------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

새 테이블 추가모바일 서비스에 새 테이블 추가
--------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data-2](../includes/mobile-services-create-new-service-data-2.md)]

앱 업데이트데이터 액세스에 모바일 서비스를 사용하도록 앱 업데이트
-----------------------------------------------------------------

이제 모바일 서비스를 사용할 수 있으므로 로컬 컬렉션 대신 모바일 서비스에 항목을 저장하도록 앱을 업데이트할 수 있습니다.

1.  [모바일 서비스 iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)를 아직 설치하지 않은 경우 지금 설치합니다.

2.  Xcode의 Project Navigator에서 Quickstart 폴더에 있는 TodoService.m 및 TodoService.h 파일을 모두 열고 다음과 같은 import 문을 추가합니다.

         #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>  

3.  ToDoService.h 파일에서 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create an MSClient property comment in the #interface declaration for the TodoService. 

   	이 주석 뒤에 다음 코드 줄을 추가합니다.

        @property (nonatomic, strong)   MSClient *client;

   	이렇게 하면 서비스에 연결되는 MSClient를 나타내는 속성이 생성됩니다.

4.  TodoService.m 파일에서 다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Create an MSTable property for your items.  

   	이 주석 뒤에 @interface 선언 안에 다음 코드 줄을 추가합니다.

        @property (nonatomic, strong)   MSTable *table;

   	이렇게 하면 모바일 서비스 테이블을 나타내는 속성이 생성됩니다.

5.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

6.  **대시보드** 탭을 클릭하여 **사이트 URL**을 기록해 두고 **키 관리**를 클릭하여 **응용 프로그램 키**를 기록해 둡니다.

   	![](./media/mobile-services-ios-get-started-data/mobile-dashboard-tab.png)

  	앱 코드에서 모바일 서비스에 액세스할 때 이 값이 필요합니다.

1.  Xcode에서 TodoService.m을 열고 다음과 같은 주석 처리된 코드 줄을 찾습니다.

         // Initialize the Mobile Service client with your URL and key.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

         self.client = [MSClient clientWithApplicationURLString:@"APPURL" applicationKey:@"APPKEY"];

    그러면 모바일 서비스 클라이언트 인스턴스가 만들어집니다.

2.  이 코드에서 **APPURL** 및 **APPKEY**의 값을 6단계에서 얻은 모바일 서비스의 URL 및 응용 프로그램 키로 바꿉니다.

3.  다음과 같은 주석 처리된 코드 줄을 찾습니다.

         // Create an MSTable instance to allow us to work with the TodoItem table.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

         self.table = [self.client tableWithName:@"TodoItem"];

    그러면 TodoItem 테이블 인스턴스가 만들어집니다.

4.  다음과 같은 주석 처리된 코드 줄을 찾습니다.

 	    // Create a predicate that finds items where complete is false comment in the refreshDataOnSuccess method.

    이 주석 뒤에 다음 코드 줄을 추가합니다.

        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

    이렇게 하면 아직 완료되지 않은 모든 작업을 반환하는 쿼리가 생성됩니다.

1.  다음과 같은 주석 처리된 코드 줄을 찾습니다.

        // Query the TodoItem table and update the items property with the results from the service.

 이 주석 및 후속 **completion** 블록 호출을 다음 코드로 바꿉니다.

        // Query the TodoItem table and update the items property with the results from the service
        [self.table readWithPredicate:predicate completion:^(NSArray *results, NSInteger totalCount, NSError *error) 
        {
           self.items = [results mutableCopy];
           completion();
        }]; 

1.  **addItem** 메서드를 찾아 이 메서드의 본문을 다음 코드로 바꿉니다.

        // Insert the item into the TodoItem table and add to the items array on completion
        [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
            NSUInteger index = [items count];
            [(NSMutableArray *)items insertObject:item atIndex:index];
         
            // Let the caller know that we finished
            completion(index);
        }];

    이 코드는 삽입 요청을 모바일 서비스에 보냅니다.

2.  **completeItem** 메서드를 찾아 이 메서드의 본문을 다음 코드로 바꿉니다.

        // Update the item in the TodoItem table and remove from the items array on completion
        [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {

            // TODO
            // Get a fresh index in case the list has changed
            NSUInteger index = [items indexOfObjectIdenticalTo:mutable];

            [mutableItems removeObjectAtIndex:index];

            // Let the caller know that we have finished
            completion(index);
        }]; 

	이 코드는 완료로 표시된 TodoItems를 제거합니다.

이제 앱이 백 엔드 저장소에 모바일 서비스를 사용하도록 업데이트되었으므로 모바일 서비스에 대해 앱을 테스트해야 합니다.

앱 테스트모바일 서비스에 대해 앱 테스트
---------------------------------------

1.  Xcode에서 배포 대상 에뮬레이터(iPhone 또는 iPad)를 선택하고 **Run** 단추(또는 Command+R 키)를 눌러 프로젝트를 다시 빌드하고 앱을 시작합니다.

   	This executes your Azure Mobile Services client, built with the iOS SDK, that queries items from your mobile service.

2.  앞에서 한 것처럼 텍스트 상자에 텍스트를 입력한 후 **+** 단추를 클릭합니다.

   	새 항목이 삽입으로 모바일 서비스에 전송됩니다.

3.  [관리 포털](https://manage.windowsazure.com/)에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.

4.  **데이터** 탭을 클릭한 후 **찾아보기**를 클릭합니다.

   	![](./media/mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png)
  
   	이제 **TodoItem** 테이블에는 모바일 서비스에서 생성한 ID 값을 비롯한 데이터가 포함되었으며, 앱의 TodoItem 클래스와 일치시키기 위해 자동으로 테이블에 열이 추가되었습니다.

이제 iOS용 **데이터 시작** 자습서를 마쳤습니다.

다음 단계
---------

이 자습서에서는 iOS 앱에서 모바일 서비스의 데이터로 작업하기 위한 기본 사항에 대해 설명했습니다.

다음에는 이 자습서에서 만든 GetStartedWithData 앱을 기반으로 하는 다음 자습서 중 하나를 완료해보십시오.

-   [스크립트를 사용하여 데이터 유효성 검사 및 수정](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    모바일 서비스에서 서버 스크립트를 사용하여 앱에서 전송된 데이터의 유효성을 검사하고 변경하는 방법을 자세히 알아봅니다.

-   [페이징을 사용하여 쿼리 구체화](/en-us/develop/mobile/tutorials/add-paging-to-data-ios)
    쿼리에 페이징을 사용하여 단일 요청으로 처리되는 데이터 양을 제어하는 방법을 알아봅니다.

데이터 시리즈를 완료한 후에는 다른 iOS 자습서 중 하나를 시도해 보십시오.

-   [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    앱 사용자를 인증하는 방법을 알아봅니다.

-   [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-ios)
    모바일 서비스를 사용하여 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.


