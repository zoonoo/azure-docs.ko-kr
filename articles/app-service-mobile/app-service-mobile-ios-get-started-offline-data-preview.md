<properties
	pageTitle="Azure 모바일 앱(iOS)용 오프라인 동기화 사용"
	description="앱 서비스 모바일 앱을 사용하여 iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다."
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="krisragh"/>

# iOS 모바일 앱에 대해 오프라인 동기화 사용

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## 개요

이 자습서에서는 iOS용 Azure 모바일 앱의 오프라인 동기화 기능을 설명합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 장치가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 백 엔드와 동기화됩니다.

Azure 모바일 앱을 처음 사용하는 경우, 먼저 [iOS 앱 만들기] 자습서를 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화] 항목을 참조하세요.

## <a name="review-sync"></a>클라이언트 동기화 코드 검토 

자습서 [iOS 앱 만들기]에 대해 다운로드한 클라이언트 프로젝트는 로컬 핵심 데이터 기반 데이터베이스를 사용하여 오프라인 동기화를 지원하는 코드를 포함합니다. 이 섹션은 이미 자습서 코드에 포함된 내용에 대한 요약입니다. 기능의 개념적 개요는 [Azure 모바일 앱에서 오프라인 데이터 동기화]를 참조하세요.

Azure 모바일 앱의 오프라인 데이터 동기화 기능을 사용하면 최종 사용자가 네트워크에 액세스할 수 없을 때 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 `MSClient`의 동기화 컨텍스트를 초기화하고 로컬 저장소를 참조합니다. 그런 다음 `MSSyncTable` 인터페이스를 통해 테이블을 참조합니다.

1. **QSTodoService.m**에서 `syncTable` 멤버의 형식은 `MSSyncTable`입니다. 오프라인 동기화에서는 `MSTable` 대신 이 동기화 테이블 인터페이스를 사용합니다. 동기화 테이블을 사용하면 모든 작업이 로컬 저장소로 이동하고 명시적 푸시 및 끌어오기 작업이 있는 원격 백 엔드와만 동기화됩니다.

    동기화 테이블에 대한 참조를 얻으려면 `syncTableWithName` 메서드를 사용합니다. 오프라인 동기화 기능을 제거하려면 대신 `tableWithName`을 사용합니다.

2. 모든 테이블 작업을 수행하려면 먼저 로컬 저장소를 초기화해야 합니다. 다음은 `QSTodoService.init` 메서드의 관련 코드입니다.

        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

    이 코드는 모바일 앱 SDK에 제공된 `MSCoreDataStore` 인터페이스를 사용하여 로컬 저장소를 만듭니다. `MSSyncContextDataSource` 프로토콜을 구현하여 다른 로컬 저장소를 대신 제공할 수 있습니다.

    `initWithDelegate`의 첫 번째 매개 변수는 충돌 처리기를 지정하는 데 사용됩니다. 여기서는 `nil`을 전달했으므로 충돌 발생 시 작업을 중단하는 기본 충돌 처리기를 얻게 됩니다.

	<!-- For details on how to implement a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services]. -->

3. `pullData` 및 `syncData` 메서드는 실제 동기화 작업을 수행합니다. 즉, `syncData`가 먼저 새 변경 내용을 푸시한 다음 `pullData`를 호출하여 원격 백 엔드에서 데이터를 가져옵니다.

        -(void)syncData:(QSCompletionBlock)completion
        {
            // push all changes in the sync context, then pull new data
            [self.client.syncContext pushWithCompletion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                [self pullData:completion];
            }];
        }

    그러면 `pullData` 메서드는 쿼리와 일치하는 새 데이터를 가져옵니다.

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

    `syncData`에서 먼저 동기화 컨텍스트에 대해 `pushWithCompletion`을 호출합니다. 이 메서드는 모든 테이블에서 변경 내용을 푸시하므로 동기화 테이블 자체가 아닌 `MSSyncContext`의 멤버입니다. CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다. 그런 다음 `pullData` 도우미가 호출됩니다. 이 도우미는 `MSSyncTable.pullWithQuery`를 호출하여 원격 데이터를 검색하고 로컬 데이터베이스에 저장합니다.

    이 예제에서 푸시 작업이 반드시 필요하지는 않았습니다. 푸시 작업을 수행하는 테이블에 대한 동기화 컨텍스트에 보류 중인 변경 내용이 있는 경우 끌어오기가 항상 푸시 작업을 먼저 실행합니다. 그러나 둘 이상의 동기화 테이블이 있는 경우 푸시를 명시적으로 호출하여 관련 테이블에서 모든 항목의 일관성을 유지하는 것이 좋습니다.

    `pullWithQuery` 메서드를 사용하면 쿼리를 지정하여 검색하려는 레코드를 필터링할 수 있습니다. 이 예제에서 쿼리는 원격 `TodoItem` 테이블의 모든 레코드를 검색합니다.

    `pullWithQuery`에 대한 두 번째 매개 변수는 *증분 동기화*에 사용되는 쿼리 ID입니다. 증분 동기화는 레코드의 `UpdatedAt` 타임스탬프(로컬 저장소에서는 `ms_updatedAt`이라고 함)를 사용하여 마지막 동기화 이후에 수정된 레코드만 검색합니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 증분 동기화를 옵트아웃하려면 `nil`을 쿼리 ID로 전달합니다. 이 경우 각 끌어오기 작업에서 모든 레코드가 검색되므로 비효율적일 수 있습니다.

	<!--     >[AZURE.NOTE] To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to purge the local store.
 -->

5. `QSTodoService` 클래스에서 `syncData` 메서드는 데이터, `addItem` 및 `completeItem`을 수정하는 작업 뒤에 호출됩니다. 사용자가 새로 고침 제스처를 수행할 때마다 최신 데이터를 가져오도록 `QSTodoListViewController.refresh`에서도 호출됩니다. `QSTodoListViewController.init`이(가) `refresh`을(를) 호출하므로 앱이 시작될 때도 동기화를 수행합니다.

    `syncData`는 데이터가 수정될 때마다 호출되므로 이 앱은 데이터를 편집할 때마다 사용자가 온라인 상태인 것으로 가정합니다. 다른 섹션에서는 오프라인 상태일 때도 편집할 수 있도록 앱을 업데이트합니다.

## <a name="review-core-data"></a>핵심 데이터 모델 검토

핵심 데이터 오프라인 저장소를 사용하는 경우 데이터 모델에서 특정 테이블 및 필드를 정의해야 합니다. 샘플 앱에는 이미 올바른 형식의 데이터 모델이 포함되어 있습니다. 이 섹션에서는 이러한 테이블 및 사용 방법을 알아봅니다.

- **QSDataModel.xcdatamodeld**를 엽니다. SDK에서 사용되는 3개의 테이블과 이 할 일 항목 자체에 사용되는 1개의 테이블 등 모두 4개의 테이블이 정의되어 있습니다.
      * MS\_TableOperations: 서버와 동기화해야 하는 항목 추적
      * MS\_TableOperationErrors: 오프라인 동기화 중에 발생하는 모든 오류를 추적
      * MS\_TableConfig: 모든 끌어오기 작업에 대한 마지막 동기화 작업의 마지막 업데이트 시간 추적
      * TodoItem: 할 일 항목 저장 시스템 열 **ms\_createdAt**, **ms\_updatedAt** 및 **ms\_version**은 선택적 시스템 속성입니다.

>[AZURE.NOTE]Azure 모바일 앱 SDK는 "**`ms_`**"로 시작하는 열 이름을 예약합니다. 시스템 열 이외의 항목에는 이 접두사를 사용하지 않아야 합니다. 그렇지 않으면 원격 백 엔드를 사용할 때 열 이름이 수정됩니다.

- 오프라인 동기화 기능을 사용할 경우 아래와 같이 시스템 테이블을 정의해야 합니다.

    ### 시스템 테이블

    **MS\_TableOperations**

    ![][defining-core-data-tableoperations-entity]

    | 특성 | 형식 |
    |----------- |   ------    |
    | id | 정수 64 |
    | itemId | String |
    | properties | 이진 데이터 |
    | 테이블 | String |
    | tableKind | 정수 16 |

    <br>**MS\_TableOperationErrors**

    ![][defining-core-data-tableoperationerrors-entity]

    | 특성 | 형식 |
    |----------- |   ------    |
    | id | String |
    | operationId | 정수 64 |
    | properties | 이진 데이터 |
    | tableKind | 정수 16 |

    <br>**MS\_TableConfig**

    ![][defining-core-data-tableconfig-entity]

    | 특성 | 형식 |
    |----------- |   ------    |
    | id | String |
    | key | String |
    | keyType | 정수 64 |
    | 테이블 | String |
    | 값 | String |

    ### 데이터 테이블

    ![][defining-core-data-todoitem-entity]

    **TodoItem**


    | 특성 | 형식 | 참고 |
    |-----------   |  ------ | -------------------------------------------------------|
    | id | 문자열, 필수로 표시 | 원격 저장소의 기본 키 |
    | complete | Boolean | todo 항목 필드 |
    | 텍스트 | String | todo 항목 필드 |
    | ms\_createdAt | Date | (선택 사항) \_\_createdAt 시스템 속성에 매핑됩니다. | | ms\_updatedAt | Date | (선택 사항) \_\_updatedAt 시스템 속성에 매핑됩니다. | | ms\_version | String | (선택 사항) 충돌을 감지하는 데 사용되며 \_\_version에 매핑됩니다. |


## <a name="setup-sync"></a>앱의 동기화 동작 변경

이 섹션에서는 앱을 시작할 때나 항목을 삽입 및 업데이트할 때는 앱이 동기화되지 않고 새로 고침 제스처 단추를 누를 때만 동기화되도록 앱을 수정합니다.

1. **QSTodoListViewController.m**에서 **viewDidLoad** 메서드가 끝날 때 `[self refresh]` 호출을 제거하도록 이 메서드를 변경합니다. 이제 데이터가 앱 시작 시에는 서버와 동기화되지 않고 대신 로컬 저장소의 내용이 됩니다.

2. **QSTodoService.m**에서 항목이 삽입된 후에 동기화되지 않도록 `addItem`의 정의를 수정합니다. `self syncData` 블록을 제거하고 다음 코드로 바꿉니다.

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

3. 위와 같이 `completeItem`의 정의를 수정하고 `self syncData`에 대한 블록을 제거한 후 다음 코드로 바꿉니다.

            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

## <a name="test-app"></a>앱 테스트


이 섹션에서는 오프라인 시나리오를 만들기 위해 시뮬레이터에서 Wi-Fi를 켭니다. 데이터 항목을 추가하면 모바일 백 엔드에 동기화되지 않고 로컬 핵심 데이터 저장소에 보관됩니다.

1. IOS 시뮬레이터에서 Wi-Fi를 끕니다.

2. 일부 할 일 항목을 추가하거나 일부 항목을 완료합니다. 시뮬레이터를 끝내고(또는 강제로 앱 닫기) 다시 시작합니다. 변경 내용이 유지되는지 확인합니다.

3. 원격 TodoItem 테이블의 내용 확인
   - JavaScript 백 엔드의 경우 관리 포털로 가서 데이터 탭을 클릭하여 `TodoItem` 테이블의 내용을 봅니다.
   - .NET 백 엔드의 경우 SQL Server Management Studio와 같은 SQL 도구나 Fiddler 또는 Postman 같은 REST 클라이언트를 사용하여 테이블 내용을 봅니다.

    새 항목이 서버와 동기화되지 *않았는지* 확인합니다.

4. IOS 시뮬레이터에서 Wi-Fi를 켠 다음 항목의 목록을 아래로 당겨 새로 고침 제스처를 수행합니다. 진행률 회전자와 텍스트 "동기화 중..."이 표시됩니다.

5. TodoItem 데이터를 다시 봅니다. 이제 새 및 변경된 TodoItems가 나타납니다.

## 요약

오프라인 동기화 기능을 지원하기 위해 `MSSyncTable` 인터페이스를 사용하고 로컬 저장소를 사용하여 `MSClient.syncContext`를 초기화했습니다. 이 경우 로컬 저장소는 핵심 데이터 기반 데이터베이스였습니다.

핵심 데이터 로컬 저장소를 사용할 경우 [올바른 시스템 속성](#review-core-data)을 사용하여 여러 테이블을 정의해야 합니다.

Azure 모바일 앱에 대한 일반적인 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업이 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `MSSyncTable.pullWithQuery` 및 `MSClient.syncContext.pushWithCompletion` 메서드를 사용했습니다.

*  서버의 변경 내용을 푸시하기 위해 `pushWithCompletion`을(를) 호출했습니다. 이 메서드는 모든 테이블에서 변경 사항을 푸시하므로 동기화 테이블이 아닌 `MSSyncContext`의 멤버입니다.

    CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다.

* 서버의 테이블에서 앱으로 데이터를 끌어오기 위해 `MSSyncTable.pullWithQuery`을(를) 호출했습니다.

    끌어오기 작업에서는 항상 푸시 작업을 먼저 수행합니다. 이는 로컬 저장소의 모든 테이블 및 관계를 동기화된 상태로 유지하기 위해서입니다.

    `pullWithQuery`는 `query` 매개 변수를 사용자 지정하여 클라이언트에 저장된 데이터를 필터링하는 데 사용할 수 있습니다.

* 증분 동기화를 사용하려면 쿼리 ID를 `pullWithQuery`로 전달합니다. 쿼리 ID는 마지막 풀링 작업의 결과에서 마지막으로 업데이트된 타임스탬프를 저장하는 데 사용됩니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 쿼리에 매개 변수가 있으면 동일한 매개 변수 값이 쿼리 ID의 일부여야 합니다.

    증분 동기화를 옵트아웃하려면 `nil`을 쿼리 ID로 전달합니다. 이 경우 `pullWithQuery`에 대한 모든 호출에서 모든 레코드가 검색되므로 이는 잠재적으로 비효율적입니다.

<!-- * To remove records from the device local store when they have been deleted in your mobile service database, you should enable [Soft Delete]. Otherwise, your app should periodically call `MSSyncTable.purgeWithQuery` to remove records from the local database, in case they have been deleted in the remote service.
 -->

## 추가 리소스

* [Azure 모바일 앱에서 오프라인 데이터 동기화]

* [클라우드 표지: Azure 모바일 서비스에서 오프라인 동기화](참고: 비디오는 모바일 서비스에 있지만 Azure 모바일 앱에서 비슷한 방식으로 오프라인 동기화가 작동합니다.)

<!-- URLs. -->


[iOS 앱 만들기]: ../app-service-mobile-dotnet-backend-ios-get-started-preview.md
[Azure 모바일 앱에서 오프라인 데이터 동기화]: ../app-service-mobile-offline-data-sync-preview.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data-preview/defining-core-data-todoitem-entity.png

[클라우드 표지: Azure 모바일 서비스에서 오프라인 동기화]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/
 

<!---HONumber=Oct15_HO3-->