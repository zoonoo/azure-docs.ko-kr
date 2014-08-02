<properties linkid="develop-net-tutorials-push-notifications-to-users-ios" urlDisplayName="Push Notifications to Users (iOS)" pageTitle="Push notifications to users (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your iOS app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="" solutions="" manager="" editor="" />

모바일 서비스를 사용하여 사용자에게 푸시 알림
=============================================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

이 항목에서는 APNS(Apple Push Notification Service) 토큰을 저장하는 새 테이블을 추가하여 [이전의 푸시 알림 자습서](/en-us/develop/mobile/tutorials/get-started-with-push-ios)를 확장했습니다. 이 토큰을 사용하여 iPhone 또는 iPad 앱 사용자에게 푸시 알림을 보낼 수 있습니다.

이 자습서에서는 앱에서 푸시 알림을 업데이트하는 다음 단계를 단계별로 안내합니다.

1.  [Device 테이블 만들기](#create-table)
2.  [앱 업데이트](#update-app)
3.  [서버 스크립트 업데이트](#update-scripts)
4.  [푸시 알림 동작 확인](#test-app)

이 자습서는 모바일 서비스 빠른 시작을 기반으로 하며 이전 자습서인 [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-ios)에 내용을 추가했습니다. 이 자습서를 시작하기 전에 먼저 [푸시 알림 시작](/en-us/develop/mobile/tutorials/get-started-with-push-ios)을 완료해야 합니다.

## <a name="create-table"></a><h2><span class="short-header">테이블 만들기새</span>Devices 테이블 만들기</h2>

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-services-selection.png)

2.  **데이터** 탭을 클릭한 후 **만들기**를 클릭합니다.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-table.png)

      **새 테이블 만들기** 대화 상자가 표시됩니다.

3.  모든 권한에 기본 **응용 프로그램 키가 있는 모든 사용자** 설정을 유지하고 **테이블 이름**에 *Devices*를 입력한 후 확인 단추를 클릭합니다.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-create-devices-table.png)

    항목 데이터와 별개로 푸시 알림을 보내기 위해 사용되는 장치 토큰을 저장하는 **Devices** 테이블이 생성됩니다.

다음으로, **TodoItem** 테이블 대신 이 새 테이블에 데이터를 저장하도록 푸시 알림 앱을 수정합니다.

앱 업데이트
-----------

1.  Xcode에서 QSTodoService.h 파일을 열고 다음 메서드 선언을 추가합니다.

         // Declare method to register device token for other users
         - (void)registerDeviceToken:(NSString *)deviceToken;

     이를 통해 App Delegate는 모바일 서비스를 사용하여 deviceToken을 등록할 수 있게 됩니다.

2.  QSTodoService.m에서 다음 인스턴스 메서드를 추가합니다.

         // Instance method to register deviceToken in Devices table.
         // Called in AppDelegate.m when APNS registration succeeds.
         - (void)registerDeviceToken:(NSString *)deviceToken
         {
             MSTable *devicesTable = [self.client tableWithName:@"Devices"]; 
             NSDictionary *device = @{ @"deviceToken" : deviceToken };

             // Insert the item into the devices table and add to the items array on completion
             [devicesTable insert:device completion:^(NSDictionary *result, NSError *error) {
                 if (error) {
                     NSLog(@"ERROR %@", error);
                 }
             }];
         }

     이제 다른 호출자가 모바일 서비스를 사용하여 장치 토큰을 등록할 수 있습니다.

3.  QSAppDelegate.m 파일에서 다음 import 문을 추가합니다.

         #import "QSTodoService.h"

    이 코드는 AppDelegate에서 TodoService 구현을 인식하도록 만듭니다.

4.  QSAppDelegate.m에서 **didRegisterForRemoteNotificationsWithDeviceToken** 메서드를 다음 코드로 바꿉니다.

         // We have registered, so now store the device token (as a string) on the AppDelegate instance
         // taking care to remove the angle brackets first.
         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
         (NSData *)deviceToken {

            // Register the APNS deviceToken with the Mobile Service Devices table.
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            NSString *token = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
                
            QSTodoService *instance = [QSTodoService defaultService];
            [instance registerDeviceToken:token];
         }

5.  QSTodoListViewController.m에서 **(IBAction)onAdd** 메서드를 찾고 다음 코드를 *제거*합니다.

         // Get a reference to the AppDelegate to easily retrieve the deviceToken.
         QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

         NSDictionary *item = @{
             @"text" : itemText.text,
             @"complete" : @(NO),
             // add the device token property to our todo item payload
             @"deviceToken" : delegate.deviceToken
         };
         
    이것을 다음 코드로 교체합니다.

         // We removed the delegate; this application no longer passes the deviceToken here.
         // Remove the device token from the payload
         NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

이제 푸시 알림을 다시 장치로 보내는 데 사용되는 장치 토큰을 저장하기 위해 새 Devices 테이블을 사용하도록 앱이 업데이트되었습니다.

서버 스크립트 업데이트
----------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **장치** 테이블을 클릭합니다.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-portal-data-tables-devices.png)

2.  **장치**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-devices.png)

    **장치** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
            var devicesTable = tables.getTable('Devices');
            devicesTable.where({
                token: item.token
            }).read({
                success: insertTokenIfNotFound
            });

            function insertTokenIfNotFound(existingTokens) {
                if (existingTokens.length > 0) {
                    request.respond(200, existingTokens[0]);
                } else {
                    request.execute();
                }
            }
         }

     이 스크립트에서 같은 토큰을 사용하여 기존 장치에 대한 **Device** 테이블을 확인합니다. 일치하는 장치가 없는 경우 삽입만 진행됩니다. 이를 통해 중복 장치 레코드를 예방할 수 있습니다.

4.  **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다.

     ![](./media/mobile-services-ios-push-notifications-app-users/mobile-insert-script-push2.png
)

5.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
           request.execute({
               success: function() {
                   request.respond();
                   sendNotifications();
               }
           });

           function sendNotifications() {
               var devicesTable = tables.getTable('Devices');
               devicesTable.read({
                   success: function(devices) {
                       // Set timeout to delay the notifications, 
                       // to provide time for the app to be closed 
                       // on the device to demonstrate toast notifications.
                       setTimeout(function() {
                           devices.forEach(function(device) {

                               push.apns.send(device.deviceToken, {
                                   alert: "Toast: " + item.text,
                                   payload: {
                                       inAppMessage: 
                                       "Hey, a new item arrived: '" + 
                                       item.text + "'"
                                   }
                               });
                           });
                       }, 2500);
                   }
               });
           }

          }

    이 삽입 스크립트는 **장치** 테이블에 저장된 모든 장치에 삽입된 항목의 텍스트가 포함된 푸시 알림을 보냅니다.

앱 테스트앱에서 푸시 알림 테스트
--------------------------------

1.  **Run** 단추를 눌러 프로젝트를 빌드하고 iOS 가능 장치에서 앱을 시작한 후 앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 더하기(**+**) 아이콘을 클릭합니다.

   ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push2-ios.png)

1.  알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

   ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push3-ios.png)

1.  2단계를 반복하여 앱을 즉시 닫은 후 다음 알림이 표시되는지 확인합니다.

   ![](./media/mobile-services-ios-push-notifications-app-users/mobile-quickstart-push4-ios.png)

이 자습서를 성공적으로 완료했습니다.

다음 단계
---------

이제 푸시 알림 관련 작업의 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작](/en-us/develop/mobile/tutorials/get-started-with-data-ios)
    
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법을 자세히 알아봅니다.

-   [인증 시작](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
    
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.


