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
	ms.date="03/07/2016"
	ms.author="krisragh"/>

# Azure 모바일 앱용 iOS 클라이언트 라이브러리를 사용하는 방법

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

이 가이드에서는 최신 [Azure 모바일 앱 iOS SDK](https://go.microsoft.com/fwLink/?LinkID=266533&clcid=0x409)를 사용하여 일반적인 시나리오를 수행하는 방법을 알려줍니다. Azure 모바일 앱을 처음 접하는 경우 먼저 [Azure 모바일 앱 빠른 시작]을 완료하여 백 엔드를 만들고, 테이블을 만든 다음 미리 빌드된 iOS Xcode 프로젝트를 다운로드합니다. 이 가이드에서는 클라이언트 쪽 iOS SDK에 초점을 둡니다. 백 엔드에 대한 .NET 서버 쪽 SDK에 대해 더 자세히 알아보려면 [.NET 백 엔드로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## 참조 설명서

iOS 클라이언트 SDK에 대한 참조 설명서는 [Azure 모바일 앱 iOS 클라이언트 참조](http://azure.github.io/azure-mobile-services/iOS/v3/)를 참조하세요.

##<a name="Setup"></a>설정 및 필수 조건

이 가이드에서는 테이블과 함께 백 엔드를 만들었다고 가정합니다. 이 가이드에서는 해당 테이블에 이러한 자습서의 테이블과 동일한 스키마가 있다고 가정합니다. 또한 이 가이드에서는 코드에서 `MicrosoftAzureMobile.framework`를 참조하고 `MicrosoftAzureMobile/MicrosoftAzureMobile.h`를 가져온다고 가정합니다.

##<a name="create-client"></a>방법: 클라이언트 만들기

프로젝트에서 Azure 모바일 앱 백 엔드에 액세스하려면 `MSClient`를 만듭니다. `AppUrl`을 앱 URL로 대체합니다. `gatewayURLString` 및 `applicationKey`는 비워둘 수 있습니다. 인증에 대한 게이트웨이를 설정하는 경우 `gatewayURLString`을 게이트웨이 URL로 채웁니다.

**Objective-C**:

```
MSClient *client = [MSClient clientWithApplicationURLString:@"AppUrl"];
```

**Swift**:

```
let client = MSClient(applicationURLString: "AppUrl")
```


##<a name="table-reference"></a>방법: 테이블 참조 만들기

데이터에 액세스하거나 데이터를 업데이트하려면 백 엔드 테이블에 대한 참조를 만듭니다. `TodoItem`을 테이블의 이름으로 바꿉니다.

**Objective-C**:

```
MSTable *table = [client tableWithName:@"TodoItem"];
```

**Swift**:

```
let table = client.tableWithName("TodoItem")
```


##<a name="querying"></a>방법: 데이터 쿼리

데이터베이스 쿼리를 만들려면 `MSTable` 개체를 쿼리합입니다. 다음 쿼리는 `TodoItem`의 모든 항목을 가져오며 각 항목의 텍스트를 기록합니다.

**Objective-C**:

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

**Swift**:

```
table.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="filtering"></a>방법: 반환된 데이터 필터링

결과를 필터링하는 데 사용할 수 있는 많은 옵션이 있습니다.

조건자를 사용하여 필터링하려면 `NSPredicate` 및 `readWithPredicate`을(를) 사용합니다. 다음은 반환된 데이터를 필터링하여 불완전한 할 일 항목만 찾습니다.

**Objective-C**:

```
// Create a predicate that finds items where complete is false
NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
// Query the TodoItem table
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

**Swift**:

```
// Create a predicate that finds items where complete is false
let predicate =  NSPredicate(format: "complete == NO")
// Query the TodoItem table
table.readWithPredicate(predicate) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```

##<a name="query-object"></a>방법: MSQuery 사용

복잡한 쿼리(정렬 및 페이징 포함)를 수행하려면 직접 또는 조건자를 사용하여 `MSQuery` 개체를 만듭니다.

**Objective-C**:

```
MSQuery *query = [table query];
MSQuery *query = [table queryWithPredicate: [NSPredicate predicateWithFormat:@"complete == NO"]];
```

**Swift**:

```
let query = table.query()
let query = table.queryWithPredicate(NSPredicate(format: "complete == NO"))
```

`MSQuery`을(를) 통해 다음을 포함한 여러 쿼리 동작을 제어할 수 있습니다. 다음 예제에 나와 있는 대로 `readWithCompletion`을(를) 호출하여 `MSQuery` 쿼리를 수행합니다.
* 결과의 순서 지정
* 반환할 필드 제한
* 반환할 레코드 수 제한
* 응답의 총 수 지정
* 요청에서 사용자 지정 쿼리 문자열 매개 변수 지정
* 추가 함수 적용


## <a name="sorting"></a>방법: MSQuery를 사용하여 데이터 정렬

결과를 정렬하기 위해 예제를 살펴봅시다. 먼저 `text` 필드를 기준으로 오름차순 정렬을 한 다음 `completion` 필드를 기준으로 내림차순 정렬을 하려면 다음과 같이 `MSQuery`을(를) 호출합니다.

**Objective-C**:

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

**Swift**:

```
query.orderByAscending("text")
query.orderByDescending("complete")
query.readWithCompletion { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let items = result?.items {
        for item in items {
            print("Todo Item: ", item["text"])
        }
    }
}
```


## <a name="selecting"></a><a name="parameters"></a>방법: MSQuery를 사용하여 필드 제한 및 쿼리 문자열 매개 변수 확장

쿼리에서 반환되는 필드를 제한하려면 **selectFields** 속성에서 필드의 이름을 지정합니다. 이렇게 하면 텍스트 및 완료된 필드만 반환됩니다.

**Objective-C**:

```
query.selectFields = @[@"text", @"complete"];
```

**Swift**:

```
query.selectFields = ["text", "complete"]
```

서버 요청에서 추가 쿼리 문자열 매개 변수를 포함하려면(예: 사용자 지정 서버 쪽 스크립트가 이를 사용하기 때문) 다음과 같이 `query.parameters`을(를) 채웁니다.

**Objective-C**:

```
query.parameters = @{
	@"myKey1" : @"value1",
	@"myKey2" : @"value2",
};
```

**Swift**:

```
query.parameters = ["myKey1": "value1", "myKey2": "value2"]
```

##<a name="inserting"></a>방법: 데이터 삽입

새 테이블 행을 삽입하려면 새 `NSDictionary`을(를) 만들고 `table insert`을(를) 호출합니다. 모바일 서비스는 [동적 스키마]를 사용하지 않는 것으로 설정된 경우 `NSDictionary`에 기반하여 새 열을 자동으로 생성합니다.

`id`이(가) 제공되지 않는 경우 백 엔드는 고유한 새 ID를 자동으로 생성합니다. 고유한 `id`을(를) 제공하여 이메일 주소, 사용자 이름을 사용하거나 고유한 사용자 지정 값을 ID로 사용합니다. 고유한 ID를 제공하면 조인 및 비즈니스 지향적인 데이터베이스 논리가 쉬을 수 있습니다.

`result`은(는) 삽입된 새 항목을 포함합니다. 서버에 전달된 데이터에 비해 서버 논리에 따라 추가 또는 수정된 데이터가 있을 수 있습니다.

**Objective-C**:

```
NSDictionary *newItem = @{@"id": @"custom-id", @"text": @"my new item", @"complete" : @NO};
[table insert:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
let newItem = ["id": "custom-id", "text": "my new item", "complete": false]
table.insert(newItem) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

##<a name="modifying"></a>방법: 데이터 수정

기존 행을 업데이트하려면 항목을 수정하고 `update`을(를) 호출합니다.

**Objective-C**:

```
NSMutableDictionary *newItem = [oldItem mutableCopy]; // oldItem is NSDictionary
[newItem setValue:@"Updated text" forKey:@"text"];
[table update:newItem completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
if let newItem = oldItem.mutableCopy() as? NSMutableDictionary {
    newItem["text"] = "Updated text"
    table2.update(newItem as [NSObject: AnyObject], completion: { (result, error) -> Void in
        if let err = error {
            print("ERROR ", err)
        } else if let item = result {
            print("Todo Item: ", item["text"])
        }
    })
}
```

또는 행 ID와 업데이트된 필드를 제공합니다.

**Objective-C**:

```
[table update:@{@"id":@"custom-id", @"text":"my EDITED item"} completion:^(NSDictionary *result, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item: %@", [result objectForKey:@"text"]);
	}
}];
```

**Swift**:

```
table.update(["id": "custom-id", "text": "my EDITED item"]) { (result, error) in
    if let err = error {
        print("ERROR ", err)
    } else if let item = result {
        print("Todo Item: ", item["text"])
    }
}
```

최소한 업데이트할 때에는 `id` 특성이 설정되어야 합니다.

##<a name="deleting"></a>방법: Blob 삭제

항목을 삭제하려면 항목과 함께 `delete`을(를) 호출합니다.

**Objective-C**:

```
[table delete:item completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.delete(newItem as [NSObject: AnyObject]) { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

또는 행 ID를 제공하여 삭제합니다.

**Objective-C**:

```
[table deleteWithId:@"37BBF396-11F0-4B39-85C8-B319C729AF6D" completion:^(id itemId, NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	} else {
		NSLog(@"Todo Item ID: %@", itemId);
	}
}];
```

**Swift**:

```
table.deleteWithId("37BBF396-11F0-4B39-85C8-B319C729AF6D") { (itemId, error) in
    if let err = error {
        print("ERROR ", err)
    } else {
        print("Todo Item ID: ", itemId)
    }
}
```

최소한 삭제할 때에는 `id` 특성이 설정되어야 합니다.

##<a name="customapi"></a>방법: 사용자 지정 API 호출

사용자 지정 API를 사용하여 백 엔드 기능을 노출할 수 있습니다. 테이블 작업에 매핑할 필요는 없습니다. 더 효율적으로 메시징을 제어할 수 있으며 헤더의 읽기/설정 및 응답의 본문 형식을 변경할 수도 있습니다. 백 엔드에서 사용자 지정 API를 만드는 방법에 대한 자세한 내용은 [사용자 지정 API](app-service-mobile-node-backend-how-to-use-server-sdk.md#work-easy-apis)를 참조하세요.

사용자 지정 API를 호출하려면 다음과 같이 `MSClient.invokeAPI`를 호출합니다. 요청 및 응답 콘텐츠는 JSON으로 간주됩니다. 다른 미디어 유형을 사용하려면 [`invokeAPI`의 다른 오버로드를 사용](http://azure.github.io/azure-mobile-services/iOS/v3/Classes/MSClient.html#//api/name/invokeAPI:data:HTTPMethod:parameters:headers:completion:)합니다.

`POST` 요청 대신 `GET` 요청을 하려면 GET 요청에는 메시지의 본문이 없기 때문에 매개 변수를 `HTTPMethod`에서 `"GET"` 및 `body`에서 `nil`으로 설정합니다. 사용자 지정 API가 다른 HTTP 동사를 지원하는 경우 `HTTPMethod`을(를) 적절하게 변경합니다.

**Objective-C**:
```
    [self.client invokeAPI:@"sendEmail"
                      body:@{ @"contents": @"Hello world!" }
                HTTPMethod:@"POST"
                parameters:@{ @"to": @"bill@contoso.com", @"subject" : @"Hi!" }
                   headers:nil
                completion: ^(NSData *result, NSHTTPURLResponse *response, NSError *error) {
                    if(error) {
                        NSLog(@"ERROR %@", error);
                    } else {
                        // Do something with result
                    }
                }];
```

**Swift**:

```
client.invokeAPI("sendEmail",
            body: [ "contents": "Hello World" ],
            HTTPMethod: "POST",
            parameters: [ "to": "bill@contoso.com", "subject" : "Hi!" ],
            headers: nil)
            {
                (result, response, error) -> Void in
                if let err = error {
                    print("ERROR ", err)
                } else if let res = result {
                          // Do something with result
                }
        }
```

##<a name="templates"></a>방법: 플랫폼 간 알림을 보내기 위해 푸시 템플릿 등록

템플릿을 등록하려면 클라이언트 앱에서 **client.push registerDeviceToken** 메서드를 사용하여 템플릿을 전달하기만 하면 됩니다.

**Objective-C**:

```
[client.push registerDeviceToken:deviceToken template:iOSTemplate completion:^(NSError *error) {
	if(error) {
		NSLog(@"ERROR %@", error);
	}
}];
```

**Swift**:

```
    client.push?.registerDeviceToken(NSData(), template: iOSTemplate, completion: { (error) in
        if let err = error {
            print("ERROR ", err)
        }
    })
```

템플릿은 NSDictionary 형식이며 다음 형식으로 여러 템플릿을 포함할 수 있습니다.

**Objective-C**:

```
NSDictionary *iOSTemplate = @{ @"templateName": @{ @"body": @{ @"aps": @{ @"alert": @"$(message)" } } } };
```

**Swift**:

```
let iOSTemplate = ["templateName": ["body": ["aps": ["alert": "$(message)"]]]]
```

보안에 대한 모든 태그는 제거될 것입니다. 설치에 태그를 추가하거나 설치 내에 템플릿을 추가하려면 [Azure 모바일 앱에 대해 .NET 백 엔드 서버 SDK로 작업](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags)을 참조하세요.

이러한 등록된 템플릿을 활용하여 알림을 보내려면 [알림 허브 API](https://msdn.microsoft.com/library/azure/dn495101.aspx)로 작업합니다.

##<a name="errors"></a>방법: 오류 처리

모바일 서비스를 호출할 때는 완료 블록에 `NSError` 매개 변수가 포함됩니다. 오류가 발생하면 이 매개 변수는 null이 아닌 값입니다. 코드에서 이 매개 변수를 확인하고 위의 코드 조각에서처럼 필요에 따라 오류를 처리해야 합니다.

다음과 같이 가져올 수 있는 오류에 관련된 더 많은 데이터를 얻도록 파일 [`<WindowsAzureMobileServices/MSError.h>`](https://github.com/Azure/azure-mobile-services/blob/master/sdk/iOS/src/MSError.h)에 `MSErrorResponseKey`, `MSErrorRequestKey` 및 `MSErrorServerItemKey` 상수를 정의합니다.

**Objective-C**:

```
NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
```

**Swift**:

```
let serverItem = error.userInfo[MSErrorServerItemKey]
```

또한 파일은 아래와 같이 사용할 수 있는 각 오류 코드에 대한 상수를 정의합니다.

**Objective-C**:

```
if (error.code == MSErrorPreconditionFailed) {
```

**Swift**:

```
if (error.code == MSErrorPreconditionFailed) {
```

## <a name="adal"></a>방법: Active Directory 인증 라이브러리를 사용하여 사용자 인증

Azure Active Directory를 사용하여 응용 프로그램에 사용자가 로그인하려면 Active Directory 인증 라이브러리(ADAL)를 사용할 수 있습니다. `loginAsync()` 메서드는 UX 느낌을 그대로 제공하고 추가 사용자 지정을 허용하기에 해당 메서드 사용을 선호할 수 있습니다.

1. 다음으로 [Active Directory 로그인에 앱 서비스를 구성하는 방법](app-service-mobile-how-to-configure-active-directory-authentication.md) 자습서를 수행하여 AAD 로그인에 모바일 앱 백 엔드를 구성합니다. 네이티브 클라이언트 응용 프로그램을 등록하는 선택적 단계를 완료해야 합니다. iOS의 경우 리디렉션 URI는 `<app-scheme>://<bundle-id>` 형식인 것이 좋습니다(필수 아님). 자세한 내용은 [ADAL iOS 빠른 시작](active-directory-devquickstarts-ios.md#em1-determine-what-your-redirect-uri-will-be-for-iosem)을 참조하세요.

2. Cocoapods를 사용하여 ADAL을 설치합니다. 다음을 포함하도록 Podfile을 편집합니다. 이때 **YOUR-PROJECT**를 Xcode 프로젝트의 이름으로 바꿉니다.

		source 'https://github.com/CocoaPods/Specs.git'
		link_with ['YOUR-PROJECT']
		xcodeproj 'YOUR-PROJECT'
및 Pod:

		pod 'ADALiOS'

3. 터미널을 사용하여 프로젝트를 포함하는 디렉터리에서 `pod install`을 실행한 다음 생성된 Xcode 작업 영역(프로젝트 아님)을 엽니다.

4. 사용하는 언어에 따라 응용 프로그램에 아래 코드를 추가합니다. 각각에서 다음과 같이 대체합니다.

* **INSERT-AUTHORITY-HERE**를 응용 프로그램이 프로비전된 테넌트의 이름으로 바꿉니다. https://login.windows.net/contoso.onmicrosoft.com 형식이어야 합니다. 이 값은 [Azure 클래식 포털]의 Azure Active Directory에 있는 도메인 탭에서 복사될 수 있습니다.

* **INSERT-RESOURCE-ID-HERE**를 모바일 앱 백 엔드에 대한 클라이언트 ID로 바꿉니다. 포털의 **Azure Active Directory 설정**에 있는 **고급** 탭에서 이를 가져올 수 있습니다.

* **INSERT-CLIENT-ID-HERE**를 네이티브 클라이언트 응용 프로그램에서 복사한 클라이언트 ID로 바꿉니다.

* HTTPS 체계를 사용하여 **INSERT-REDIRECT-URI-HERE**를 사이트의 _/.auth/login/done_ 끝점으로 바꿉니다. 이 값은 \__https://contoso.azurewebsites.net/.auth/login/done_와 유사해야 합니다.

**Objective-C**:

	#import <ADALiOS/ADAuthenticationContext.h>
	#import <ADALiOS/ADAuthenticationSettings.h>
	// ...
	- (void) authenticate:(UIViewController*) parent
	           completion:(void (^) (MSUser*, NSError*))completionBlock;
	{
	    NSString *authority = @"INSERT-AUTHORITY-HERE";
	    NSString *resourceId = @"INSERT-RESOURCE-ID-HERE";
	    NSString *clientId = @"INSERT-CLIENT-ID-HERE";
	    NSURL *redirectUri = [[NSURL alloc]initWithString:@"INSERT-REDIRECT-URI-HERE"];
	    ADAuthenticationError *error;
	    ADAuthenticationContext authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
	    authContext.parentController = parent;
	    [ADAuthenticationSettings sharedInstance].enableFullScreen = YES;
	    [authContext acquireTokenWithResource:resourceId
	                                 clientId:clientId
	                              redirectUri:redirectUri
	                          completionBlock:^(ADAuthenticationResult *result) {
	                              if (result.status != AD_SUCCEEDED)
	                              {
	                                  completionBlock(nil, result.error);;
	                              }
	                              else
	                              {
	                                  NSDictionary *payload = @{
	                                                            @"access_token" : result.tokenCacheStoreItem.accessToken
	                                                            };
	                                  [client loginWithProvider:@"aad" token:payload completion:completionBlock];
	                              }
	                          }];
	}


**Swift**:

	// add the following imports to your bridging header:
	//     #import <ADALiOS/ADAuthenticationContext.h>
	//     #import <ADALiOS/ADAuthenticationSettings.h>

	func authenticate(parent: UIViewController, completion: (MSUser?, NSError?) -> Void) {
		let authority = "INSERT-AUTHORITY-HERE"
		let resourceId = "INSERT-RESOURCE-ID-HERE"
		let clientId = "INSERT-CLIENT-ID-HERE"
		let redirectUri = NSURL(string: "INSERT-REDIRECT-URI-HERE")
		var error: AutoreleasingUnsafeMutablePointer<ADAuthenticationError?> = nil
		let authContext = ADAuthenticationContext(authority: authority, error: error)
		authContext.parentController = parent
		ADAuthenticationSettings.sharedInstance().enableFullScreen = true
		authContext.acquireTokenWithResource(resourceId, clientId: clientId, redirectUri: redirectUri) { (result) in
		        if result.status != AD_SUCCEEDED {
		            completion(nil, result.error)
		        }
		        else {
		            let payload: [String: String] = ["access_token": result.tokenCacheStoreItem.accessToken]
		            client.loginWithProvider("aad", token: payload, completion: completion)
		        }
    		}
	}


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
[Conflict-Handler]: mobile-services-ios-handling-conflicts-offline-data.md#add-conflict-handling

<!---HONumber=AcomDC_0309_2016-->