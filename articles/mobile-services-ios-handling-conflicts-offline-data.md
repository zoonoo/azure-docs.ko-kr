<properties 
	pageTitle="모바일 서비스에서 오프라인 데이터를 사용하여 충돌 처리(iOS) | 모바일 개발자 센터" 
	description="iOS 응용 프로그램에서 오프라인 데이터를 동기화할 때 Azure 모바일 서비스를 사용하여 충돌을 처리하는 방법에 대해 알아봅니다." 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="01/26/2015" 
	ms.author="krisragh,donnam"/>


# 모바일 서비스에서 오프라인 데이터 동기화를 사용하여 충돌 처리

[WACOM.INCLUDE [mobile-services-selector-offline-conflicts](../includes/mobile-services-selector-offline-conflicts.md)]

이 항목에서는 Azure 모바일 서비스의 오프라인 기능을 사용할 때 데이터를 동기화하고 충돌을 처리하는 방법을 보여 줍니다. 이 자습서는 이전 자습서인 [오프라인 데이터 시작]의 단계 및 샘플 앱을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [오프라인 데이터 시작]을 완료해야 합니다.

>[AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서에서는 다음 기본 단계를 단계별로 안내합니다.

1. [편집을 허용하도록 앱 프로젝트 업데이트]
2. [할 일 목록 보기 컨트롤러 업데이트]
3. [할 일 항목 보기 컨트롤러 추가]
4. [스토리보드에 할 일 항목 보기 컨트롤러 및 Segue 추가]
5. [할 일 항목 보기 컨트롤러에 항목 세부 정보 추가]
6. [편집 내용 저장 지원 기능 추가]
7. [충돌 처리 문제]
8. [충돌 처리를 지원하도록 QSTodoService 업데이트]
9. [충돌 처리를 지원하도록 UI 경고 보기 도우미 추가]
10. [할 일 목록 보기 컨트롤러에 충돌 처리기 추가]
11. [앱 테스트]

## 오프라인 데이터 시작 자습서 완료

[오프라인 데이터 시작] 자습서의 지침에 따라 해당 프로젝트를 완료합니다. 해당 자습서에서 완료한 프로젝트를 사용하여 이 자습서를 시작합니다.

## <a name="update-app"></a>편집을 허용하도록 앱 프로젝트 업데이트

항목을 편집할 수 있도록 [오프라인 데이터 시작]에서 완료한 프로젝트를 업데이트해 보겠습니다. 현재 두 휴대폰에서 같은 앱을 실행하는 경우 같은 항목을 두 휴대폰에서 로컬로 변경한 다음 변경 내용을 서버로 다시 푸시하면 충돌이 발생하고 작업이 실패합니다.

SDK의 오프라인 동기화 기능을 사용하면 코드를 통해 이러한 충돌을 처리하고 충돌하는 항목에 대해 수행할 작업을 동적으로 결정할 수 있습니다. 퀵 스타트 프로젝트를 변경하여 이 기능을 사용해 볼 수 있습니다.

### <a name="update-list-view"></a>할 일 목록 보기 컨트롤러 업데이트

1. Xcode 프로젝트 탐색기에서 **MainStoryboard_iPhone.storyboard**를 선택하고 **할 일 목록 보기 컨트롤러**를 선택합니다. 테이블 보기 셀을 선택하고 해당 액세서리 모드를 **표시 지시기**로 설정합니다. 표시 지시기는 사용자가 연결된 테이블 보기 컨트롤러를 누르면 새 보기가 표시됨을 나타내며, 이벤트를 생성하지는 않습니다.

      ![][update-todo-list-view-controller-2]

2. **TodoListViewController.m**에서 다음 작업과 해당 내용은 필요하지 않으므로 함께 제거합니다.

        -(NSString *)tableView:(UITableView *)tableView titleForDeleteConfirmationButtonForRowAtIndexPath:(NSIndexPath *)indexPath

        -(UITableViewCellEditingStyle)tableView:(UITableView *)tableView editingStyleForRowAtIndexPath:(NSIndexPath *)indexPath

        -(void)tableView:(UITableView *)tableView commitEditingStyle:(UITableViewCellEditingStyle)editingStyle
         forRowAtIndexPath:(NSIndexPath *)indexPath

### <a name="add-view-controller"></a>할 일 항목 보기 컨트롤러 추가

1. **UIViewController**에서 파생된 **QSItemViewController**라는 새 Cocoa Touch 클래스를 만듭니다.

2. **QSItemViewController.h**에 다음 형식 정의를 추가합니다.

        typedef void (^ItemEditCompletionBlock) (NSDictionary *editedItem);

3. **QSItemViewController.h**에서 수정할 항목을 포함할 속성을 추가하고 세부 정보 뷰에서 뒤로 단추를 누른 후에 호출되는 콜백에 대한 속성을 추가합니다.

        @property (nonatomic, weak) NSMutableDictionary *item;
        @property (nonatomic, strong) ItemEditCompletionBlock editCompleteBlock;

4. **QSItemViewController.m**에서 편집할 할 일 항목의 두 필드에 대해 개인 속성 두 개, 즉 완료 상태와 할 일 항목 자체의 텍스트를 추가합니다.

        @interface QSItemViewController ()

        @property (nonatomic, strong) IBOutlet UITextField *itemText;
        @property (nonatomic, strong) IBOutlet UISegmentedControl *itemComplete;

        @end

5. **QSItemViewController.m**에서 **viewDidLoad**의 스텁 구현을 다음 코드로 업데이트합니다.

        - (void)viewDidLoad
        {
            [super viewDidLoad];

            UINavigationItem *nav = [self navigationItem];
            [nav setTitle:@"Todo Item"];

            NSDictionary *theItem = [self item];
            [self.itemText setText:[theItem objectForKey:@"text"]];

            BOOL isComplete = [[theItem objectForKey:@"complete"] boolValue];
            [self.itemComplete setSelectedSegmentIndex:(isComplete ? 0 : 1)];

            [self.itemComplete addTarget:self
                                  action:@selector(completedValueChanged:)
                        forControlEvents:UIControlEventValueChanged];
        }

6. **QSItemViewController.m**에서 편집된 컨트롤 이벤트를 처리하기 위한 메서드 4개를 더 추가합니다.

        - (BOOL)textFieldShouldEndEditing:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }

        - (BOOL)textFieldShouldReturn:(UITextField *)textField {
            [textField resignFirstResponder];
            return YES;
        }


        - (void)completedValueChanged:(id)sender {
            [[self view] endEditing:YES];
        }

7. **QSItemViewController**에서 사용자가 탐색 모음에서 **뒤로** 단추를 누를 때 호출되는 다음 메서드도 추가합니다. 다른 이벤트의 경우에도 이 메서드가 호출될 수 있으므로 먼저 부모 뷰를 확인합니다. 항목이 변경되면 **self.item**이 수정되고 **editCompleteBlock** 콜백이 호출됩니다.

        - (void)didMoveToParentViewController:(UIViewController *)parent
        {
            if (![parent isEqual:self.parentViewController]) {
                NSNumber *completeValue = [NSNumber numberWithBool:self.itemComplete.selectedSegmentIndex == 0];
                
                Boolean changed =
                    [self.item valueForKey:@"text"] != [self.itemText text] ||
                    [self.item valueForKey:@"complete"] != completeValue;
                
                if (changed) {
                    [self.item setValue:[self.itemText text] forKey:@"text"];
                    [self.item setValue:completeValue forKey:@"complete"];
                    
                    self.editCompleteBlock(self.item);
                }
            }
        }

### <a name="add-segue"></a>스토리보드에 할 일 항목 보기 컨트롤러 및 Segue 추가

1. 프로젝트 탐색기를 사용하여 **MainStoryboard_iPhone.storyboard** 파일로 돌아옵니다.

2. 스토리보드에서 기존 **할 일 목록 보기 컨트롤러**의 오른쪽에 할 일 항목에 대한 새 보기 컨트롤러를 추가합니다. 이 새 보기 컨트롤러의 사용자 지정 클래스를 **QSItemViewController**로 설정합니다. 자세한 내용은 [스토리보드에 장면 추가](영문)를 참조하세요.

3. **할 일 목록 보기 컨트롤러**에서 **할 일 항목 보기 컨트롤러**로 **표시** Segue를 추가합니다. 그런 다음 특성 검사기에서 segue 식별자를 **detailSegue**로 설정합니다. 

    원래 보기 컨트롤러의 셀이나 단추에서 이 Segue를 만들지 말고, Ctrl 키를 누른 상태로 스토리보드 인터페이스의 **할 일 목록 보기 컨트롤러** 위의 보기 컨트롤러 아이콘에서 **할 일 항목 보기 컨트롤러**로 끕니다.

    ![][todo-list-view-controller-add-segue]

    실수로 셀에서 Segue를 만드는 경우 앱을 실행할 때 Segue가 두 번 트리거되어 다음 오류가 발생합니다.

        Nested push animation can result in corrupted navigation bar

    segue에 대한 자세한 내용은 [스토리보드에서 장면 간에 Segue 추가]를 참조하세요. 

4. 완료 상태 표시를 위한 분할된 컨트롤과 항목 텍스트용 텍스트 필드를 새 **할 일 항목 보기 컨트롤러**에 레이블과 함께 추가합니다. 분할된 컨트롤에서 **세그먼트 0**의 제목을 **예**로 설정하고 **세그먼트 1**의 제목을 **아니요**로 설정합니다. 이러한 새 필드를 코드의 출선에 연결합니다. 자세한 내용은 [사용자 인터페이스 빌드](영문) 및 [분할된 컨트롤](영문)을 참조하세요.

      ![][add-todo-item-view-controller-3]

5. **QSItemViewController.m**에 이미 추가한 해당 출선에 이러한 새 필드를 연결합니다. 항목 텍스트 필드는 **itemText** 출선에 연결하고 완료 상태 분할된 컨트롤은 **itemComplete** 출선에 연결합니다. 자세한 내용은 [출선 연결 만들기](영문)를 참조하세요.

6. 텍스트 필드의 대리자를 보기 컨트롤러로 설정합니다. Ctrl 키를 누른 상태로 텍스트 필드에서 스토리보드 인터페이스의 **할 일 항목 보기 컨트롤러** 아래 보기 컨트롤러 아이콘으로 끈 다음 대리자 출선을 선택합니다. 그러면 스토리보드에서 해당 텍스트 필드의 대리자가 이 보기 컨트롤러로 지정됩니다.

7. 지금까지 적용한 모든 변경 내용이 앱에서 작동하는지 확인합니다. 이렇게 하려면 시뮬레이터에서 앱을 실행하고 할 일 목록에 항목을 추가한 다음 클릭해 봅니다. 그러면 현재는 비어 있는 항목 보기 컨트롤러가 표시됩니다.

      ![][add-todo-item-view-controller-4]          ![][add-todo-item-view-controller-5]

### <a name="add-item-details"></a>할 일 항목 보기 컨트롤러에 항목 세부 정보 추가

1. 이 단계에서는 **QSTodoListViewController.m**의 **QSTodoItemViewController**를 참조합니다. **QSTodoListViewController.m**에서 **QSItemViewController.h**를 가져오는 줄을 추가해 보겠습니다.

        #import "QSItemViewController.h"

2. 편집 중인 항목을 지정할 새 속성 두 개를 **QSTodoListViewController.m**의 **QSTodoListViewController** 인터페이스에 추가합니다.

        @property (strong, nonatomic)   NSDictionary *editingItem;

3. **QSTodoListViewController.m**에서 편집 중인 항목을 저장한 다음 Segue를 호출해 세부 정보 보기를 표시하도록 **tableView:didSelectRowAtIndexPath:**를 구현합니다.

        - (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath {
            NSManagedObject *item = [self.fetchedResultsController objectAtIndexPath:indexPath];
            self.editingItem = [MSCoreDataStore tableItemFromManagedObject:item]; // map from managed object to dictionary
            
            [self performSegueWithIdentifier:@"detailSegue" sender:self];
        }

4. **QSTodoListViewController.m**에서 **prepareForSegue:sender:**를 구현하여 항목을 **할 일 항목 보기 컨트롤러**에 제공하고 사용자가 세부 정보 보기를 끝낼 때 콜백을 지정합니다.

        - (void)prepareForSegue:(UIStoryboardSegue *)segue sender:(id)sender {
            if ([[segue identifier] isEqualToString:@"detailSegue"]) {
                QSItemViewController *ivc = (QSItemViewController *) [segue destinationViewController];
                ivc.item = [self.editingItem mutableCopy];
                
                ivc.editCompleteBlock = ^(NSDictionary *editedValue) {
                    [self.todoService updateItem:editedValue completion:^(NSUInteger index) {
                        self.editingItem = nil;
                    }];
                };
            }
        }

5. 지금까지 적용한 모든 변경 내용이 앱에서 작동하는지 확인합니다. 이렇게 하려면 시뮬레이터에서 앱을 실행하고 할 일 목록에 항목을 추가한 다음 클릭해 봅니다. 그러면 항목 보기 컨트롤러가 더 이상 비어 있지 않으며 할 일 항목의 세부 정보가 표시됨을 확인할 수 있습니다.

      ![][add-todo-item-view-controller-6]

### <a name="saving-edits"></a>편집 내용 저장 지원 기능 추가

1. 탐색 보기에서 "뒤로" 단추를 클릭하면 편집 내용이 손실됩니다. 세부 정보 보기로 전송한 데이터가 마스터 보기로는 다시 전송되지 않았기 때문입니다. 항목 복사본으로 포인터를 이미 전달했으므로 해당 포인터를 사용하여 항목에 대해 수행한 업데이트 목록을 검색하고 서버에서 항목을 업데이트할 수 있습니다. 이 작업을 시작하려면 먼저 **completeItem** 작업을 시작하고 새 **updateItem** 작업을 추가하여 **QSTodoService.m**에서 **QSTodoService**의 서버 래퍼 클래스를 업데이트합니다. 이렇게 하는 이유는 **completeItem**의 경우 항목을 완료된 것으로만 표시하지만 **updateItem**은 항목을 업데이트하기 때문입니다.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion
        {
            // Set the item to be complete (we need a mutable copy)
            NSMutableDictionary *mutable = [item mutableCopy];
            
            // Update the item in the TodoItem table and remove from the items array when we mark an item as complete
            [self.syncTable update:mutable completion:^(NSError *error) {
                [self logErrorIfNotNil:error];
                
                if (completion != nil) {
                    dispatch_async(dispatch_get_main_queue(), completion);
                }
            }];
        }

2. **QSTodoService.h**에서 **completeItem**의 선언을 제거하고 **updateItem**에 대해 다음 선언을 추가합니다.

        - (void)updateItem:(NSDictionary *)item completion:(QSCompletionBlock)completion;

3. 이제 앱을 테스트해 보겠습니다. 지금까지 적용한 모든 변경 내용이 앱에서 작동하는지 확인합니다. 이렇게 하려면 시뮬레이터에서 앱을 실행하고 할 일 목록에 항목을 추가한 다음 클릭해 봅니다. 그런 후에는 항목을 편집한 다음 돌아와서 앱의 마스터 보기에서 항목 설명이 업데이트되었는지 확인합니다. 아래로 끌기 제스처를 사용해 앱을 새로 고친 다음 편집 내용이 원격 서비스에 반영되었는지 확인합니다.

### <a name="conflict-handling-problem"></a>충돌 처리 문제

1. 서로 다른 두 클라이언트가 같은 데이터 부분을 동시에 수정하려는 경우 발생하는 현상에 대해 알아보겠습니다. 아래 예제에는 "Mobile Services is Cool!" 항목이 있습니다. 이제 한 장치에서는 이 항목을 "I love Mobile Services!"로 변경하고 또 다른 장치에서는 "I love Azure!"로 변경하겠습니다.

      ![][conflict-handling-problem-1]

2. 두 위치(두 iOS 장치 또는 시뮬레이터와 iOS 장치)에서 앱을 시작합니다. 테스트를 수행할 실제 장치가 없으면 시뮬레이터에서 인스턴스 하나를 시작하고 REST 클라이언트를 사용해 PATCH 요청을 모바일 서비스로 보냅니다. PATCH 요청의 URL은 모바일 서비스, 할 일 항목 테이블의 이름 및 편집 중인 할 일 항목 테이블의 ID를 반영하며 x-zumo-application 헤더는 응용 프로그램 키입니다.

        PATCH https://donnam-tutorials.azure-mobile.net/tables/todoitem/D265929E-B17B-42D1-8FAB-D0ADF26486FA?__systemproperties=__version
        Content-Type: application/json
        x-zumo-application: xuAdWVDcLuCNfkTvOfaqzCCSBVHqoy96

        {
            "id": "CBBF4464-E08A-47C9-B6FB-6DCB30ACCE7E",
            "text": "I love Azure!"
        }

3. 이제 두 앱 인스턴스에서 항목을 새로 고칩니다. 그러면 Xcode의 출력 로그에 오류가 인쇄됩니다.

        TodoList[1575:4a2f] ERROR Error Domain=com.Microsoft.WindowsAzureMobileServices.ErrorDomain Code=-1170 "Not all operations completed successfully" UserInfo=0x8dd6310 {com.Microsoft.WindowsAzureMobileServices.ErrorPushResultKey=(
            "The server's version did not match the passed version"
        ), NSLocalizedDescription=Not all operations completed successfully}

  완료 블록의 **pullWithQuery:completion:** 호출에서 오류 매개 변수가 nil이 아니기 때문에 **NSLog**를 통해 출력에 오류가 인쇄되는 것입니다.

### <a name="service-add-conflict-handling"></a>충돌 처리를 지원하도록 QSTodoService 업데이트

1. 사용자가 클라이언트에서 충돌을 해결하여 충돌 처리 방법을 결정하도록 지정해 보겠습니다. 이렇게 하려면 **MSSyncContextDelegate** 프로토콜을 구현합니다. **QSTodoService.h** 및 **QSTodoService.m**에서 동기화 컨텍스트 대리자를 매개 변수로 받도록 **(QSTodoService *)defaultService;** 팩터리 메서드 선언을 아래 문으로 변경합니다.

        + (QSTodoService *)defaultServiceWithDelegate:(id)delegate;

2. 마찬가지로 **QSTodoService.m**에서 동기화 컨텍스트 대리자를 매개 변수로 받도록 **init** 줄을 아래에 나와 있는 것처럼 변경합니다.

€

3. **QSTodoService.m**에서 **defaultServiceWithDelegate**의 **init** 호출을 **initWithDelegate**로 변경합니다.

        service = [[QSTodoService alloc] initWithDelegate:delegate];

4. **QSTodoService.m**으로 돌아와서 대리자에 대해 **nil**이 아닌 **syncDelegate**를 전달하도록 **self.client.syncContext**의 초기화를 변경합니다.

        self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:syncDelegate dataSource:store callback:nil];

### <a name="add-alert-view"></a>충돌 처리를 지원하도록 UI 경고 보기 도우미 추가

1. 충돌 발생 시 사용자가 유지할 버전을 선택할 수 있도록 지정해 보겠습니다.
  * 클라이언트 버전 유지(서버의 버전이 재정의됨)
  * 서버 버전 유지(클라이언트 로컬 테이블이 업데이트됨) 또는 
  * 두 버전을 모두 유지하지 않음(푸시가 취소되고 작업이 보류 중 상태로 유지됨)

  메시지를 표시하는 동안 다른 업데이트도 수행될 수 있으므로 서버에서 실패 응답을 더 이상 반환하지 않을 때까지는 옵션이 계속 표시되도록 지정합니다. 이를 위해 코드에서 경고 보기를 표시하고 해당 경고 보기가 표시될 때 호출되는 대리자를 가져오는 도우미 클래스를 사용하겠습니다. 먼저 **QSUIAlertViewWithBlock** 도우미 클래스를 정의합니다.

2. Xcode를 사용하여 새 클래스 **QSUIAlertViewWithBlock**을 추가하고 **QSUIAlertViewWithBlock.h**를 다음 내용으로 덮어씁니다.

        #import <Foundation/Foundation.h>

        typedef void (^QSUIAlertViewBlock) (NSInteger index);

        @interface QSUIAlertViewWithBlock : NSObject <UIAlertViewDelegate>

        - (id) initWithCallback:(QSUIAlertViewBlock)callback;
        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles;

        @end

3. 그런 후에 다음 파일로 **QSUIAlertViewWithBlock.m**을 덮어씁니다.

        #import "QSUIAlertViewWithBlock.h"
        #import <objc/runtime.h>

        @interface QSUIAlertViewWithBlock()

        @property (nonatomic, copy) QSUIAlertViewBlock callback;

        @end

        @implementation QSUIAlertViewWithBlock

        static const char *key;

        @synthesize callback = _callback;

        - (id) initWithCallback:(QSUIAlertViewBlock)callback
        {
            self = [super init];
            if (self) {
                _callback = [callback copy];
            }
            return self;
        }

        - (void) showAlertWithTitle:(NSString *)title message:(NSString *)message cancelButtonTitle:(NSString *)cancelButtonTitle otherButtonTitles:(NSArray *)otherButtonTitles {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title
                                                            message:message
                                                           delegate:self
                                                  cancelButtonTitle:cancelButtonTitle
                                                  otherButtonTitles:nil];

            if (otherButtonTitles) {
                for (NSString *buttonTitle in otherButtonTitles) {
                    [alert addButtonWithTitle:buttonTitle];
                }
            }

            [alert show];

            objc_setAssociatedObject(alert, &key, self, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
        }

        - (void) alertView:(UIAlertView *)alertView didDismissWithButtonIndex:(NSInteger)buttonIndex
        {
            if (self.callback) {
                self.callback(buttonIndex);
            }
        }

        @end

### <a name="add-conflict-handling"></a>할 일 목록 보기 컨트롤러에 충돌 처리기 추가

1. **QSTodoListViewController.m**에서 **viewDidLoad**를 편집합니다. **defaultService** 호출을 대신 **defaultServiceWithDelegate** 호출로 바꿉니다.

        self.todoService = [QSTodoService defaultServiceWithDelegate:self];

2. **QSTodoListViewController.h**에서 **MSSyncContextDelegate** 프로토콜을 구현하도록 **&lt;MSSyncContextDelegate&gt;**를 인터페이스 선언에 추가합니다.

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

### <a name="test-app"></a>앱 테스트

이제 충돌이 발생하는 응용 프로그램을 테스트해 보겠습니다. 동시에 실행 중인 앱의 서로 다른 두 인스턴스에서 같은 항목을 편집하거나 해당 앱과 REST 클라이언트를 사용하여 편집합니다. 

앱의 맨 위에서 끌어 앱 인스턴스에서 새로 고침 제스처를 수행합니다. 그러면 충돌을 조정하라는 메시지가 표시됩니다.

![][conflict-ui]


### 요약

이 문서에서는 [오프라인 데이터 시작]에서 완료한 프로젝트가 충돌을 검색하도록 설정하기 위해 먼저 할 일 항목을 편집 및 업데이트하는 기능을 추가했습니다.

이를 위해 새 항목 세부 정보 보기 컨트롤러를 추가하고, 주 보기 컨트롤러와 세부 정보 보기 컨트롤러를 연결했으며, 마지막으로 편집 내용을 저장하고 클라우드로 푸시하는 기능을 추가했습니다.

그런 후에 충돌 발생 시 나타나는 현상을 확인했습니다. 그리고 **MSSyncContextDelegate** 프로토콜을 구현하여 충돌 처리기 지원을 추가했습니다. 또한 서버 인터페이스 클래스 **QSTodoService**, **QSTodoListViewController** 및 지원 클래스를 통해 동기화 컨텍스트 대리자도 지원하도록 설정했습니다.

그리고 사용자에게 경고를 표시하기 위한 **QSUIAlertViewWithBlock** 도우미 클래스를 추가했으며, 마지막으로 세 가지 방법 중 하나로 충돌을 조정하라는 메시지를 사용자에게 표시하기 위해 **QSTodoListViewController**에 코드를 추가했습니다.

<!-- URLs. -->

[편집을 허용하도록 앱 프로젝트 업데이트]: #update-app
[할 일 목록 보기 컨트롤러 업데이트]: #update-list-view
[할 일 항목 보기 컨트롤러 추가]: #add-view-controller
[스토리보드에 할 일 항목 보기 컨트롤러 및 Segue 추가]: #add-segue
[할 일 항목 보기 컨트롤러에 항목 세부 정보 추가]: #add-item-details
[편집 내용 저장 지원 기능 추가]: #saving-edits
[충돌 처리 문제]: #conflict-handling-problem
[충돌 처리를 지원하도록 QSTodoService 업데이트]: #service-add-conflict-handling
[충돌 처리를 지원하도록 UI 경고 보기 도우미 추가]: #add-alert-view
[할 일 목록 보기 컨트롤러에 충돌 처리기 추가]: #add-conflict-handling
[앱 테스트]: #test-app


[add-todo-item-view-controller-3]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-3.png
[add-todo-item-view-controller-4]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-4.png
[add-todo-item-view-controller-5]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-5.png
[add-todo-item-view-controller-6]: ./media/mobile-services-ios-handling-conflicts-offline-data/add-todo-item-view-controller-6.png
[todo-list-view-controller-add-segue]: ./media/mobile-services-ios-handling-conflicts-offline-data/todo-list-view-controller-add-segue.png
[update-todo-list-view-controller-2]: ./media/mobile-services-ios-handling-conflicts-offline-data/update-todo-list-view-controller-2.png
[conflict-handling-problem-1]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-handling-problem-1.png
[conflict-ui]: ./media/mobile-services-ios-handling-conflicts-offline-data/conflict-ui.png


[분할된 컨트롤](영문): https://developer.apple.com/library/ios/documentation/UserExperience/Conceptual/UIKitUICatalog/UISegmentedControl.html
[Core Data Model Editor 도움말](영문): https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[출선 연결 만들기](영문): https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[사용자 인터페이스 빌드](영문): https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[스토리보드에서 장면 간에 Segue 추가](영문): https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[스토리보드에 장면 추가](영문): https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html
[Core Data](영문): https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[미리 보기 SDK 다운로드](영문): http://aka.ms/Gc6fex
[iOS용 모바일 서비스 클라이언트 라이브러리를 사용하는 방법]: /ko-kr/documentation/articles/mobile-services-ios-how-to-use-client-library/
[오프라인 iOS 샘플 시작](영문): https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[오프라인 데이터 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-offline-data/
[모바일 서비스 시작](영문): /ko-kr/documentation/articles/mobile-services-ios-get-started/
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-data/


<!--HONumber=42-->
