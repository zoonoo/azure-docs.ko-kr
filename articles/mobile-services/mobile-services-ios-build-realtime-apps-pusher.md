<properties 
	pageTitle="Pusher를 사용하여 실시간 앱 빌드(iOS) - 모바일 서비스" 
	description="Pusher를 사용하여 iOS에서 Azure 미디어 서비스에 알림을 보내는 방법에 대해 알아봅니다." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="06/05/2015" 
	ms.author="donnam"/>


# 모바일 서비스 및 Pusher를 사용하여 실시간 앱 빌드
<div class="dev-center-tutorial-selector sublanding">
	<a href="" title="iOS" class="current">iOS</a>
</div>

이 항목에서는 Azure 모바일 서비스 기반 앱에 실시간 기능을 추가하는 방법을 보여 줍니다. 완료하면 실행 중인 모든 앱 인스턴스에서 TodoList 데이터가 실시간으로 동기화됩니다.

[사용자에게 푸시 알림][] 자습서에서는 푸시 알림을 사용하여 할 일 목록의 새 항목을 사용자에게 알리는 방법을 보여 줍니다. 푸시 알림은 가끔 발생하는 변경 내용을 표시하는 데 유용한 방법입니다. 그러나 앱에 빈번한 실시간 알림이 필요한 경우도 있습니다. Pusher API를 사용하여 모바일 서비스에 실시간 알림을 추가할 수 있습니다. 이 자습서에서는 모바일 서비스와 함께 Pusher를 사용하여 실행 중인 앱 인스턴스에서 변경 시 할 일 목록을 동기화된 상태로 유지합니다.

Pusher는 모바일 서비스처럼 실시간 앱을 쉽게 빌드할 수 있게 해주는 클라우드 기반 서비스입니다. Pusher를 사용하여 라이브 설문 조사, 채팅방, 멀티플레이어 게임, 공동 작업 앱을 빠르게 빌드하고 라이브 데이터와 콘텐츠를 브로드캐스트하며 그 밖의 여러 작업을 수행할 수 있습니다. 자세한 내용은 [http://pusher.com](http://pusher.com)을 참조하십시오.

이 자습서에서는 할 일 목록 응용 프로그램에 실시간 공동 작업을 추가하기 위한 기본 단계를 단계별로 안내합니다.

1. [Pusher 계정 만들기][]
2. [앱 업데이트][]
3. [서버 스크립트 설치][]
4. [앱 테스트][]

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][]를 완료해야 합니다.

## <a name="sign-up"></a>새 Pusher 계정 만들기

[AZURE.INCLUDE [pusher-sign-up](../../includes/pusher-sign-up.md)]

## <a name="update-app"></a>앱 업데이트

이제 Pusher 계정이 설정되었으므로 다음 단계는 새 기능을 위해 iOS 앱 코드를 수정하는 것입니다.

###libPusher 라이브러리 설치

[libPusher][] 라이브러리를 사용하면 iOS에서 Pusher에 액세스할 수 있습니다.

1. [여기][libPusherDownload]에서 libPusher 라이브러리를 다운로드합니다.

2. 프로젝트에 _libPusher_라는 그룹을 만듭니다.

3. Finder에서 다운로드한 zip 파일의 압축을 풀고 **libPusher-combined.a** 및 **/headers** 폴더를 선택한 후 프로젝트의 **libPusher** 그룹으로 끌어 놓습니다.

4. **Copy items into destination group's folder**를 선택하고 **Finish**를 클릭합니다.

	![][add-files-to-group]

   libPusher 파일이 프로젝트에 복사됩니다.

5. Project Explorer의 프로젝트 루트에서 **Build Phases**를 클릭한 후 **Add Build Phase** 및 **Add Copy Files**를 클릭합니다.

6. Project Explorer에서 새 빌드 단계로 **libPusher-combined.a** 파일을 끌어 놓습니다.

7. **Destination**을 **Frameworks**로 변경하고 **Copy only when installing**을 클릭합니다.

	![][add-build-phase]

8. **Link Binary With Libraries** 영역 내에 다음 라이브러리를 추가합니다.

	- libicucore.dylib
	- CFNetwork.framework
	- Security.framework
	- SystemConfiguration.framework

9. 마지막으로, **Build Settings** 내에서 대상 빌드 설정 **Other Linker Flags**를 찾은 후 **-all_load** 플래그를 추가합니다.

	![][add-linker-flag]

	Debug build target에 대해 **-all_load** 플래그가 설정됩니다.

이제 라이브러리가 설치되고 사용할 준비가 되었습니다.

### 응용 프로그램에 코드 추가

1. Xcode에서 **QSTodoService.h** 파일을 열고 다음 메서드 선언을 추가합니다.

        // Allows retrieval of items by id
        - (NSUInteger) getItemIndex:(NSDictionary *)item;

        // To be called when items are added by other users
        - (NSUInteger) itemAdded:(NSDictionary *)item;

        // To be called when items are completed by other users
        - (NSUInteger) itemCompleted:(NSDictionary *)item;

2. 기존 **addItem** 및 **completeItem** 선언을 다음으로 바꿉니다.

		- (void) addItem:(NSDictionary *) item;
		- (void) completeItem: (NSDictionary *) item;

3. **QSTodoService.m**에서 새 메서드를 구현하는 다음 코드를 추가합니다.

        // Allows retrieval of items by id
		- (NSUInteger) getItemIndex:(NSDictionary *)item
		{
		    NSInteger itemId = [[item objectForKey: @"id"] integerValue];

		    return [items indexOfObjectPassingTest:^BOOL(id currItem, NSUInteger idx, BOOL *stop)
                 {
                     return ([[currItem objectForKey: @"id"] integerValue] == itemId);
                 }];
		}

        // To be called when items are added by other users
        -(NSUInteger) itemAdded:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item not already in list
		    if(index == NSNotFound)
		    {
        		NSUInteger newIndex = [items count];
		        [(NSMutableArray *)items insertObject:item atIndex:newIndex];
		        return newIndex;
		    }
		    else
        		return -1;
		}

        // To be called when items are completed by other users
		- (NSUInteger) itemCompleted:(NSDictionary *)item
		{
		    NSUInteger index = [self getItemIndex:item];

		    // Only complete action if item exists in items list
		    if(index != NSNotFound)
		    {
		        NSMutableArray *mutableItems = (NSMutableArray *) items;
		        [mutableItems removeObjectAtIndex:index];
		    }
		    return index;
		}

	이제 QSTodoService를 사용하여 항목을 **id**로 찾은 다음 원격 서비스에 명시적 요청을 보내지 않고 로컬에서 항목을 추가 및 완료할 수 있습니다.

4. 기존 **addItem** 및 **completeItem** 메서드를 다음 코드로 바꿉니다.

		-(void) addItem:(NSDictionary *)item
		{
		    // Insert the item into the TodoItem table and add to the items array on completion
		    [self.table insert:item completion:^(NSDictionary *result, NSError *error) {
        		[self logErrorIfNotNil:error];
		    }];
		}

		-(void) completeItem:(NSDictionary *)item
		{
		    // Set the item to be complete (we need a mutable copy)
		    NSMutableDictionary *mutable = [item mutableCopy];
		    [mutable setObject:@(YES) forKey:@"complete"];

		    // Update the item in the TodoItem table and remove from the items array on completion
		    [self.table update:mutable completion:^(NSDictionary *item, NSError *error) {
		        [self logErrorIfNotNil:error];
		    }];
		}


	이제 데이터 테이블이 업데이트될 때가 아니라 Pusher로부터 이벤트가 수신될 때 UI 업데이트와 함께 항목이 추가 및 완료됩니다.

5. **QSTodoListViewController.h** 파일에 다음 import 문을 추가합니다.

		#import "PTPusherDelegate.h"
		#import "PTPusher.h"
		#import "PTPusherEvent.h"
		#import "PTPusherChannel.h"

6. **PTPusherDelegate**를 추가하는 인터페이스 선언을 다음과 같이 수정합니다.

		@interface QSTodoListViewController : UITableViewController<UITextFieldDelegate, PTPusherDelegate>

7. 다음 새 속성을 추가합니다.

		@property (nonatomic, strong) PTPusher *pusher;

8. 새 메서드를 선언하는 다음 코드를 추가합니다.

		// Sets up the Pusher client
		- (void) setupPusher;

9. **QSTodoListViewController.m**에서 새 속성을 구현하는 다음 줄을 다른 **@synthesise** 줄 아래에 추가합니다.

		@synthesize pusher = _pusher;

10. 이제 새 메서드를 구현하는 다음 코드를 추가합니다.

		// Sets up the Pusher client
		- (void) setupPusher {

			// Create a Pusher client, using your Pusher app key as the credential
		    // TODO: Move Pusher app key to configuration file
		    self.pusher = [PTPusher pusherWithKey:@"**your_app_key**" delegate:self encrypted:NO];
		    self.pusher.reconnectAutomatically = YES;

    		// Subscribe to the 'todo-updates' channel
		    PTPusherChannel *todoChannel = [self.pusher subscribeToChannelNamed:@"todo-updates"];

    		// Bind to the 'todo-added' event
		    [todoChannel bindToEventNamed:@"todo-added" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Add item to the todo list
        		NSUInteger index = [self.todoService itemAdded:channelEvent.data];

        		// If the item was not already in the list, add the item to the UI
		        if(index != -1)
        		{
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView insertRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];

    		// Bind to the 'todo-completed' event
		    [todoChannel bindToEventNamed:@"todo-completed" handleWithBlock:^(PTPusherEvent *channelEvent) {

		    	// Update the item to be completed
		        NSUInteger index = [self.todoService itemCompleted:channelEvent.data];

        		// As long as the item did exit in the list, update the UI
		        if(index != NSNotFound)
		        {
		            NSIndexPath *indexPath = [NSIndexPath indexPathForRow:index inSection:0];
        		    [self.tableView deleteRowsAtIndexPaths:@[ indexPath ]
                                  withRowAnimation:UITableViewRowAnimationTop];
		        }
		    }];
		}

11. `**your_app_key**` 자리 표시자를 이전에 연결 정보 대화 상자에서 복사한 app_key 값으로 바꿉니다.

12. **onAdd** 메서드를 다음 코드로 바꿉니다.

		- (IBAction)onAdd:(id)sender
		{
		    if (itemText.text.length  == 0) {
        		return;
		    }

		    NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };
		    [self.todoService addItem:item];

		    itemText.text = @"";
		}

13. **QSTodoListViewController.m** 파일에서 (void)viewDidLoad 메서드를 찾은 후 처음 몇 줄이 다음과 같도록 **setupPusher** 메서드 호출을 추가합니다.

		- (void)viewDidLoad
		{
		    [super viewDidLoad];
		    [self setupPusher];

14. **tableView:commitEditingStyle:forRowAtIndexPath** 메서드의 끝에서 **completeItem** 호출을 다음 코드로 바꿉니다.

		// Ask the todoService to set the item's complete value to YES
	    [self.todoService completeItem:item];

이제 앱이 Pusher로부터 이벤트를 받고 로컬 할 일 목록을 적절하게 업데이트할 수 있습니다.



##<a name="install-scripts"></a>서버 스크립트 설치



이제 남은 작업은 서버 스크립트 설정뿐입니다. TodoList 테이블에 항목이 삽입되거나 업데이트되는 경우에 대한 스크립트를 삽입합니다.



1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다.


2. 관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

	![][1]



3. **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.


	![][2]



	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.


4. insert 함수를 다음 코드로 바꿉니다.


		var Pusher = require('pusher');

		function insert(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been inserted, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemCreatedEvent(item);
			    }
			});

			function publishItemCreatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-added', item );
			}
		}



5. 위 스크립트의 자리 표시자를 이전에 연결 정보 대화 상자에서 복사한 값으로 바꿉니다.

	- **`**your_app_id**`**: the app&#95;id value
	- **`**your_app_key**`**: the app&#95;key value
	- **`**your_app_key_secret**`**: the app&#95;key&#95;secret


6. **저장** 단추를 클릭합니다. 이제 **TodoItem** 테이블에 새 항목이 삽입될 때마다 Pusher에 이벤트를 게시하도록 스크립트를 구성했습니다.


7. **작업** 드롭다운에서 **업데이트**를 선택합니다.


8. update 함수를 다음 코드로 바꿉니다.

		var Pusher = require('pusher');

		function update(item, user, request) {

			request.execute({
	    		success: function() {
			        // After the record has been updated, trigger immediately to the client
	    		    request.respond();

			        // Publish event for all other active clients
			        publishItemUpdatedEvent(item);
			    }
			});

			function publishItemUpdatedEvent(item) {

				// Ideally these settings would be taken from config
			    var pusher = new Pusher({
				  appId: '**your_app_id**',
				  key: '**your_app_key**',
				  secret: '**your_app_secret**'
				});

				// Publish event on Pusher channel
			    pusher.trigger( 'todo-updates', 'todo-completed', item );

			}
		}



9. 이 스크립트에 대해 5단계를 반복하여 자리 표시자를 바꿉니다.


10. **저장** 단추를 클릭합니다. 이제 새 항목이 업데이트될 때마다 Pusher에 이벤트를 게시하도록 스크립트를 구성했습니다.



##<a name="test-app"></a>앱 테스트



앱을 테스트하려면 두 개의 인스턴스를 실행해야 합니다. iOS 장치와 iOS 시뮬레이터에서 각각 하나의 인스턴스를 실행할 수 있습니다.

1. iOS 장치를 연결하고 **실행** 단추(또는 Command+R 키)를 눌러 장치에서 앱을 시작한 후 디버깅을 중지합니다.

	이제 장치에 앱이 설치되었습니다.

2. iOS 시뮬레이터에서 앱을 실행하고, 동시에 iOS 장치에서 앱을 시작합니다.

	이제 두 개의 앱 인스턴스가 실행되고 있습니다.

3. 앱 인스턴스 중 하나에서 새 할 일 항목을 추가합니다.

	추가된 항목이 다른 인스턴스에 표시되는지 확인합니다.

4. 앱 인스턴스 중 하나에서 할 일 항목을 선택하여 완료로 표시합니다.

	해당 항목이 다른 인스턴스에서 사라지는지 확인합니다.

축하합니다. 모든 클라이언트에서 실시간으로 동기화되도록 모바일 서비스 앱을 구성했습니다.

## <a name="nextsteps"> </a>다음 단계

지금까지 모바일 서비스와 함께 Pusher 서비스를 사용하는 것이 얼마나 쉬운지 살펴보았습니다. 이제 다음 링크를 따라 Pusher에 대해 자세히 알아보십시오.

-   Pusher API 설명서: <http://pusher.com/docs>
-   Pusher 자습서: <http://pusher.com/tutorials>

서버 스크립트 등록 및 사용에 대한 자세한 내용은 [모바일 서비스 서버 스크립트 참조](영문)를 참조하십시오.

<!-- Anchors. -->
[Pusher 계정 만들기]: #sign-up
[앱 업데이트]: #update-app
[서버 스크립트 설치]: #install-scripts
[앱 테스트]: #test-app

<!-- Images. -->
[1]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-portal-data-tables.png
[2]: ./media/mobile-services-ios-build-realtime-apps-pusher/mobile-insert-script-push2.png

[add-files-to-group]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-files-to-group.png
[add-build-phase]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-build-phase.png
[add-linker-flag]: ./media/mobile-services-ios-build-realtime-apps-pusher/pusher-ios-add-linker-flag.png

<!-- URLs. -->
[사용자에게 푸시 알림]: /develop/mobile/tutorials/push-notifications-to-users-ios
[모바일 서비스 시작하기]: /develop/mobile/tutorials/get-started
[libPusher]: http://go.microsoft.com/fwlink/p?LinkId=276999
[libPusherDownload]: http://go.microsoft.com/fwlink/p/?LinkId=276998


[Azure 관리 포털]: https://manage.windowsazure.com/

[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/p/?LinkId=262293
 

<!---HONumber=July15_HO1-->