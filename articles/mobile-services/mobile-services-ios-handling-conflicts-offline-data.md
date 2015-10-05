<properties
	pageTitle="모바일 서비스에서 오프라인 데이터를 사용하여 충돌 처리(iOS) | 모바일 개발자 센터"
	description="iOS 응용 프로그램에서 오프라인 데이터를 동기화할 때 Azure 모바일 서비스를 사용하여 충돌을 처리하는 방법에 대해 알아보기"
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
	ms.date="09/16/2015"
	ms.author="krisragh;donnam"/>


# 모바일 서비스에서 오프라인 데이터 동기화를 사용하여 충돌 처리

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

이 항목에서는 Azure 모바일 서비스의 오프라인 기능을 사용할 때 데이터를 동기화하고 충돌을 처리하는 방법을 보여 줍니다. 이 자습서는 [오프라인 데이터 시작] 자습서를 기반으로 합니다.

>[AZURE.NOTE]이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요.


## iOS 프로젝트 다운로드

이 자습서의 경우 [Github에서 업데이트된 Xcode 프로젝트](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS)를 다운로드합니다. [오프라인 데이터 시작] 자습서의 끝에 있는 Xcode 프로젝트를 시작 지점으로 사용한 후 항목 편집을 허용하도록 업데이트했습니다. 또한 다음 섹션에서 충돌 처리기를 추가할 수 있도록 지원 클래스 및 메서드를 추가했습니다.

이 자습서의 끝에서, 두 휴대폰에서 이 앱을 실행하고 두 휴대폰에서 동일한 항목을 로컬로 변경한 다음 서버에 변경 내용을 다시 푸시하는 경우 각 휴대폰의 사용자가 유지할 버전을 다음 중에서 선택할 수 있습니다. * 클라이언트 버전 유지(서버의 버전이 재정의됨), * 서버 버전 유지(클라이언트 로컬 테이블이 업데이트됨) 또는 * 두 버전을 모두 유지하지 않음(푸시가 취소되고 작업이 보류 중 상태로 유지됨).

이제 이 기능을 사용할 수 있도록 하는 충돌 처리기를 추가하겠습니다.

## <a name="add-conflict-handling"></a>할 일 목록 보기 컨트롤러에 충돌 처리기 추가

1. **QSTodoListViewController.m**에서 **viewDidLoad**를 편집합니다. **defaultService** 호출을 대신 **defaultServiceWithDelegate** 호출로 바꿉니다.

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. **QSTodoListViewController.h**에서 **&lt;MSSyncContextDelegate&gt;** 프로토콜을 구현하도록 **MSSyncContextDelegate**를 인터페이스 선언에 추가합니다.

        @interface QSTodoListViewController : UITableViewController<MSSyncContextDelegate, NSFetchedResultsControllerDelegate>

3. **QSTodoListViewController.m**의 맨 위에 다음 가져오기 문을 추가합니다.

        #import "QSUIAlertViewWithBlock.h"

4. 마지막으로 이 도우미 클래스를 사용하고 사용자에게 세 가지 방법 중 하나로 충돌을 조정하라는 메시지를 표시하도록 **QSTodoListViewController.m**에 다음의 두 작업을 추가합니다.

        - (void)tableOperation:(MSTableOperation *)operation onComplete:(MSSyncItemBlock)completion
        {
            [self doOperation:operation complete:completion];
        }

        -(void)doOperation:(MSTableOperation *)operation complete:(MSSyncItemBlock)completion
        {
            [operation executeWithCompletion:^(NSDictionary *item, NSError *error) {

                NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];

                if (error.code == MSErrorPreconditionFailed) {
                    QSUIAlertViewWithBlock *alert = [[QSUIAlertViewWithBlock alloc] initWithCallback:^(NSInteger buttonIndex) {
                        if (buttonIndex == 1) { // Client
                            NSMutableDictionary *adjustedItem = [operation.item mutableCopy];

                            [adjustedItem setValue:[serverItem objectForKey:MSSystemColumnVersion] forKey:MSSystemColumnVersion];
                            operation.item = adjustedItem;

                            [self doOperation:operation complete:completion];
                            return;

                        } else if (buttonIndex == 2) { // Server
                            NSDictionary *serverItem = [error.userInfo objectForKey:MSErrorServerItemKey];
                            completion(serverItem, nil);
                        } else { // Cancel
                            [operation cancelPush];
                            completion(nil, error);
                        }
                    }];

                    NSString *message = [NSString stringWithFormat:@"Client value: %@\nServer value: %@", operation.item[@"text"], serverItem[@"text"]];

                    [alert showAlertWithTitle:@"Server Conflict"
                                      message:message
                            cancelButtonTitle:@"Cancel"
                            otherButtonTitles:[NSArray arrayWithObjects:@"Use Client", @"Use Server", nil]];
                } else {
                    completion(item, error);
                }
            }];
        }

## <a name="test-app"></a>앱 테스트

이제 충돌이 발생하는 응용 프로그램을 테스트해 보겠습니다. 동시에 실행 중인 앱의 서로 다른 두 인스턴스에서 같은 항목을 편집하거나 해당 앱과 REST 클라이언트를 사용하여 편집합니다.

앱의 맨 위에서 끌어 앱 인스턴스에서 새로 고침 제스처를 수행합니다. 그러면 충돌을 조정하라는 메시지가 표시됩니다.

![][conflict-ui]

<!-- URLs. -->

[Update the App Project to Allow Editing]: #update-app
[Update Todo List View Controller]: #update-list-view
[Add Todo Item View Controller]: #add-view-controller
[Add Todo Item View Controller and Segue to Storyboard]: #add-segue
[Add Item Details to Todo Item View Controller]: #add-item-details
[Add Support for Saving Edits]: #saving-edits
[Conflict Handling Problem]: #conflict-handling-problem
[Update QSTodoService to Support Conflict Handling]: #service-add-conflict-handling
[Add UI Alert View Helper to Support Conflict Handling]: #add-alert-view
[Add Conflict Handler to Todo List View Controller]: #add-conflict-handling
[Test the App]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[Segmented Controls]: https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Getting Started Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[오프라인 데이터 시작]: mobile-services-ios-get-started-offline-data.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md

<!---HONumber=Sept15_HO4-->