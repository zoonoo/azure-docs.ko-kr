<properties urlDisplayName="Using Offline Data" pageTitle="모바일 서비스에서 오프라인 데이터 동기화 사용(iOS) | 모바일 개발자 센터" metaKeywords="" description="Azure 모바일 서비스를 사용하여 iOS 응용 프로그램에서 오프라인 데이터를 캐시 및 동기화하는 방법에 대해 알아봅니다." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using Offline data sync in Mobile Services" authors="krisragh" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh" />

# 모바일 서비스에서 오프라인 데이터 동기화 시작


[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


이 자습서에서는 최종 사용자에게 네트워크 액세스가 없어도 사용 가능한 앱을 개발자가 작성할 수 있게 해주는 iOS의 모바일 서비스 오프라인 동기화 기능에 대해 설명합니다.

오프라인 동기화의 몇 가지 잠재적인 용도는 다음과 같습니다.

* 서버 데이터를 장치에 로컬로 캐시하여 앱 응답성 향상
* 일시적인 네트워크 연결 중단에도 앱 복원력 제공
* 최종 사용자가 네트워크에 액세스할 수 없는 경우에도 데이터를 만들고 수정할 수 있도록 허용하여 네트워크에 연결되지 않은 시나리오까지 지원
* 여러 장치 간에 데이터를 동기화하고 동일한 레코드를 두 개의 장치에서 수정할 때 충돌 감지

이 자습서에서는 [모바일 서비스 시작] 자습서에서 작성한 앱을 업데이트하여 Azure 모바일 서비스의 오프라인 기능을 지원하는 방법을 보여 줍니다. 그런 다음, 연결이 끊긴 오프라인 시나리오에서 데이터를 추가하고 해당 항목을 온라인 데이터베이스와 동기화한 후 Azure 관리 포털에 로그인하여 앱을 실행할 때 수행된 데이터 변경 사항을 확인합니다.

>[WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서는 Windows 스토어 앱에서 모바일 서비스를 통해 Azure를 사용하여 데이터를 저장하고 검색할 수 있는 방법을 더욱 잘 이해할 수 있도록 돕기 위한 것입니다. 이 항목에서는 모바일 서비스 빠른 시작에서 완료한 다수의 단계를 순서대로 안내합니다. 모바일 서비스를 처음 사용하는 경우 먼저 [모바일 서비스 시작] 자습서를 완료하는 것이 좋습니다.

>[WACOM.NOTE] 이러한 섹션을 건너뛰고 오프라인 지원이 이미 제공되는 시작 프로젝트 버전과 이 항목에 설명된 모든 항목을 다운로드할 수 있습니다.  오프라인 지원이 설정된 프로젝트를 다운로드하려면 [오프라인 iOS 샘플 시작]을 참조하세요.


이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [샘플 빠른 시작 앱 가져오기]
2. [Preview SDK 다운로드 및 프레임워크 업데이트]
3. [핵심 데이터 설정]
4. [핵심 데이터 모델 정의]
5. [동기화 테이블 및 동기화 컨텍스트 초기화와 사용]
6. [앱 테스트]

## <a name="get-app"></a>샘플 빠른 시작 앱 가져오기

[모바일 서비스 시작]의 지침에 따라 빠른 시작 프로젝트를 다운로드합니다.

## <a name="update-app"></a>Preview SDK 다운로드 및 프레임워크 업데이트

1. 응용 프로그램에 오프라인 지원을 추가하기 위해 오프라인 동기화를 지원하는 모바일 서비스 iOS SDK 버전을 가져오겠습니다. 미리 보기 기능으로 출시하고 있으므로 아직 공식적으로 다운로드할 수 있는 SDK에는 없습니다. [여기에서 미리 보기 SDK를 다운로드하세요].

2. 그런 다음 기존 **WindowsAzureMobileServices.framework** 참조를 선택하고 **편집** 메뉴를 클릭을 선택한 후 "휴지통으로 이동"을 선택하여 파일을 실제로 삭제합니다.

      ![][update-framework-1]

3. 새 미리 보기 SDK 콘텐츠의 압축을 풀고 이전 SDK 위에 새 **WindowsAzureMobileServices.framework** SDK를 끌어다 놓습니다. "Copy items into destination group's folder (if needed)(대상 그룹의 폴더로 항목 복사(필요한 경우))"를 선택했는지 확인합니다.

      ![][update-framework-2]


## <a name="setup-core-data"></a>핵심 데이터 설정

1. IOS 모바일 서비스 SDK에서는 **MSSyncContextDataSource** 프로토콜을 준수하는 한, 어떤 영구 저장소도 사용할 수 있습니다. SDK에는 [핵심 데이터]를 기반으로 하는 이 프로토콜을 구현하는 데이터 소스가 포함되어 있습니다.

2. 앱은 핵심 데이터를 사용하므로 **대상** --> **Build Phases**로 이동한 후 **Link Binary with Libraries** 에서 **CoreData.framework**를 추가합니다..

      ![][core-data-1]

      ![][core-data-2]

3. 아직 핵심 데이터를 지원하지 않는 Xcode이 기존 프로젝트에 핵심 데이터 추가합니다. 이와 같이 프로젝트의 다양한 부분에 추가 상용구 코드를 추가해야 합니다. 먼저 **QSAppDelegate.h**에 다음 코드를 추가 합니다.

        #import <UIKit/UIKit.h>  
        #import <CoreData/CoreData.h>  

        @interface QSAppDelegate : UIResponder <UIApplicationDelegate>  

        @property (strong, nonatomic) UIWindow *window;  

        @property (readonly, strong, nonatomic) NSManagedObjectContext *managedObjectContext;  
        @property (readonly, strong, nonatomic) NSManagedObjectModel *managedObjectModel;  
        @property (readonly, strong, nonatomic) NSPersistentStoreCoordinator *persistentStoreCoordinator;  

        - (void)saveContext;  
        - (NSURL *)applicationDocumentsDirectory;  

        @end

4. 다음으로 **QSAppDelegate.m**의 콘텐츠를 다음 코드로 바꿉니다. 이 코드는 **_managedObjectContext**를 초기화할 때 개인 큐 동시성 형식을 사용하는 경우를 제외하고 Xcode에서 새 응용 프로그램을 만들고 "핵심 데이터 사용" 확인란을 선택할 때 얻게 되는 코드와 거의 같습니다. 이와 같이 변경하면 핵심 데이터를 사용할 준비가 거의 완료되었지만 아직 핵심 데이터로 작업을 수행하지는 않을 것입니다.

        #import "QSAppDelegate.h"

        @implementation QSAppDelegate

        @synthesize managedObjectContext = _managedObjectContext;
        @synthesize managedObjectModel = _managedObjectModel;
        @synthesize persistentStoreCoordinator = _persistentStoreCoordinator;

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
        {
            return YES;
        }

        - (void)saveContext
        {
            NSError *error = nil;
            NSManagedObjectContext *managedObjectContext = self.managedObjectContext;
            if (managedObjectContext != nil) {
                if ([managedObjectContext hasChanges] && ![managedObjectContext save:&error]) {
                    // Replace this implementation with code to handle the error appropriately.
                    // abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.
                    NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                    abort();
                }
            }
        }

        #pragma mark - Core Data stack

        // Returns the managed object context for the application.
        // If the context doesn't already exist, it is created and bound to the persistent store coordinator for the application.
        - (NSManagedObjectContext *)managedObjectContext
        {
            if (_managedObjectContext != nil) {
                return _managedObjectContext;
            }

            NSPersistentStoreCoordinator *coordinator = [self persistentStoreCoordinator];
            if (coordinator != nil) {
                _managedObjectContext = [[NSManagedObjectContext alloc] initWithConcurrencyType:NSPrivateQueueConcurrencyType];
                [_managedObjectContext setPersistentStoreCoordinator:coordinator];
            }
            return _managedObjectContext;
        }

        // Returns the managed object model for the application.
        // If the model doesn't already exist, it is created from the application's model.
        - (NSManagedObjectModel *)managedObjectModel
        {
            if (_managedObjectModel != nil) {
                return _managedObjectModel;
            }
            NSURL *modelURL = [[NSBundle mainBundle] URLForResource:@"QSTodoDataModel" withExtension:@"momd"];
            _managedObjectModel = [[NSManagedObjectModel alloc] initWithContentsOfURL:modelURL];
            return _managedObjectModel;
        }

        // Returns the persistent store coordinator for the application.
        // If the coordinator doesn't already exist, it is created and the application's store added to it.
        - (NSPersistentStoreCoordinator *)persistentStoreCoordinator
        {
            if (_persistentStoreCoordinator != nil) {
                return _persistentStoreCoordinator;
            }

            NSURL *storeURL = [[self applicationDocumentsDirectory] URLByAppendingPathComponent:@"qstodoitem.sqlite"];

            NSError *error = nil;
            _persistentStoreCoordinator = [[NSPersistentStoreCoordinator alloc] initWithManagedObjectModel:[self managedObjectModel]];
            if (![_persistentStoreCoordinator addPersistentStoreWithType:NSSQLiteStoreType configuration:nil URL:storeURL options:nil error:&error]) {
                /*
                 Replace this implementation with code to handle the error appropriately.

                 abort() causes the application to generate a crash log and terminate. You should not use this function in a shipping application, although it may be useful during development.

                 Typical reasons for an error here include:
                 * The persistent store is not accessible;
                 * The schema for the persistent store is incompatible with current managed object model.
                 Check the error message to determine what the actual problem was.

                 If the persistent store is not accessible, there is typically something wrong with the file path. Often, a file URL is pointing into the application's resources directory instead of a writeable directory.

                 If you encounter schema incompatibility errors during development, you can reduce their frequency by:
                 * Simply deleting the existing store:
                 [[NSFileManager defaultManager] removeItemAtURL:storeURL error:nil]

                 * Performing automatic lightweight migration by passing the following dictionary as the options parameter:
                 @{NSMigratePersistentStoresAutomaticallyOption:@YES, NSInferMappingModelAutomaticallyOption:@YES}

                 Lightweight migration will only work for a limited set of schema changes; consult "Core Data Model Versioning and Data Migration Programming Guide" for details.

                 */

                NSLog(@"Unresolved error %@, %@", error, [error userInfo]);
                abort();
            }

            return _persistentStoreCoordinator;
        }

        #pragma mark - Application's Documents directory

        // Returns the URL to the application's Documents directory.
        - (NSURL *)applicationDocumentsDirectory
        {
            return [[[NSFileManager defaultManager] URLsForDirectory:NSDocumentDirectory inDomains:NSUserDomainMask] lastObject];
        }

        @end

## <a name="defining-core-data"></a>핵심 데이터 모델 정의

1. 데이터 모델을 정의하여 핵심 데이터로 응용 프로그램을 설정해봅니다. 아직 이 데이터 모델을 사용하지는 않을 것입니다. 먼저 핵심 데이터 모델 또는 스키마를 정의해보겠습니다. 시작하려면 **파일** -> **새 파일**을 클릭하고 **핵심 데이터** 섹션에서 **데이터 모델**을 선택합니다. 파일 이름을 묻는 메시지가 표시되면 **QSTodoDataModel.xcdatamodeld**를 사용합니다.

      ![][defining-core-data-main-screen]

2. 다음으로 필요한 실제 엔터티(테이블)를 정의해보겠습니다. 핵심 데이터 모델 편집기를 사용하여 세 개의 테이블(엔터티)를 만들어 보겠습니다. 자세한 내용은 [핵심 데이터 모델 편집기 도움말]을 참조하세요.

  * TodoItem: 항목 자체 저장
  * MS_TableOperations: 서버와 동기화해야 하는 항목 추적(오프라인 기능 작동에 필요)
  * MS_TableOperationErrors: 오프라인 동기화 중에 발생하는 모든 오류 추적(오프라인 기능 작동에 필요)

      ![][defining-core-data-model-editor]

3. 아래와 같이 세 개의 엔터티를 정의합니다. 모델을 저장하고 프로젝트를 빌드하여 모든 항목에 문제가 없도록 합니다. 이제 핵심 데이터가 사용되도록 응용 프로그램 설정을 완료했지만 앱을 아직 사용하지는 않을 것입니다.

      ![][defining-core-data-todoitem-entity]

      ![][defining-core-data-tableoperations-entity]

      ![][defining-core-data-tableoperationerrors-entity]


    **TodoItem**

    | 특성  |  형식   |
    |----------- |  ------ |
    | id         | 문자열  |
    | complete   | 부울 |
    | text       | 문자열  |
    | ms_version | 문자열  |

    **MS_TableOperations**

    | 특성  |    형식     |
    |----------- |   ------    |
    | id         | Integer 64  |
    | properties | 이진 데이터 |
    | itemId     | 문자열      |
    | table      | 문자열      |

    **MS_TableOperationErrors**

    | 특성  |    형식     |
    |----------- |   ------    |
    | id         | 문자열      |
    | properties | 이진 데이터 |

## <a name="setup-sync"></a> 동기화 테이블 및 동기화 컨텍스트 초기화와 사용

1. 오프라인 데이터 캐시를 시작하려면 용도를 **MSTable**에서 **MSSyncTable**로 바꾸어 모바일 서비스에 액세스합니다. 일반적인 **MSTable**와 달리 동기화 테이블은 로컬로 변경된 내용을 원격 테이블에 푸시하고 해당 변경 내용을 로컬로 끌어오는 기능을 추가하는 로컬 테이블과 비슷합니다. **QSTodoService.h**에서 **table** 속성의 정의를 제거합니다.

        @property (nonatomic, strong)   MSTable *table;

    Add a new line to define the **syncTable** property:

        @property (nonatomic, strong)   MSTable *syncTable;

2. Add the following import statement at the top of **QSTodoService.m**:

        #import "QSAppDelegate.h"

3. **QSTodoService.m**에서 **init**의 다음 두 줄을 제거합니다.

        // Create an MSTable instance to allow us to work with the TodoItem table
        self.table = [_client tableWithName:@"TodoItem"];

    Instead, add these two new lines in its place:

        // Create an MSSyncTable instance to allow us to work with the TodoItem table
        self.syncTable = [self.client syncTableWithName:@"TodoItem"];

4. 다음으로 **QSTodoService.m**으로 다시 가서 위의 핵심 데이터 기반 데이터 저장소 구현으로 **MSClient**의 동기화 컨텍스트를 초기화합니다. 이 컨텍스트는 로컬로 변경된 항목을 추적한 후 푸시 작업이 시작될 때 서버로 전송합니다. 이 컨텍스트를 초기화하려면 데이터 소스(프로토콜의 **MSCoreDataStore** 구현) 및 선택적 **MSSyncContextDelegate** 구현)가 필요합니다. 위에서 삽입한 두 줄 바로 위에 다음 줄을 삽입합니다.

        QSAppDelegate *delegate = (QSAppDelegate *)[[UIApplication sharedApplication] delegate];
        NSManagedObjectContext *context = delegate.managedObjectContext;
        MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];

5. 다음으로 일반 테이블 대신 동기화 테이블을 사용하도록 **QSTodoService.m**의 작업을 업데이트합니다. 먼저 **refreshDataOnSuccess**를 다음 구현으로 바꿉니다. 이렇게 하면 서비스에서 데이터가 검색되므로 동기화 테이블을 사용하도록 업데이트하고, 동기화 테이블에 조건에 맞는 항목만 끌어오도록 요청한 후 로컬 동기화 테이블의 데이터를 서비스의 **items** 속성에 로드하기 시작합니다. 이 코드를 통해   **refreshDataOnSuccess**는 원격 테이블의 데이터를 로컬(동기화) 테이블로 끌어옵니다. 필요하지 않은 정보로 클라이언트를 오버로드하지 않도록 테이블의 일부만 끌어오는 것이 좋습니다.

    이 작업과 아래에 나오는 추가 작업의 경우 완료 블록에 대한 호출을 주 스레드에 대한 **dispatch_async** 호출에 래핑합니다. 동기화 컨텍스트를 초기화할 때는 콜백 매개 변수를 제공하지 않으므로 프레임워크는 모든 syncTable 작업의 결과를 백그라운드 스레드로 디스패치하는 기본 직렬 큐를 만듭니다. UI 구성 요소를 수정하는 경우 이 코드를 UI 스레드로 다시 디스패치해야 합니다.

          -(void) refreshDataOnSuccess:(QSCompletionBlock)completion
          {
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];
              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              [query orderByAscending:@"text"];
              [query readWithCompletion:^(MSQueryResult *result, NSError *error) {
                  [self logErrorIfNotNil:error];

                  self.items = [result.items mutableCopy];

                  // Let the caller know that we finished
                  dispatch_async(dispatch_get_main_queue(), ^{
                      completion();
                  });
              }];
          }

6. 다음에는 **QSTodoService.m**에서 **addItem**을 다음과 같이 바꿉니다. 이와 같이 변경하면서 변경 내용을 원격 서비스에 푸시하고 모든 사용자가 볼 수 있도록 작업을 큐에 대기하게 됩니다.

        -(void)addItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Insert the item into the TodoItem table and add to the items array on completion
            [self.syncTable insert:item completion:^(NSDictionary *result, NSError *error)
             {
                 [self logErrorIfNotNil:error];

                 NSUInteger index = [items count];
                 [(NSMutableArray *)items insertObject:result atIndex:index];

                 // Let the caller know that we finished
                 dispatch_async(dispatch_get_main_queue(), ^{
                     completion(index);
                 });
             }];
        }

7. **QSTodoService.m**에서 다음과 같이 **completeItem**을 업데이트합니다. **MSTable**의 경우와 달리 **MSSyncTable**에 대한 **업데이트** 작업의 완료 블록에는 업데이트된 항목이 없습니다. **MSTable**을 사용할 경우 서버는 업데이트 중인 항목을 수정하고 수정 내용은 클라이언트에 반영됩니다. **MSSyncTable**을 사용하는 경우 업데이트된 항목이 수정되지 않고 완료 블록에는 매개 변수가 없습니다.

        -(void) completeItem:(NSDictionary *)item completion:(QSCompletionWithIndexBlock)completion
        {
            // Cast the public items property to the mutable type (it was created as mutable)
            NSMutableArray *mutableItems = (NSMutableArray *) items;

            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            [mutable setObject:@YES forKey:@"complete"];

            // Replace the original in the items array
            NSUInteger index = [items indexOfObjectIdenticalTo:item];
            [mutableItems replaceObjectAtIndex:index withObject:item];

            // Update the item in the TodoItem table and remove from the items array on completion
            [self.syncTable update:mutable completion:^(NSError *error) {

                [self logErrorIfNotNil:error];

                NSUInteger index = [items indexOfObjectIdenticalTo:mutable];
                if (index != NSNotFound)
                {
                    [mutableItems removeObjectAtIndex:index];
                }

                // Let the caller know that we have finished
                dispatch_async(dispatch_get_main_queue(), ^{
                    completion(index);
                });

            }];
        }

8. **syncData**의 다음 작업 선언을 **QSTodoService.h**에 추가합니다.

        - (void)syncData:(QSCompletionBlock)completion;

     Add the corresponding implementation of **syncData** to **QSTodoService.m**. We're adding this operation to update the sync table with remote changes.

          -(void)syncData:(QSCompletionBlock)completion
           {
              // Create a predicate that finds items where complete is false
              NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

              MSQuery *query = [self.syncTable queryWithPredicate:predicate];

              // Pulls data from the remote server into the local table. We're only
              // pulling the items which we want to display (complete == NO).
              [self.syncTable pullWithQuery:query completion:^(NSError *error) {
                  [self logErrorIfNotNil:error];
                  [self refreshDataOnSuccess:completion];
           }];
          }

9. Back in **QSTodoListViewController.m**, change the implementation of **refresh** to call **syncData** instead of **refreshDataOnSuccess**:

        -(void) refresh
        {
            [self.refreshControl beginRefreshing];
            [self.todoService syncData:^
             {
                  [self.refreshControl endRefreshing];
                  [self.tableView reloadData];
             }];
        }

10. 다시 **QSTodoListViewController.m**으로 와서 **viewDidLoad** 작업 끝에서 **[self refresh]**에 대한 호출을 다음 코드로 바꿉니다.

        // load the local data, but don't pull from server
        [self.todoService refreshDataOnSuccess:^
         {
             [self.refreshControl endRefreshing];
             [self.tableView reloadData];
         }];

11. 이제 오프라인에서 실제로 앱을 테스트해보겠습니다. 앱에 일부 항목을 추가한 다음 Azure 관리 포털을 방문하고 앱에 대한 **데이터** 탭을 확인합니다. 아직 아무 항목도 추가되지 않을 것을 알 수 있습니다.

12. 다음에는 위쪽에서 끌어와 앱에 대해 새로 고침 제스처를 수행합니다. 그런 다음 Azure 관리 포털을 다시 방문하고 **데이터** 탭을 다시 확인합니다. 이제 해당 데이터가 클라우드에 저장되어 있을 것을 볼 수 있습니다. 항목을 추가한 후 앱을 닫을 수도 있습니다(또는 앱에 항목 편집 기능이 설정된 경우 편집한 후에). 앱이 다시 시작되면 여전히 서버와 동기화되고 변경 내용이 저장됩니다.

13. 클라이언트가 로컬로 항목을 일부 변경하면 해당 변경 내용이 서버로 전송될 동기화 컨텍스트에 저장됩니다. *푸시* 작업은 추적한 변경 내용을 원격 서버로 보내지만 여기서는 서버에 대한 푸시 호출이 없습니다. 그러나 *끌어오기를 실행하기 전에 보류 중인 모든 작업은 일반적으로 서버로 푸시되므로* 충돌 방지를 위해 여전히 자동으로 푸시가 진행됩니다. 이 때문에 이 앱에 명시적인 *푸시* 호출이 없는 것입니다.

## <a name="test-app"></a>앱 테스트

마지막으로 오프라인에서 응용 프로그램을 테스트해보겠습니다. 앱에 몇 가지 항목을 추가합니다. 그런 다음 포털로 이동하고 데이터를 찾아봅니다(또는 PostMan이나 Fiddler 같은 네트워킹 도구를 사용하여 테이블을 직접 쿼리).

항목이 서비스에 아직 추가되지 않았음을 알 수 있습니다. 그런 다음 앱의 맨 위에서 끌어 앱에서 새로 고침 제스처를 수행합니다. 이제 해당 데이터가 클라우드에 저장되어 있을 것을 볼 수 있습니다. 일부 항목을 추가한 후 앱을 닫을 수도 있습니다. 앱을 다시 시작하면 서버와 동기화되고 변경 내용이 저장됩니다.

## 다음 단계

* [모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]

<!-- URLs. -->

[샘플 빠른 시작 앱 가져오기]: #get-app
[Preview SDK 다운로드 및 프레임워크 업데이트]: #update-app
[핵심 데이터 설정]: #setup-core-data
[핵심 데이터 모델 정의]: #defining-core-data
[동기화 테이블 및 동기화 컨텍스트 초기화와 사용]: #setup-sync
[앱 테스트]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png




[핵심 데이터 모델 편집기 도움말]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[콘센트 연결 만들기]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[사용자 인터페이스 작성]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[스토리보드에서 장면 사이에 연결선 추가]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[스토리보드에 장면 추가]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[핵심 데이터]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[여기에서 미리 보기 SDK 다운로드]: http://aka.ms/Gc6fex
[iOS용 모바일 서비스 클라이언트 라이브러리 사용 방법]: /ko-kr/documentation/articles/mobile-services-ios-how-to-use-client-library/
[오프라인 iOS 샘플 시작]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611


[모바일 서비스 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started/
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-data/
[모바일 서비스에 대한 오프라인 지원을 통해 충돌 처리]: /ko-kr/documentation/articles/mobile-services-ios-handling-conflicts-offline-data/
