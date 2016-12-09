---
title: "Azure 모바일 앱(iOS)용 오프라인 동기화 사용"
description: "앱 서비스 모바일 앱을 사용하여 iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법을 알아봅니다."
documentationcenter: ios
author: ysxu
manager: yochayk
editor: 
services: app-service\mobile
ms.assetid: eb5b9520-0f39-4a09-940a-dadb6d940db8
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c213f8f4f8de6f16efe70ac3332ccbc8c428b85b


---
# <a name="enable-offline-sync-for-your-ios-mobile-app"></a>iOS 모바일 앱에 대해 오프라인 동기화 사용
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

## <a name="overview"></a>개요
이 자습서에서는 iOS용 Azure 모바일 앱의 오프라인 동기화 기능을 설명합니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다. 변경 내용은 로컬 데이터베이스에 저장됩니다. 장치가 다시 온라인 상태가 되면 이러한 변경 내용이 원격 백 엔드와 동기화됩니다.

Azure 모바일 앱을 처음 사용하는 경우, 먼저 [iOS 앱 만들기]자습서를 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 데이터 액세스 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

오프라인 동기화 기능에 대한 자세한 내용은 [Azure 모바일 앱에서 오프라인 데이터 동기화]항목을 참조하세요.

## <a name="a-namereview-syncareview-the-client-sync-code"></a><a name="review-sync"></a>클라이언트 동기화 코드 검토
자습서 [iOS 앱 만들기] 에 대해 다운로드한 클라이언트 프로젝트는 로컬 핵심 데이터 기반 데이터베이스를 사용하여 오프라인 동기화를 지원하는 코드를 포함합니다. 이 섹션은 이미 자습서 코드에 포함된 내용에 대한 요약입니다. 기능의 개념적 개요는 [Azure 모바일 앱에서 오프라인 데이터 동기화]를 참조하세요.

Azure 모바일 앱의 오프라인 데이터 동기화 기능을 사용하면 최종 사용자가 네트워크에 액세스할 수 없을 때 로컬 데이터베이스를 조작할 수 있습니다. 앱에서 이러한 기능을 사용하려면 `MSClient` 의 동기화 컨텍스트를 초기화하고 로컬 저장소를 참조합니다. 그런 다음 `MSSyncTable` 인터페이스를 통해 테이블을 참조합니다.

1. **QSTodoService.m** (Objective-C) 또는 **ToDoTableViewController.swift** (Swift)에서 `syncTable` 멤버 형식은 `MSSyncTable`입니다. 오프라인 동기화에서는 `MSTable`대신 이 동기화 테이블 인터페이스를 사용합니다. 동기화 테이블을 사용하면 모든 작업이 로컬 저장소로 이동하고 명시적 푸시 및 끌어오기 작업이 있는 원격 백 엔드와만 동기화됩니다.
   
    동기화 테이블에 대한 참조를 얻으려면 `MSClient`에서 `syncTableWithName` 메서드를 사용합니다. 오프라인 동기화 기능을 제거하려면 대신 `tableWithName` 을(를) 사용합니다.
2. 모든 테이블 작업을 수행하려면 먼저 로컬 저장소를 초기화해야 합니다. 관련 코드는 다음과 같습니다. 
   
    **Objective-C**:
   
    `QSTodoService.init` 메서드:

            MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
            self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];


    **Swift**:

    `ToDoTableViewController.viewDidLoad` 메서드:


            let client = MSClient(applicationURLString: "http:// ...") // URI of the Mobile App
            let managedObjectContext = (UIApplication.sharedApplication().delegate as! AppDelegate).managedObjectContext!
            self.store = MSCoreDataStore(managedObjectContext: managedObjectContext)
            client.syncContext = MSSyncContext(delegate: nil, dataSource: self.store, callback: nil)


    이 코드는 모바일 앱 SDK에 제공된 `MSCoreDataStore`인터페이스를 사용하여 로컬 저장소를 만듭니다. `MSSyncContextDataSource` 프로토콜을 구현하여 다른 로컬 저장소를 대신 제공할 수 있습니다. 

    또한 `MSSyncContext` 의 첫 번째 매개 변수는 충돌 처리기를 지정하는 데 사용됩니다. 여기서는 `nil`을 전달했으므로 충돌 발생 시 작업을 중단하는 기본 충돌 처리기를 얻게 됩니다.

1. 이제 실제 동기화 작업을 수행하여 원격 백 엔드에서 데이터를 가져와 보겠습니다.
   
    **Objective-C**:
   
    `syncData`는 새 변경 내용을 푸시한 다음 `pullData`를 호출하여 원격 백 엔드에서 데이터를 가져옵니다. 그러면 `pullData` 메서드는 쿼리와 일치하는 새 데이터를 가져옵니다.

            -(void)syncData:(QSCompletionBlock)completion
            {
                // push all changes in the sync context, then pull new data
                [self.client.syncContext pushWithCompletion:^(NSError *error) {
                    [self logErrorIfNotNil:error];
                    [self pullData:completion];
                }];
            }

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


      **Swift**:


        func onRefresh(sender: UIRefreshControl!) {
            UIApplication.sharedApplication().networkActivityIndicatorVisible = true

            self.table!.pullWithQuery(self.table?.query(), queryId: "AllRecords") {
                (error) -> Void in

                UIApplication.sharedApplication().networkActivityIndicatorVisible = false

                if error != nil {
                    // A real application would handle various errors like network conditions,
                    // server conflicts, etc via the MSSyncContextDelegate
                    print("Error: \(error!.description)")

                    // We will just discard our changes and keep the servers copy for simplicity
                    if let opErrors = error!.userInfo[MSErrorPushResultKey] as? Array<MSTableOperationError> {
                        for opError in opErrors {
                            print("Attempted operation to item \(opError.itemId)")
                            if (opError.operation == .Insert || opError.operation == .Delete) {
                                print("Insert/Delete, failed discarding changes")
                                opError.cancelOperationAndDiscardItemWithCompletion(nil)
                            } else {
                                print("Update failed, reverting to server's copy")
                                opError.cancelOperationAndUpdateItem(opError.serverItem!, completion: nil)
                            }
                        }
                    }
                }
                self.refreshControl?.endRefreshing()
            }
        } 


    Objective-C 버전의 `syncData`에서 먼저 동기화 컨텍스트에 대해 `pushWithCompletion`을 호출합니다. 이 메서드는 모든 테이블에서 변경 내용을 푸시하므로 동기화 테이블 자체가 아닌 `MSSyncContext`의 멤버입니다. CUD 작업을 통해 로컬에서 수정된 레코드만 서버에 전송됩니다. 그런 다음 `pullData` 도우미가 호출됩니다. 이 도우미는 `MSSyncTable.pullWithQuery`를 호출하여 원격 데이터를 검색하고 로컬 데이터베이스에 저장합니다.

    Swift 버전에는 `pushWithCompletion`에 대한 호출이 없습니다. 그렇기 때문에 푸시 작업이 반드시 필요한 것은 아닙니다. 푸시 작업을 수행하는 테이블에 대한 동기화 컨텍스트에 보류 중인 변경 내용이 있는 경우 끌어오기가 항상 푸시 작업을 먼저 실행합니다. 그러나 둘 이상의 동기화 테이블이 있는 경우 푸시를 명시적으로 호출하여 관련 테이블에서 모든 항목의 일관성을 유지하는 것이 좋습니다.

    Objective-C 버전과 Swift 버전 모두에서 `pullWithQuery` 메서드를 사용하면 쿼리를 지정하여 검색하려는 레코드를 필터링할 수 있습니다. 이 예제에서 쿼리는 원격 `TodoItem` 테이블의 모든 레코드를 검색합니다.

    `pullWithQuery` 에 대한 두 번째 매개 변수는 *증분 동기화*에 사용되는 쿼리 ID입니다. 증분 동기화는 레코드의 `UpdatedAt` 타임스탬프(로컬 저장소에서는 `updatedAt`이라고 함)를 사용하여 마지막 동기화 이후에 수정된 레코드만 검색합니다. 쿼리 ID는 앱의 각 논리 쿼리에 고유한 설명 문자열이어야 합니다. 증분 동기화를 옵트아웃하려면 `nil` 을 쿼리 ID로 전달합니다. 이 경우 각 끌어오기 작업에서 모든 레코드가 검색되므로 비효율적일 수 있습니다.

1. Objective-C 앱은 데이터를 추가하거나 수정할 때 사용자가 새로 고침 제스처를 수행할 때 및 시작 시 동기화됩니다. Swift 앱은 사용자가 새로 고침 제스처를 수행할 때 및 시작 시 동기화됩니다. 

데이터가 수정될 때마다(Objective-C) 또는 앱이 시작도리 때마다(Objective-C 및 Swift) 앱이 동기화되므로 앱은 사용자가 온라인 상태인 것으로 가정합니다. 다른 섹션에서는 오프라인 상태일 때도 편집할 수 있도록 앱을 업데이트합니다.

## <a name="a-namereview-core-dataareview-the-core-data-model"></a><a name="review-core-data"></a>핵심 데이터 모델 검토
핵심 데이터 오프라인 저장소를 사용하는 경우 데이터 모델에서 특정 테이블 및 필드를 정의해야 합니다. 샘플 앱에는 이미 올바른 형식의 데이터 모델이 포함되어 있습니다. 이 섹션에서는 이러한 테이블 및 사용 방법을 알아봅니다.

* **QSDataModel.xcdatamodeld**를 엽니다. SDK에서 사용되는 3개의 테이블과 이 할 일 항목 자체에 사용되는 1개의 테이블 등 모두 4개의 테이블이 정의되어 있습니다.
  * MS_TableOperations: 서버와 동기화해야 하는 항목 추적
  * MS_TableOperationErrors: 오프라인 동기화 중에 발생하는 모든 오류를 추적
  * MS_TableConfig: 모든 끌어오기 작업에 대한 마지막 동기화 작업의 마지막 업데이트 시간 추적
  * TodoItem: 할 일 항목 저장 시스템 열 **createdAt**, **updatedAt** 및 **version**은 선택적 시스템 속성입니다.

> [!NOTE]
> Azure 모바일 앱 SDK는 "**``**"로 시작하는 열 이름을 예약합니다. 시스템 열 이외의 항목에는 이 접두사를 사용하지 않아야 합니다. 그렇지 않으면 원격 백 엔드를 사용할 때 열 이름이 수정됩니다.
> 
> 

* 오프라인 동기화 기능을 사용할 경우 아래와 같이 시스템 테이블을 정의해야 합니다.
  
  ### <a name="system-tables"></a>시스템 테이블
    **MS_TableOperations**
  
    ![][defining-core-data-tableoperations-entity]
  
  | 특성 | 형식 |
  | --- | --- |
  | id |정수 64 |
  | itemId |String |
  | properties |이진 데이터 |
  | 테이블 |String |
  | tableKind |정수 16 |
  
    <br>**MS_TableOperationErrors**
  
    ![][defining-core-data-tableoperationerrors-entity]
  
  | 특성 | 형식 |
  | --- | --- |
  | id |문자열 |
  | operationId |정수 64 |
  | properties |이진 데이터 |
  | tableKind |정수 16 |
  
    <br>**MS_TableConfig**
  
    ![][defining-core-data-tableconfig-entity]
  
  | 특성 | 형식 |
  | --- | --- |
  | id |문자열 |
  | key |String |
  | keyType |정수 64 |
  | 테이블 |String |
  | 값 |문자열 |
  
  ### <a name="data-table"></a>데이터 테이블
    **TodoItem**
  
  | 특성 | 유형 | 참고 |
  | --- | --- | --- |
  | id |문자열, 필수로 표시 |원격 저장소의 기본 키 |
  | complete |Boolean |todo 항목 필드 |
  | 텍스트 |String |todo 항목 필드 |
  | createdAt |Date |(옵션) createdAt 시스템 속성에 매핑됩니다. |
  | updatedAt |Date |(옵션) updatedAt 시스템 속성에 매핑됩니다. |
  | 버전 |String |(옵션) 충돌을 검색하는 데 사용되며 version에 매핑됩니다. |

## <a name="a-namesetup-syncachange-the-sync-behavior-of-the-app"></a><a name="setup-sync"></a>앱의 동기화 동작 변경
이 섹션에서는 앱을 시작할 때나 항목을 삽입 및 업데이트할 때는 앱이 동기화되지 않고 새로 고침 제스처 단추를 누를 때만 동기화되도록 앱을 수정합니다.

**Objective-C**:

1. **QSTodoListViewController.m**에서 **viewDidLoad** 메서드가 끝날 때 `[self refresh]` 호출을 제거하도록 이 메서드를 변경합니다. 이제 데이터가 앱 시작 시에는 서버와 동기화되지 않고 대신 로컬 저장소의 내용이 됩니다.
2. **QSTodoService.m**에서 항목이 삽입된 후에 동기화되지 않도록 `addItem`의 정의를 수정합니다. `self syncData` 블록을 제거하고 다음 코드로 바꿉니다.
   
            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }
3. 위와 같이 `completeItem`의 정의를 수정하고 `self syncData`에 대한 블록을 제거한 후 다음 코드로 바꿉니다.
   
            if (completion != nil) {
                dispatch_async(dispatch_get_main_queue(), completion);
            }

**Swift**:

1. 앱 시작 시 동기화를 중지하려면 **ToDoTableViewController.swift**의 `viewDidLoad`에서 이 두 줄을 주석 처리합니다. 이 문서를 작성할 당시에는 Swift Todo 앱이 누군가가 항목을 추가하거나 완료할 때는 서비스를 업데이트하지 않고 앱 시작 시에만 서비스를 업데이트했습니다.
   
        self.refreshControl?.beginRefreshing()
        self.onRefresh(self.refreshControl)

## <a name="a-nametest-appatest-the-app"></a><a name="test-app"></a>앱 테스트
이 섹션에서 오프라인 시나리오를 시뮬레이션하면 잘못된 URL로 연결됩니다. 데이터 항목을 추가하면 모바일 백 엔드에 동기화되지 않고 로컬 핵심 데이터 저장소에 보관됩니다.

1. **QSTodoService.m** 의 모바일 앱 URL을 잘못된 URL로 변경하고 앱 다시 실행하기:
   
    **Objective-C** 의 QSTodoService.m:
   
            self.client = [MSClient clientWithApplicationURLString:@"https://sitename.azurewebsites.net.fail"];
   
    **Swift** 의 ToDoTableViewController.swift:
   
        let client = MSClient(applicationURLString: "https://sitename.azurewebsites.net.fail")
2. 몇 가지 할 일 항목을 추가합니다. 시뮬레이터를 끝내고(또는 강제로 앱 닫기) 다시 시작합니다. 변경 내용이 유지되는지 확인합니다.
3. 원격 TodoItem 테이블의 내용 확인
   
   * Node.js 백 엔드의 경우 [Azure Portal](https://portal.azure.com/)로 이동하여 모바일 앱 백 엔드에서 **쉬운 테이블** > **TodoItem**을 클릭하여 `TodoItem` 테이블의 내용을 봅니다.
     
     * .NET 백 엔드의 경우 SQL Server Management Studio와 같은 SQL 도구나 Fiddler 또는 Postman 같은 REST 클라이언트를 사용하여 테이블 내용을 봅니다.
     
     새 항목이 서버와 동기화되지 *않았는지* 확인합니다.
4. **QSTodoService.m** 의 URL을 올바르게 다시 변경하고 해당 앱을 다시 실행합니다. 항목 목록을 아래로 끌어서 새로 고침 제스처를 수행합니다. 진행률 회전자가 표시됩니다.
5. TodoItem 데이터를 다시 봅니다. 이제 새 및 변경된 TodoItems가 나타납니다.

## <a name="summary"></a>요약
오프라인 동기화 기능을 지원하기 위해 `MSSyncTable` 인터페이스를 사용하고 로컬 저장소를 사용하여 `MSClient.syncContext`를 초기화했습니다. 이 경우 로컬 저장소는 핵심 데이터 기반 데이터베이스였습니다.

핵심 데이터 로컬 저장소를 사용할 경우 [올바른 시스템 속성](#review-core-data)을 사용하여 여러 테이블을 정의해야 합니다.

Azure 모바일 앱에 대한 일반적인 CRUD 작업은 앱이 계속 연결되어 있는 것처럼 작동하지만 모든 작업이 로컬 저장소에 대해 수행됩니다.

서버와 로컬 저장소를 동기화하려는 경우 `MSSyncTable.pullWithQuery`메서드를 사용했습니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure 모바일 앱에서 오프라인 데이터 동기화]
* [Cloud Cover: Azure Mobile Services에서 오프라인 동기화] \(참고: 비디오는 Mobile Services에 있지만 Azure Mobile Apps에서 비슷한 방식으로 오프라인 동기화가 작동합니다.\)

<!-- URLs. -->


[iOS 앱 만들기]: app-service-mobile-ios-get-started.md
[Azure 모바일 앱에서 오프라인 데이터 동기화]: app-service-mobile-offline-data-sync.md

[defining-core-data-tableoperationerrors-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/app-service-mobile-ios-get-started-offline-data/defining-core-data-todoitem-entity.png

[Cloud Cover: Azure Mobile Services에서 오프라인 동기화]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Azure 모바일 서비스의 오프라인 지원 앱]: http://azure.microsoft.com/en-us/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/



<!--HONumber=Nov16_HO3-->


