<properties
	pageTitle="Azure 모바일 앱에 iOS SDK를 사용하는 방법"
	description="Azure 모바일 앱에 iOS SDK를 사용하는 방법"
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="krisragh"/>

# Azure 모바일 앱용 iOS 클라이언트 라이브러리를 사용하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;
 
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 가이드에서는 최신 [Azure 모바일 앱 iOS SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409)를 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure 모바일 앱을 처음 접하는 경우 먼저 [Azure 모바일 앱 빠른 시작]을 완료하여 백 엔드를 만들고 테이블을 만든 다음 미리 빌드된 iOS Xcode 프로젝트를 다운로드합니다. 이 가이드에서는 클라이언트 쪽 iOS SDK에 초점을 둡니다. 백 엔드의 .NET 서버 쪽 SDK에 대해 자세히 알아보려면 [.NET 백 엔드로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##<a name="Setup"></a>설정 및 필수 조건

이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다. 또한 이 가이드에서는 코드에서 `WindowsAzureMobileServices.framework`를 참조하고 `WindowsAzureMobileServices/WindowsAzureMobileServices.h`를 가져온다고 가정합니다.

##<a name="create-client"></a>방법: 클라이언트 만들기

프로젝트에서 Azure 모바일 앱 백 엔드에 액세스하려면 `MSClient`를 만듭니다. `AppUrl`을 앱 URL로 대체합니다. `gatewayURLString` 및 `applicationKey`는 비워 둘 수 있습니다. 인증에 대한 게이트웨이를 설정하는 경우 `gatewayURLString`을 게이트웨이 URL로 채웁니다.

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl" gatewayURLString:@"" applicationKey:@""];
```

##<a name="table-reference"></a>방법: 테이블 참조 만들기

데이터에 액세스하거나 데이터를 업데이트하려면 백 엔드 테이블에 대한 참조를 만듭니다. `TodoItem`을(를) 테이블의 이름으로 바꿉니다.

```
	MSTable *table = [client tableWithName:@"TodoItem"];
```

##<a name="querying"></a>방법: 데이터 쿼리

데이터베이스 쿼리를 만들려면 `MSTable` 개체를 쿼리합입니다. 다음 쿼리는 `TodoItem`의 모든 항목을 가져오며 각 항목의 텍스트를 기록합니다.

```
[table readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) { // error is nil if no error occured
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) { // items is NSArray of records that match query
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="filtering"></a>방법: 반환된 데이터 필터링

결과를 필터링하는 데 사용할 수 있는 많은 옵션이 있습니다.

조건자를 사용하여 필터링하려면 `NSPredicate` 및 `readWithPredicate`을(를) 사용합니다. 다음은 반환된 데이터를 필터링하여 불완전한 할 일 항목만 찾습니다.

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table and update the items property with the results from the service
[table readWithPredicate:predicate completion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```

##<a name="query-object"></a>방법: MSQuery 사용

복잡한 쿼리(정렬 및 페이징 포함)를 수행하려면 직접 또는 조건자를 사용하여 `MSQuery` 개체를 만듭니다.

```
    MSQuery *query = [table query];
    MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

`MSQuery`을(를) 통해 다음을 포함한 여러 쿼리 동작을 제어할 수 있습니다. 다음 예시에서 볼 수 있는 대로 `readWithCompletion`을(를) 호출하여 `MSQuery` 쿼리를 실행합니다. * 결과 순서 지정 * 반환할 필드 제한 * 반호나할 레코드 수 제한 * 응답에서 총 수 지정 * 요청에서 사용자 지정 쿼리 문자열 매개 변수 지정 * 추가 함수 적용


## <a name="sorting"></a>방법: MSQuery를 사용하여 데이터 정렬

결과를 정렬하기 위해 예제를 살펴봅시다. 먼저 `text` 필드를 기준으로 오름차순 정렬을 한 다음 `completion` 필드를 기준으로 내림차순 정렬을 하려면 다음과 같이 `MSQuery`을(를) 호출합니다.

```
[query orderByAscending:@"text"];
[query orderByDescending:@"complete"];
[query readWithCompletion:^(MSQueryResult *result, NSError *error) {
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
				for(NSDictionary *item in result.items) {
						NSLog(@"Todo Item: %@", [item objectForKey:@"text"]);
				}
		}
}];
```


## <a name="selecting"></a><a name="parameters"></a>방법: MSQuery를 사용하여 필드 제한 및 쿼리 문자열 매개 변수 확장

쿼리에서 반환되는 필드를 제한하려면 **selectFields** 속성에서 필드의 이름을 지정합니다. 이렇게 하면 텍스트 및 완료된 필드만 반환됩니다.

```
	query.selectFields = @[@"text", @"completed"];
```

서버 요청에서 추가 쿼리 문자열 매개 변수를 포함하려면(예: 사용자 지정 서버 쪽 스크립트가 이를 사용하기 때문) 다음과 같이 `query.parameters`을(를) 채웁니다.

```
	query.parameters = @{
		@"myKey1" : @"value1",
		@"myKey2" : @"value2",
	};
```

##<a name="inserting"></a>방법: 데이터 삽입

새 테이블 행을 삽입하려면 새 `NSDictionary`을(를) 만들고 `table insert`을(를) 호출합니다. 모바일 서비스는 [동적 스키마]를 사용하지 않는 것으로 설정된 경우 `NSDictionary`에 기반하여 새 열을 자동으로 생성합니다.

`id`이(가) 제공되지 않는 경우 백 엔드는 고유한 새 ID를 자동으로 생성합니다. 고유한 `id`을(를) 제공하여 이메일 주소, 사용자 이름을 사용하거나 고유한 사용자 지정 값을 ID로 사용합니다. 고유한 ID를 제공하면 조인 및 비즈니스 지향적인 데이터베이스 논리가 쉬을 수 있습니다.

```
	NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
	[self.table insert:newItem completion:^(NSDictionary *result, NSError *error) {
		// The result contains the new item that was inserted,
		// depending on your server scripts it may have additional or modified
		// data compared to what was passed to the server.
		if(error) {
				NSLog(@"ERROR %@", error);
		} else {
						NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
		}
	}];
```

##<a name="modifying"></a>방법: 데이터 수정

기존 행을 업데이트하려면 항목을 수정하고 `update`을(를) 호출합니다.

```
	NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
	[newItem setValue:@"Updated text" forKey:@"text"];
	[self.table update:newItem completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

또는 행 ID와 업데이트된 필드를 제공합니다.

```
	[self.table update:@{@"id":@"37BBF396-11F0-4B39-85C8-B319C729AF6D", @"Complete":@YES} completion:^(NSDictionary *item, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

최소한 업데이트할 때에는 `id` 특성이 설정되어야 합니다.

##<a name="deleting"></a>방법: Blob 삭제

항목을 삭제하려면 항목과 함께 `delete`을(를) 호출합니다.

```
	[self.table delete:item completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];
```

또는 행 ID를 제공하여 삭제합니다.

```
	[self.table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
		// Handle error or perform additional logic as needed
	}];   
```

최소한 삭제할 때에는 `id` 특성이 설정되어야 합니다.


##<a name="errors"></a>방법: 오류 처리

모바일 서비스를 호출할 때는 완료 블록에 `NSError *error` 매개 변수가 포함됩니다. 오류가 발생하면 이 매개 변수는 null이 아닌 값입니다. 코드에서 이 매개 변수를 확인하여 필요한 경우 오류를 처리해야 합니다.

파일 [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h)는 `MSErrorResponseKey`. `MSErrorRequestKey` 및 `MSErrorServerItemKey` 상수를 정의하여 오류에 관련된 더 많은 데이터를 가져옵니다. 또한 이 파일은 각 오류 코드에 대한 상수를 정의합니다. 이러한 상수를 사용하는 방법에 대한 예제는 `MSErrorServerItemKey` 및 `MSErrorPreconditionFailed`의 사용에 대한 [충돌 처리기]를 참조하세요.

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
[Azure 모바일 앱 빠른 시작]: app-service-mobile-ios-get-started.md

[Add Mobile Services to Existing App]: /develop/mobile/tutorials/get-started-data
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Validate and modify data in Mobile Services by using server scripts]: /develop/mobile/tutorials/validate-modify-and-augment-data-ios
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[iOS SDK]: https://developer.apple.com/xcode

[Handling Expired Tokens]: http://go.microsoft.com/fwlink/p/?LinkId=301955
[Live Connect SDK]: http://go.microsoft.com/fwlink/p/?LinkId=301960
[Permissions]: http://msdn.microsoft.com/library/windowsazure/jj193161.aspx
[Service-side Authorization]: mobile-services-javascript-backend-service-side-authorization.md
[Use scripts to authorize users]: /develop/mobile/tutorials/authorize-users-in-scripts-ios
[동적 스키마]: http://go.microsoft.com/fwlink/p/?LinkId=296271
[How to: access custom parameters]: /develop/mobile/how-to-guides/work-with-server-scripts#access-headers
[Create a table]: http://msdn.microsoft.com/library/windowsazure/jj193162.aspx
[NSDictionary object]: http://go.microsoft.com/fwlink/p/?LinkId=301965
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md#Mobile_Tables
[충돌 처리기]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=Nov15_HO3-->