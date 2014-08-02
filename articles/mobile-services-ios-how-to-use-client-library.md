<properties linkid="mobile-services-how-to-ios-client" urlDisplayName="iOS Client Library" pageTitle="How to use the iOS client library - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service iOS client library, iOS client library" description="Learn how to use the iOS client library for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use the iOS client library for Mobile Services" authors="glenga" solutions="" manager="" editor="" />

모바일 서비스용 iOS 클라이언트 라이브러리를 사용하는 방법
=========================================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework")[HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

이 가이드에서는 Azure 모바일 서비스용 iOS 클라이언트를 사용하여 일반적인 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Objective-C로 작성되었으며 [모바일 서비스 SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)가 필요합니다. 이 자습서에는 [iOS SDK](https://developer.apple.com/xcode)(영문)도 필요합니다. 여기서 다루는 시나리오에는 데이터 쿼리, 삽입, 업데이트, 삭제 및 사용자 인증과 오류 처리가 포함됩니다. 모바일 서비스를 처음 접하는 경우 먼저 [모바일 서비스 빠른 시작](/en-us/develop/mobile/tutorials/get-started-ios)을 완료하는 것이 좋습니다. 빠른 시작 자습서를 참조하여 계정을 구성하고 첫 모바일 서비스를 만들 수 있습니다.

목차
----

-   [모바일 서비스 정의](#what-is)
-   [개념](#concepts)
-   [설정 및 필수 조건](#Setup)
-   [방법: 모바일 서비스 클라이언트 만들기](#create-client)
-   [방법: 테이블 참조 만들기](#table-reference)
-   [방법: 모바일 서비스에서 데이터 쿼리](#querying)
    -   [반환된 데이터 필터링](#filtering)
    -   [MSQuery 개체 사용](#query-object)
    -   [특정 열 선택](#selecting)
-   [방법: 모바일 서비스에 데이터 삽입](#inserting)
-   [방법: 모바일 서비스의 데이터 수정](#modifying)
-   [방법: 사용자 인터페이스에 데이터 바인딩](#binding)
-   [방법: 사용자 인증](#authentication)
-   [방법: 오류 처리](#errors)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

설정 및 필수 조건
-----------------

이 가이드에서는 이미 모바일 서비스 및 테이블을 만들었다고 가정합니다. 자세한 내용은 [테이블 만들기](http://msdn.microsoft.com/en-us/library/windowsazure/jj193162.aspx)를 참조하십시오. 이 항목의 예에서는 이름이 `ToDoItem`이고 다음 열이 포함된 테이블을 사용합니다.

-   `id`
-   `text`
-   `complete`
-   `duration`

iOS 응용 프로그램을 처음 만드는 경우 응용 프로그램의 **Link Binary With Libraries** 설정에 `WindowsAzureMobileServices.framework`를 추가해야 합니다.

또한 해당 파일이나 응용 프로그램의 .pch 파일에서 다음 참조를 추가해야 합니다.

     #import <WindowsAzureMobileServices/WindowsAzureMobileServices.h>

클라이언트 만들기방법: 모바일 서비스 클라이언트 만들기
------------------------------------------------------

다음 코드는 모바일 서비스에 액세스하는 데 사용되는 모바일 서비스 클라이언트 개체를 만듭니다.

    MSClient *client = [MSClient clientWithApplicationURLString:@"MobileServiceUrl" applicationKey:@"AppKey"]

위 코드에서 `MobileServiceUrl` 및 `AppKey`를 모바일 서비스 URL 및 모바일 서비스의 응용 프로그램 키로 바꿉니다. 모바일 서비스에 대해 이 설정을 결정하려면 Azure 관리 포털에서 모바일 서비스를 선택한 후 **대시보드**를 클릭합니다.

다음과 같이 서비스 URL인 **NSURL** 개체에서 클라이언트를 만들 수도 있습니다.

    MSClient *client = [MSClient clientWithApplicationURL:(NSURL *)url
                                 applicationKey:(NSString *)string];

테이블 참조 만들기방법: 테이블 참조 만들기
------------------------------------------

모바일 서비스에서 데이터에 액세스하려면 먼저 항목을 쿼리하거나 업데이트하거나 삭제할 테이블에 대한 참조를 가져와야 합니다. 다음 예에서 `ToDoItem`은 테이블 이름입니다.

    MSTable *table = [client tableWithName:@"ToDoItem"]; 

데이터 쿼리방법: 모바일 서비스에서 데이터 쿼리
----------------------------------------------

MSTable 개체가 있으면 쿼리를 만들 수 있습니다. 다음과 같은 단순 쿼리가 ToDoItem 테이블의 모든 항목을 가져옵니다.

    [table readWithCompletion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        if(error) {
            NSLog(@"ERROR %@", error);
        } else {
            for(NSDictionary *item in items) {
                NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
            }
        }
    }];

이 경우에는 간단히 작업 텍스트를 로그에 씁니다.

다음 매개 변수가 콜백에서 지원됩니다.

-   *items*: 쿼리와 일치한 레코드의 **NSArray**입니다.
-   *totalCount*: 쿼리의 현재 페이지에 반환된 항목뿐만 아니라 모든 페이지에 있는 항목의 총 개수입니다. 요청에서 총 개수를 명시적으로 요청하지 않는 경우 이 값은 -1로 설정됩니다. 자세한 내용은 [페이지에서 데이터 반환](#paging)을 참조하십시오.
-   *error*: 발생한 오류입니다. 오류가 발생하지 않은 경우 `nil`입니다.

### 방법: 반환된 데이터 필터링

결과를 필터링하려면 다수의 옵션을 사용할 수 있습니다.

가장 일반적인 경우는 NSPredicate를 사용하여 결과를 필터링하는 것입니다.

    [table readWithPredicate:(NSPredicate *)predicate completion:(MSReadQueryBlock)completion];

다음 조건자는 ToDoItem 테이블에서 완료되지 않은 항목만 반환합니다.

    NSPredicate *predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
    [table readWithPredicate:predicate completion:^(NSArray *items, NSInteger totalCount, NSError *error) {
        //loop through our results
    }];

ID를 사용하여 단일 레코드를 검색할 수 있습니다.

    [table readWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(NSDictionary *item, NSError *error) {
        //your code here
    }];

이 경우에는 콜백 매개 변수가 약간 다릅니다. 결과 배열 및 선택적인 개수를 얻는 대신 해당하는 하나의 레코드만 다시 가져오게 됩니다.

### MSQuery 개체 사용

결과 정렬 순서를 변경하거나 다시 가져오는 데이터 레코드의 수를 제한하는 등과 같이 단순히 행을 필터링하는 것 이상으로 복잡한 쿼리가 필요한 경우 **MSQuery** 개체를 사용합니다. 다음 두 가지 예는 MSQuery 개체 인스턴스를 만드는 방법을 보여 줍니다.

-   `MSQuery *query = [table query];`
-   `MSQuery *query = [table queryWithPredicate:(NSPredicate *)predicate];`

MSQuery 개체는 다음 쿼리 동작을 제어할 수 있게 합니다.

-   결과가 반환되는 순서를 지정합니다.
-   반환되는 필드를 제한합니다.
-   반환되는 레코드 수를 제한합니다.
-   응답에 총 개수를 포함할지 여부를 지정합니다.
-   요청에서 사용자 지정 쿼리 문자열 매개 변수를 지정합니다.

하나 이상의 함수를 적용하여 추가로 쿼리를 정의합니다. 쿼리가 정의되고 나면 **readWithCompletion** 함수를 호출하여 실행합니다.

#### 반환된 데이터 정렬

다음 함수는 정렬에 사용되는 필드를 지정하는 데 사용됩니다.

    -(void) orderByAscending:(NSString *)field
    -(void) orderByDescending:(NSString *)field

다음 쿼리는 먼저 기간을 기준으로, 그런 다음 작업 완료 여부를 기준으로 결과를 정렬합니다.

    [query orderByAscending(@"duration")];
    [query orderByAscending(@"complete")];
    [query readWithCompletion:(NSArray *items, NSInteger totalCount, NSError *error) {
        //code to parse results here
    }]; 

#### 페이지에서 데이터 반환

모바일 서비스에서는 단일 응답에 반환되는 레코드의 양을 제한합니다. 사용자에게 표시되는 레코드 수를 제어하려면 페이징 시스템을 구현해야 합니다. 페이징은 **MSQuery** 개체의 다음 세 가지 속성을 사용하여 수행합니다.

-   `BOOL includeTotalCount`
-   `NSInteger fetchLimit`
-   `NSInteger fetchOffset`

다음 예에서는 간단한 함수가 서버에서 20개의 레코드를 요청한 후 이전에 로드된 레코드의 로컬 컬렉션에 이 레코드를 추가합니다.

    - (bool) loadResults() {
        MSQuery *query = [self.table query];

        query.includeTotalCount = YES;
        query.fetchLimit = 20;
        query.fetchOffset = self.loadedItems.count;
        [query readWithCompletion:(NSArray *items, NSInteger totalCount, NSError *error) {
            if (!error) {
                //add the items to our local copy
                [self.loadedItems addObjectsFromArray:items];       

                //set a flag to keep track if there are any additional records we need to load
                self.moreResults = (self.loadedItems.count < totalCount);
            }
        }];
    }

#### 반환되는 필드 제한

쿼리에서 반환되는 필드를 제한하려면 원하는 필드의 이름을 **selectFields** 속성에서 지정하면 됩니다. 다음 예는 텍스트 및 완료된 필드만 반환됩니다.

    query.selectFields = @[@"text", @"completed"];

#### 추가 querystring 매개 변수 지정

클라이언트 라이브러리를 사용하면 서버에 보내는 요청에 추가 querystring 매개 변수를 포함할 수 있습니다. 이 매개 변수는 서버 쪽 스크립트에 필요할 수 있습니다. 다음 예는 요청에 두 개의 querystring 매개 변수를 추가합니다.

    query.parameters = @{
        @"myKey1" : @"value1",
        @"myKey2" : @"value2",
    };

이 매개 변수는 `myKey1=value1&myKey2=value2`로 쿼리 URI에 추가됩니다. 자세한 내용은 [방법: 사용자 지정 매개 변수 액세스](/en-us/develop/mobile/how-to-guides/work-with-server-scripts#access-headers)를 참조하십시오.

데이터 삽입방법: 모바일 서비스에 데이터 삽입
--------------------------------------------

테이블에 새 행을 삽입하려면 새 [NSDictionary 개체](http://go.microsoft.com/fwlink/p/?LinkId=301965)(영문)를 만들어 삽입 함수에 전달합니다. 다음 코드는 테이블에 새 할 일 항목을 삽입합니다.

    NSDictionary *newItem = @{@"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

모바일 서비스는 테이블 ID용으로 고유한 사용자 지정 문자열 값을 지원합니다. 이에 따라, 응용 프로그램에서 전자 메일 주소나 사용자 이름과 같은 사용자 지정 값을 모바일 서비스 테이블의 ID 행에 사용할 수 있습니다. 예를 들어 각 레코드를 전자 메일 주소로 식별하려는 경우 다음과 같은 JSON 개체를 사용합니다.

    NSDictionary *newItem = @{@"id": @"myemail@emaildomain.com", @"text": @"my new item", @"complete" : @NO};
    [table insert:newItem completion:^(NSDictionary *result, NSError *error) {
        // The result contains the new item that was inserted,
        // depending on your server scripts it may have additional or modified 
        // data compared to what was passed to the server.
    }]; 

새 레코드를 테이블에 삽입할 때 문자열 ID 값이 제공되지 않는 경우 모바일 서비스는 ID용으로 고유한 값을 생성합니다.

문자열 ID 지원은 개발자에게 다음과 같은 이점이 있습니다.

-   데이터베이스에 대한 왕복 없이도 ID를 생성할 수 있습니다.
-   여러 테이블 또는 데이터베이스의 레코드를 병합하기가 더 쉽습니다.
-   응용 프로그램의 논리를 통해 ID 값이 더 효율적으로 통합될 수 있습니다.

서버 스크립트를 사용하여 ID 값을 설정할 수도 있습니다. 다음 스크립트 예제는 사용자 지정 GUID를 생성하여 새 레코드의 ID에 할당합니다. 이는 레코드 ID용으로 값을 전달하지 않은 경우 모바일 서비스에서 생성되는 ID 값과 비슷합니다.

    //Example of generating an id. This is not required since Mobile Services
    //will generate an id if one is not passed in.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

응용 프로그램에서 ID용 값을 제공하는 경우, 모바일 서비스는 값을 있는 그대로 저장합니다. 여기에는 선행 또는 후행 공백이 포함됩니다. 공백이 값에서 제거되지 않습니다.

`id`의 값은 고유해야 하며 다음과 같은 문자를 포함해서는 안 됩니다.

-   제어 문자: [0x0000-0x001F] 및 [0x007F-0x009F]. 자세한 내용은 [ASCII 제어 코드 C0 및 C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set)(영문)을 참조하십시오.
-   인쇄 가능한 문자: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
-   ID "." 및 ".."

또는 테이블에 정수 ID를 사용할 수 있습니다. 정수 ID를 사용하려면 `--integerId` 옵션을 사용하는 `mobile table create` 명령으로 테이블을 만들어야 합니다. 이 명령은 Azure용 CLI(명령줄 인터페이스)와 함께 사용됩니다. CLI 사용에 대한 자세한 내용은 [모바일 서비스 테이블 관리 CLI](http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables)(영문)를 참조하십시오.

동적 스키마가 사용하도록 설정된 경우 모바일 서비스에서 삽입 또는 업데이트 요청에서 개체의 필드를 기준으로 새 열을 자동으로 생성합니다. 자세한 내용은 [동적 스키마](http://go.microsoft.com/fwlink/p/?LinkId=296271)를 참조하십시오.

데이터 수정방법: 모바일 서비스의 데이터 수정
--------------------------------------------

이전 쿼리에서 반환된 항목을 수정한 후 **update** 함수를 호출하여 기존 개체를 업데이트합니다.

    NSMutableDictionary *item = [self.results.item objectAtIndex:0];
    [item setObject:@YES forKey:@"complete"];
    [table update:item completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

업데이트할 때는 다음 예처럼 업데이트할 필드를 해당 행 ID와 함께 제공하기만 하면 됩니다.

    [table update:@{@"id" : @"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete": @Yes} completion:^(NSDictionary *item, NSError *error) {
        //handle errors or any additional logic as needed
    }];

테이블에서 항목을 삭제하려면 다음과 같이 해당 항목을 delete 메서드에 전달하면 됩니다.

    [table delete:item completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }];

다음 예처럼 ID를 직접 사용하여 레코드를 삭제할 수도 있습니다.

    [table deleteWithId:[@"37BBF396-11F0-4B39-85C8-B319C729AF6D"] completion:^(id itemId, NSError *error) {
        //handle errors or any additional logic as needed
    }]; 

업데이트 및 삭제를 수행할 때는 최소한 `id` 특성을 설정해야 합니다.

인증방법: 사용자 인증
---------------------

모바일 서비스에서는 다음 ID 공급자를 사용하여 사용자를 인증할 수 있습니다.

-   Facebook
-   Google
-   Microsoft 계정
-   Twitter
-   Azure Active Directory

ID 공급자 구성에 대한 자세한 내용은 [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-ios)을 참조하십시오.

모바일 서비스에서는 다음과 같은 두 가지 인증 워크플로를 지원합니다.

-   서버 관리 로그인에서는 모바일 서비스가 앱을 대신하여 로그인 프로세스를 관리합니다. 클라이언트 라이브러리별로 공급자 특정 로그인 페이지가 표시되며, 선택한 공급자에서 인증하는 작업을 모바일 서비스에서 수행합니다.

-   클라이언트 관리 로그인에서는 앱이 ID 공급자에게 토큰을 요청한 후 이 토큰을 인증을 위해 모바일 서비스에 제시해야 합니다.

인증에 성공하면 할당된 사용자 ID 값과 인증 토큰이 포함된 사용자 개체가 반환됩니다. 이 사용자 ID를 서버 스크립트에서 사용하여 요청의 유효성을 검사하거나 요청을 수정할 수 있습니다. 자세한 내용은 [스크립트를 사용하여 사용자 권한 부여](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios)를 참조하십시오. 토큰 자체는 후속 로그인에 사용하도록 안전하게 캐시할 수 있습니다.

테이블에 대해 사용 권한을 설정하여 특정 작업을 위한 액세스를 인증된 사용자로만 제한할 수도 있습니다. 자세한 정보는 [사용 권한](http://msdn.microsoft.com/en-us/library/windowsazure/jj193161.aspx)을 참조하십시오.

### 서버 관리 로그인

다음은 Microsoft 계정을 사용하여 로그인하는 방법의 예입니다. 이 코드는 컨트롤러의 ViewDidLoad에서 호출하거나 UIButton에서 수동으로 트리거할 수 있습니다. 그러면 ID 공급자에 로깅하기 위한 표준 UI가 표시됩니다.

    [client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
        completion:^(MSUser *user, NSError *error) {
            NSString *msg;
            if(error) {
                msg = [@"An error occured: " stringByAppendingString:error.description];
            } else {
                msg = [@"You are logged in as " stringByAppendingString:user.userId];
            }
        
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                  message:msg 
                                  delegate:nil 
                                  cancelButtonTitle:@"OK" 
                                  otherButtonTitles: nil];
            [alert show];
    }]; 

참고: Microsoft 계정의 공급자 이외의 ID 공급자를 사용하는 경우 위의 login 메서드에 전달된 값을 다음 중 하나로 변경합니다. `facebook`, `twitter`, `google`, or `windowsazureactivedirectory`.

MSLoginController에 대한 참조를 가져와서 다음을 사용하여 직접 표시할 수 있습니다.

    -(MSLoginController *)loginViewControllerWithProvider:(NSString *)provider completion:(MSClientLoginBlock)completion;

### 클라이언트 관리 로그인(Single Sign-On)

로그인 프로세스가 모바일 서비스 클라이언트 외부에서 이루어지는 경우가 있습니다. 이는 Single Sign-On 기능을 사용하려는 경우 또는 사용자 정보를 얻기 위해 앱에서 바로 ID 공급자에 연락해야 하는 경우 필요할 수 있습니다. 이런 경우 지원되는 ID 공급자로부터 별도로 획득한 토큰을 제공하여 모바일 서비스에 로그인할 수 있습니다.

다음 예에서는 [Live Connect SDK](http://go.microsoft.com/fwlink/p/?LinkId=301960)(영문)를 사용하여 iOS 앱용 Single Sign-On을 사용하도록 설정합니다.

    [client loginWithProvider:@"microsoftaccount" 
        token:@{@"authenticationToken" : self.liveClient.session.authenticationToken}
        completion:^(MSUser *user, NSError *error) {
            self.loggedInUser = user;
            NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                message:msg 
                delegate:nil 
                cancelButtonTitle:@"OK" 
                otherButtonTitles: nil];
            [alert show];
    }];

이 코드에서는 이미 컨트롤러에서 `liveClient`라는 **LiveConnectClient** 인스턴스를 만들었으며 사용자가 로그인한 상태임을 가정합니다.

### 방법: 인증 토큰 캐시

사용자가 응용 프로그램을 실행할 때마다 인증을 거칠 필요가 없도록 하려면 사용자가 로그인한 후에 현재 사용자 ID를 캐시하면 됩니다. 그런 다음 이 정보를 사용해 직접 사용자를 생성하여 로그인 프로세스를 무시할 수 있습니다. 이렇게 하려면 사용자 ID 및 인증 토큰을 로컬에서 저장해야 합니다. 다음 예에서는 토큰이 [KeyChain]에 안전하게 캐시됩니다.

    - (NSMutableDictionary *) createKeyChainQueryWithClient:(MSClient *)client andIsSearch:(bool)isSearch
    {
        NSMutableDictionary *query = [[NSMutableDictionary alloc] init];
        [query setObject:(__bridge id)kSecClassInternetPassword forKey:(__bridge id)(kSecClass)];
        [query setObject:client.applicationURL.absoluteString forKey:(__bridge id)(kSecAttrServer)];
        
        if(isSearch) {
            // Use the proper search constants, return only the attributes of the first match.
            [query setObject:(__bridge id)kSecMatchLimitOne forKey:(__bridge id)kSecMatchLimit];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnAttributes];
            [query setObject:(id)kCFBooleanTrue forKey:(__bridge id)kSecReturnData];
        }

        return query;
    }

    - (IBAction)loginUser:(id)sender {
        NSMutableDictionary *query = [self createKeyChainQueryWithClient:self.todoService.client andIsSearch:YES];
        CFDictionaryRef cfresult;

        OSStatus status = SecItemCopyMatching((__bridge CFDictionaryRef)query, (CFTypeRef *)&cfresult);
        if (status == noErr) {
            NSDictionary * result = (__bridge_transfer NSDictionary*) cfresult;
            
            //create an MSUser object
            MSUser *user = [[MSUser alloc] initWithUserId:[result objectForKey:(__bridge id)(kSecAttrAccount)]];
            NSData *data = [result objectForKey:(__bridge id)(kSecValueData)];
            user.mobileServiceAuthenticationToken = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
            [self.todoService.client setCurrentUser:user];
            
        } else if (status == errSecItemNotFound) {
            //we need to log the user in
            [self.todoService.client loginWithProvider:@"MicrosoftAccount" controller:self animated:YES
                completion:^(MSUser *user, NSError *error) {
                    NSString *msg = [@"You are logged in as " stringByAppendingString:user.userId];
                    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Login" 
                                            message:msg delegate:nil 
                                            cancelButtonTitle:@"OK" 
                                            otherButtonTitles: nil];
                    [alert show];
                
                    //save the user id and token to the KeyChain
                    NSMutableDictionary *newItem = [self createKeyChainQueryWithClient:self.todoService.client 
                                                            andIsSearch:NO];
                    [newItem setObject:user.userId forKey:(__bridge id)kSecAttrAccount];
                    [newItem setObject:[user.mobileServiceAuthenticationToken dataUsingEncoding:NSUTF8StringEncoding] 
                                                    forKey:(__bridge id)kSecValueData];
                 
                    OSStatus status = SecItemAdd((__bridge CFDictionaryRef)newItem, NULL);
                    if(status != errSecSuccess) {
                        //handle error as needed
                        NSAssert(NO, @"Error caching password.");
                    }
            }];
        }   

**참고**

토큰은 중요한 데이터이므로 장치가 분실 또는 도난되는 경우에 대비하여 암호화하여 저장해야 합니다.

캐시된 토큰을 사용하면 사용자는 토큰이 만료될 때까지 다시 로그인할 필요가 없습니다. 사용자가 만료된 토큰으로 로그인하려고 하면 401 권한 없음 응답이 반환됩니다. 이때 사용자는 다시 로그인하여 새 토큰을 획득해야 하며, 새 토큰은 다시 캐시할 수 있습니다. 필터를 사용하면 앱이 모바일 서비스를 호출할 때마다 만료된 토큰을 처리하는 코드를 작성하지 않아도 됩니다. 필터를 사용하여 모바일 서비스 호출 및 모바일 서비스에서 나오는 응답을 차단할 수 있습니다. 필터의 코드는 응답에 401이 있는지 테스트하고, 토큰이 만료된 경우 로그인 프로세스를 트리거한 다음, 401을 생성한 요청을 다시 시도합니다. 자세한 내용은 [만료된 토큰 처리](http://go.microsoft.com/fwlink/p/?LinkId=301955)(영문)를 참조하십시오.

오류 처리방법: 오류 처리
------------------------

모바일 서비스를 호출할 때는 완료 블록에 `NSError *error` 매개 변수가 포함됩니다. 오류가 발생하면 이 매개 변수가 null이 아닌 값으로 반환됩니다. 코드에서 이 매개 변수를 확인하여 필요한 경우 오류를 처리해야 합니다.

오류가 발생한 경우 코드에 MSError.h 파일을 포함하여 추가 정보를 구할 수 있습니다. 이 파일은 `[error userInfo]`에서 추가 데이터에 액세스하는 데 사용할 수 있는 다음 상수를 정의합니다.

-   **MSErrorResponseKey**: 오류와 관련된 HTTP 응답 데이터
-   **MSErrorRequestKey**: 오류와 관련된 HTTP 요청 데이터

또한 각 오류 코드에 대해 상수가 정의되어 있습니다. 이 코드에 대한 설명은 MSError.h 파일에서 찾을 수 있습니다.

유효성 검사 및 오류 처리의 예를 보려면 [모바일 서비스에서 서버 스크립트를 사용하여 데이터 유효성 검사 및 수정](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios)을 참조하십시오. 이 항목에서는 서버 스크립트를 사용하여 서버 쪽 유효성 검사가 구현됩니다. 잘못된 데이터가 제출되는 경우 오류 응답이 반환되며 이 응답은 클라이언트에서 처리됩니다.


<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[Setup and Prerequisites]: #Setup
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #table-reference
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[How to: Bind data to the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching-tokens
[How to: Upload images and large files]: #blobs
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing 
[How to: Customize the client]: #customizing
[Customize request headers]: #custom-headers
[Customize data type serialization]: #custom-serialization
[Next Steps]: #next-steps
[How to: Use MSQuery]: #query-object

<!-- Images. -->

<!-- URLs. -->
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193161.aspx
[Use scripts to authorize users]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Dynamic schema]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /en-us/develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/en-us/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables
