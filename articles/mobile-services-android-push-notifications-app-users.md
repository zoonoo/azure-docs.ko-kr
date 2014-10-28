<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# 모바일 서비스를 사용하여 사용자에게 푸시 알림

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/ko-kr/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>이 항목에서는 Android 앱의 여러 사용자에게 푸시 알림을 보내는 데 사용할 수 있는 GCM(Google Cloud Messaging) 등록 URI를 저장할 새 테이블을 추가하여 <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-android">이전 푸시 알림 자습서</a>를 확장합니다. 이 자습서에서 단일 업데이트는 ToDoList 테이블에 삽입이 수행될 때마다 등록된 모든 장치에 푸시 알림을 생성합니다. 이전 자습서에서는 삽입을 수행하는 장치에만 알림이 전송되었습니다.</p>
</div>

이 자습서에서는 앱에서 푸시 알림을 업데이트하는 다음 단계를 단계별로 안내합니다.

1.  [Registration 테이블 만들기][Registration 테이블 만들기]
2.  [앱 업데이트][앱 업데이트]
3.  [서버 스크립트 업데이트][서버 스크립트 업데이트]
4.  [푸시 알림 동작 확인][푸시 알림 동작 확인]

이 자습서는 모바일 서비스 빠른 시작을 기반으로 하며 이전 자습서인 [푸시 알림 시작][이전 푸시 알림 자습서]에 내용을 추가했습니다. 이 자습서를 시작하기 전에 먼저 [푸시 알림 시작][이전 푸시 알림 자습서]을 완료해야 합니다.

## <a name="create-table"></a>새 테이블 만들기

1.  [Azure 관리 포털][Azure 관리 포털]에 로그인하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][]

2.  **데이터** 탭을 클릭한 후 **만들기**를 클릭합니다.

    ![][1]

    **Create new table** 대화 상자가 표시됩니다.

3.  모든 권한에 기본 **응용 프로그램 키가 있는 모든 사용자** 설정을 유지하고 **테이블 이름**에 *Registration*을 입력한 후 확인 단추를 클릭합니다.

    ![][2]

    항목 데이터와 별개로 푸시 알림을 보내는 데 사용되는 등록 URI를 저장하는 **Registration** 테이블이 생성됩니다.

**TodoItem** 테이블 대신 이 새 테이블에 등록 데이터를 저장하도록 푸시 알림 앱을 수정합니다.

## <a name="update-app"></a>앱 업데이트

1.  Eclipse의 Package Explorer에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**와 **Class**를 차례로 클릭합니다.

2.  **Name**에 `Registration`을 입력하고 **Finish**를 클릭합니다.

    ![][3]

    새 Registration 클래스가 생성됩니다.

3.  ToDoItem.java 파일을 열고 다음 코드를 잘라냅니다.

        @com.google.gson.annotations.SerializedName("handle")
        private String mHandle;

        public String getHandle() {
            return mHandle;
        }

        public final void setHandle(String handle) {
            mHandle = handle;
        }

4.  이전 단계에서 잘라낸 코드를 이전에 만든 **Registration** 클래스의 본문에 붙여넣습니다.

5.  **Registration** 클래스에 다음 코드를 추가합니다.

        @com.google.gson.annotations.SerializedName("id")
        private int mId;

        /**
         * Returns the item id
         */
        public int getId() {
            return mId;
        }

        /**
         * Sets the item id
         * 
         * @param id : id to set
         */
        public final void setId(int id) {
            mId = id;
        }

6.  **ToDoActivity.java** 파일을 열고 `addItem` 메서드에서 다음 줄을 삭제합니다.

        item.setHandle(MyHandler.getHandle());

7.  `mClient` 속성을 찾은 후 다음 코드로 바꿉니다.

        /**
         * Mobile Service Client reference
         */
        private static MobileServiceClient mClient;

        /**
         * Returns the client reference
         */
        public static MobileServiceClient getClient() {
            return mClient;
        }

8.  **MyHandler** 파일에 다음 import 문을 추가합니다.

        import android.util.Log;

        import com.microsoft.windowsazure.notifications.NotificationsHandler;

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  `onRegistered` 메서드의 끝에 다음 코드를 추가합니다.

        MobileServiceClient client = ToDoActivity.getClient();
        MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
        registrations.insert(registration, new TableOperationCallback<Registration>() {

            public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                if (exception != null) {
                    Log.e("MyHandler", exception.getMessage());
                } else {
                    Log.e("MyHandler", "Registration OK");
                }
            }
        });

이제 사용자에게 푸시 알림을 지원하도록 앱이 업데이트되었습니다.

## <a name="update-scripts"></a>서버 스크립트 업데이트

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **Registration** 테이블을 클릭합니다.

    ![][4]

2.  **등록**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][5]

    **Registration** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            var registrationTable = tables.getTable('Registration');
            registrationTable
                .where({ handle: item.handle })
                .read({ success: insertRegistrationIfNotFound });
            function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
                }
            }
        }

    이 스크립트는 **Registration** 테이블에서 동일한 URI를 가진 기존 등록을 확인합니다. 일치하는 등록을 찾을 수 없는 경우에만 삽입이 진행됩니다. 이 경우 중복 등록 레코드가 생기지 않습니다.

4.  **TodoItem**을 클릭하고 **스크립트**를 클릭한 후 **삽입**을 선택합니다.

    ![][6]

5.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
                    request.respond();
                    sendNotifications(item.text);

                }
            });

        function sendNotifications(item_text) {
            var registrationTable = tables.getTable('Registration');
            registrationTable.read({
                success: function(registrations) {
                    registrations.forEach(function(registration) {
                        push.gcm.send(registration.handle, item_text, {
                            success: function(response) {
                                console.log('Push notification sent: ', response);
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }

    }

    이 삽입 스크립트는 **Registration** 테이블에 저장된 모든 등록에 삽입된 항목의 텍스트가 포함된 푸시 알림을 보냅니다.

## <a name="test-app"></a>앱 테스트

1.  Eclipse의 **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

2.  앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.

3.  화면 아래쪽에 검은색 알림 상자가 잠시 나타납니다.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

이 자습서를 성공적으로 완료했습니다.

## 다음 단계

이제 푸시 알림 관련 작업의 기본 사항을 설명하는 자습서를 마쳤습니다. 다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

-   [인증 시작하기][인증 시작하기]
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

-   [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법][모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법]
    .NET과 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Phone]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone"
  [iOS]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios "iOS"
  [Android]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-android "Android"
  [이전 푸시 알림 자습서]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
  [Registration 테이블 만들기]: #create-table
  [앱 업데이트]: #update-app
  [서버 스크립트 업데이트]: #update-scripts
  [푸시 알림 동작 확인]: #test-app
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [데이터 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-data-android
  [인증 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-with-users-android
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
  [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법]: /ko-kr/develop/mobile/how-to-guides/work-with-android-client-library
