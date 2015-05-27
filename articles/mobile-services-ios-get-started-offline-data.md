<properties
	pageTitle="모바일 서비스에서 오프라인 데이터 동기화 시작(iOS) | 모바일 개발자 센터"
	description="Azure 모바일 서비스를 사용하여 iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다."
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/19/2015"
	ms.author="krisragh;donnam"/>

# 모바일 서비스에서 오프라인 데이터 동기화 시작

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

오프라인 동기화를 사용하면 네트워크에 연결되어 있지 않은 경우에도 모바일 앱의 데이터를 보거나, 추가하거나, 수정할 수 있습니다. 이 자습서에서는 로컬 오프라인 데이터베이스에서 변경 내용을 자동으로 저장하는 방법 및 온라인으로 돌아갈 때마다 해당 변경 내용을 동기화하는 방법에 대해 알아봅니다.

오프라인 동기화의 몇 가지 혜택은 다음과 같습니다.

* 서버 데이터를 장치에 로컬로 캐시하여 앱 응답성 향상
* 일시적인 네트워크 연결 중단에도 앱 복원력 제공
* 연결이 약하거나 연결되지 않은 상태에서도 데이터를 만들고 수정하도록 허용
* 여러 장치에서 데이터 동기화
* 두 장치에서 동일한 레코드 수정 시 충돌 감지

> [AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 [평가판 사용 기간이 끝난 후에도 계속 사용할 수 있는 무료 모바일 서비스](http://azure.microsoft.com/pricing/details/mobile-services/)를 사용할 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target="_blank")을 참조하세요.

이 자습서는 먼저 완료해야 하는 [모바일 서비스 퀵 스타트 자습서]를 기반으로 합니다. 먼저 퀵 스타트에서 오프라인 동기화와 관련된 코드를 검토해 보겠습니다.

## <a name="review-sync"></a>모바일 서비스 동기화 코드 검토

Azure 모바일 서비스의 오프라인 동기화를 사용하면 최종 사용자가 네트워크에 액세스할 수 없을 때 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 `MSClient`의 동기화 컨텍스트를 초기화하고 로컬 저장소를 참조합니다. 그런 다음 `MSSyncTable` 인터페이스를 통해 테이블을 참조합니다.

* **QSTodoService.m**에서 `syncTable` 멤버의 형식은 `MSSyncTable`입니다. 오프라인 동기화는 `MSTable` 대신 이를 사용합니다. 동기화 테이블을 사용하면 모든 작업이 로컬 저장소로 이동하고 명시적 푸시 및 끌어오기 작업이 있는 원격 서비스와만 동기화됩니다.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

동기화 테이블에 대한 참조를 얻으려면 `syncTableWithName` 메서드를 사용합니다. 오프라인 동기화 기능을 제거하려면 대신 `tableWithName`을(를) 사용합니다.

* **QSTodoService.m**에서 테이블 작업이 수행되기 전에 로컬 저장소가 `QSTodoService.init`에서 초기화됩니다.

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

이는 `MSCoreDataStore` 인터페이스를 사용하여 로컬 저장소를 만듭니다. `MSSyncContextDataSource` 프로토콜을 구현하여 다른 로컬 저장소를 제공할 수 있습니다.

`initWithDelegate`의 첫 번째 매개 변수는 충돌 처리기를 지정하지만 `nil`을(를) 전달했기 때문에 충돌에서 실패하는 기본 충돌 처리기를 얻게 됩니다. 사용자 지정 충돌 처리기 구현 방법에 대한 자세한 내용은 [모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]를 참조하세요.

* **QSTodoService.m**에서 `syncData`은(는) 먼저 새 변경 내용을 푸시하고 원격 서비스에서 데이터를 얻도록 `pullData`을(를) 호출합니다. `syncData`에서 먼저 동기화 컨텍스트에 대해 `pushWithCompletion`을(를) 호출합니다. 이 메서드는 모든 테이블에서 변경 내용을 푸시하므로 동기화 테이블 자체가 아닌 `MSSyncContext`의 멤버입니다. 만들기, 업데이트 또는 삭제 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다. `syncData` 끝부분에서 도우미 `pullData`을(를) 호출합니다.

이 예제에서 푸시 작업이 반드시 필요하지는 않았습니다. 푸시 작업을 수행하는 테이블에 대한 동기화 컨텍스트에 보류 중인 변경 내용이 있는 경우 끌어오기가 항상 푸시 작업을 먼저 실행합니다. 하지만 하나 이상의 동기화 테이블이 있는 경우, 테이블 간에 일관성을 갖도록 명시적으로 푸시를 호출합니다.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* 그런 다음 **QSTodoService.m**에서 `pullData`은(는) 쿼리와 일치하는 새 데이터를 가져옵니다. `pullData`은(는) `MSSyncTable.pullWithQuery`을(를) 호출하여 원격 데이터를 검색하고 이를 로컬로 저장하도록 합니다. 또한 `pullWithQuery`을(를) 통해 검색하려는 레코드를 필터링하도록 쿼리를 지정할 수 있습니다. 이 예제에서 쿼리는 원격 `TodoItem` 테이블의 모든 레코드를 검색합니다.

`pullWithQuery`에 대한 두 번째 매개 변수는 _증분 동기화_를 위한 쿼리 ID입니다. 증분 동기화는 레코드의 `UpdatedAt` 타임스탬프(로컬 저장소에서는 `ms_updatedAt`(이)라고 함)를 사용하여 마지막 동기화 이후에 수정된 레코드만 검색합니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열입니다. 증분 동기화를 옵트아웃하려면 `nil`을(를) 쿼리 ID로 전달합니다. 이 방법은 가져오기 작업에서 모든 레코드를 검색하기 때문에 효율적이지 않습니다.

```
      -(void)pullData:(QSCompletionBlock)completion
      {
          MSQuery *query = [self.syncTable query];

          // Pulls data from the remote server into the local table.
          // We're pulling all items and filtering in the view
          // query ID is used for incremental sync
          [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
              [self logErrorIfNotNil:error];

              // Let the caller know that we have finished
              if (completion != nil) {
                  dispatch_async(dispatch_get_main_queue(), completion);
              }
          }];
      }
```


>[AZURE.NOTE]모바일 서비스 데이터베이스에서 삭제된 레코드를 장치 로컬 저장소에서 제거하려면 [일시 삭제]를 사용해야 합니다. 그렇지 않으면 앱이 주기적으로 `MSSyncTable.purgeWithQuery`에 대해 호출하여 로컬 저장소를 제거합니다.


* **QSTodoService.m**에서 `addItem` 및 `completeItem` 메서드는 데이터 수정 후에 `syncData`을(를) 호출합니다. **QSTodoListViewController.m**에서 `refresh` 메서드도 `syncData`을(를) 호출하므로 새고 고침되고 실행될 때마다 UI가 최신 데이터를 표시합니다(`init`이(가) `refresh` 호출).

데이터를 수정할 때마다 응용 프로그램이 `syncData`을(를) 호출하기 때문에 해당 앱에서 데이터를 편집할 때마다 온라인 상태인 것으로 가정합니다.

## <a name="review-core-data"></a>핵심 데이터 모델 검토

핵심 데이터 오프라인 저장소를 사용하는 경우 데이터 모델에서 특정 테이블 및 필드를 정의해야 합니다. 샘플 앱에는 이미 올바른 형식의 데이터 모델이 포함되어 있습니다. 이 섹션에서는 이러한 테이블 및 사용 방법을 알아봅니다.

- **QSDataModel.xcdatamodeld**를 엽니다. SDK에서 사용되는 3개의 테이블과 할 일 항목 자체에 사용되는 1개의 테이블 등 모두 4개의 테이블이 정의되어 있습니다.

      * MS_TableOperations: 서버와 동기화되는 항목 추적
      * MS_TableOperationErrors: 오프라인 동기화 중에 발생하는 모든 오류 추적
      * MS_TableConfig: 모든 끌어오기 작업에 대한 마지막 동기화 작업의 마지막 업데이트 시간 추적
      * TodoItem: 할 일 항목 저장 시스템 열 **ms_createdAt**, **ms_updatedAt** 및 **ms_version**은 선택적 시스템 속성입니다.

>[AZURE.NOTE]모바일 서비스 SDK는 "**`ms_`**"(으)로 시작하는 열 이름을 예약합니다. 시스템 열 이외에 이 접두사를 사용하지 마세요. 사용하는 경우 원격 서비스를 사용하면 열 이름이 수정됩니다.

- 오프라인 동기화 기능을 사용할 경우 아래와 같이 시스템 테이블을 정의해야 합니다.

    ### 시스템 테이블

    #### MS_TableOperations

    | 특성 | 형식 |
    |----------- |   ------    |
    | id | 정수 64 |
    | itemId | String |
    | properties | 이진 데이터 |
    | 테이블 | String |
    | tableKind | 정수 16 |

    #### MS_TableOperationErrors

    | 특성 | 형식 |
    |------------ | ----------  |
    | id | String |
    | operationId | 정수 64 |
    | properties | 이진 데이터 |
    | tableKind | 정수 16 |

    #### MS_TableConfig


    | 특성 | 형식 |
    |----------- | ----------  |
    | id | String |
    | key | String |
    | keyType | 정수 64 |
    | 테이블 | String |
    | 값 | String |

    ### 데이터 테이블

    #### TodoItem

    | 특성 | 형식 |
    |-----------   |  ------ |
    | id | String |
    | complete | Boolean |
    | 텍스트 | String |
    | ms_createdAt | Date |
    | ms_updatedAt | Date |
    | ms_version | String |


## <a name="setup-sync"></a>앱의 동기화 동작 변경

이 섹션에서는 앱을 시작할 때나 항목을 삽입 및 업데이트할 때는 앱이 동기화되지 않고 새로 고침 제스처를 수행할 때만 동기화되도록 앱을 수정합니다.

* **QSTodoListViewController.m**에서 메서드가 끝날 때 `[self refresh]` 호출을 제거하도록 `viewDidLoad`을(를) 변경합니다. 이제 데이터가 앱 시작 시에는 서버와 동기화되지 않고 대신 로컬로만 저장됩니다.

* **QSTodoService.m**에서 항목이 삽입된 후에 동기화되지 않도록 `addItem`을(를) 수정합니다. `self syncData` 블록을 제거하고 다음으로 바꿉니다.

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* 마찬가지로, 다시 `completeItem`의 **QSTodoService.m**에서 `self syncData`에 대한 블록을 제거하고 다음으로 바꿉니다.

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>앱 테스트

이 섹션에서는 오프라인 시나리오를 만들기 위해 시뮬레이터에서 Wi-Fi를 켭니다. 데이터 항목을 추가하면 모바일 서비스에 동기화되지 않고 로컬 핵심 데이터 저장소에 보관됩니다.

1. IOS 시뮬레이터에서 Wi-Fi를 끕니다. 이는 offlien 시나리오를 시뮬레이션합니다.

2. 일부 할 일 항목을 추가하거나 일부 항목을 완료합니다. 시뮬레이터를 끝내고(또는 강제로 앱 닫기) 다시 시작합니다. 변경 내용이 유지되는지 확인합니다. 데이터 항목은 로컬 핵심 데이터 저장소에 보관되기 때문에 계속 표시됩니다.

3. 원격 TodoItem 테이블의 내용을 확인합니다. 새 항목이 서버와 동기화되지 _않았는지_ 확인합니다.

   - JavaScript 백 엔드의 경우 관리 포털로 가서 데이터 탭을 클릭하여 `TodoItem` 테이블의 내용을 봅니다.
   - .NET 백 엔드의 경우 SQL Server Management Studio와 같은 SQL 도구나 Fiddler 또는 Postman 같은 REST 클라이언트를 사용하여 테이블 내용을 봅니다.

4. iOS 시뮬레이터에서 Wi-Fi를 끕니다. 그런 다음 항목 목록을 아래로 끌어서 새로 고침 제스처를 수행합니다. 진행률 회전자와 텍스트 "동기화 중..."이 표시됩니다.

5. TodoItem 데이터를 다시 봅니다. 이제 새 및 변경된 TodoItems가 나타납니다.

## 요약

모바일 서비스의 오프라인 기능을 지원하기 위해 `MSSyncTable` 인터페이스를 사용하고 로컬 저장소에서 `MSClient.syncContext`을(를) 초기화했습니다. 이 경우 로컬 저장소는 핵심 데이터 기반 데이터베이스였습니다.

핵심 데이터 로컬 저장소를 사용할 경우 [올바른 시스템 속성][Review the Core Data model]을 사용하여 여러 테이블을 정의합니다. 모바일 서비스에 대한 일반 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업은 로컬 저장소에 대해 수행됩니다.

로컬 저장소를 서버와 동기화하기 위해 `MSSyncTable.pullWithQuery` 및 `MSClient.syncContext.pushWithCompletion`을(를) 사용했습니다.

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## 다음 단계

* [모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]

* [모바일 서비스에서 일시 삭제 사용][Soft Delete]

## 추가 리소스

* [클라우드 커버: Azure 모바일 서비스의 오프라인 동기화]

* [Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱](참고: 데모는 Windows용이지만 기능 설명은 모든 플랫폼에 적용)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[일시 삭제]: mobile-services-using-soft-delete.md

[클라우드 커버: Azure 모바일 서비스의 오프라인 동기화]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[모바일 서비스 퀵 스타트 자습서]: mobile-services-ios-get-started.md

<!--HONumber=54-->