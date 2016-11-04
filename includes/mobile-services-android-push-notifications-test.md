
### 테스트를 위해 Android 에뮬레이터 설정
에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

> [!IMPORTANT]
> 푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다(에뮬레이터에서 **Settings**로 이동하고 **Add Account** 클릭). 에뮬레이터가 인터넷에 연결되어 있어야 합니다.
> 
> 

1. **Tools**에서 **Open Android Emulator Manager**를 클릭하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.
   
       ![Android Virtual Device Manager](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app7.png)
2. **Target**에서 **Google APIs**를 선택하고 **OK**를 클릭합니다.
   
       ![Android Virtual Device 편집](./media/mobile-services-android-push-notifications-test/notification-hub-create-android-app8.png)
3. 위쪽 도구 모음에서 **Run**을 클릭하고 앱을 선택합니다. 에뮬레이터가 시작되고 앱이 실행됩니다.
   
   앱이 GCM에서 *registrationId*를 검색하고 알림 허브에 등록됩니다.

### 새 항목을 삽입하면 알림이 생성됩니다.
1. 앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.
2. 알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 알림 센터를 엽니다.
   
    ![알림 센터에서 알림 보기](./media/mobile-services-android-push-notifications-test/notification-area-received.png)

<!---HONumber=Oct15_HO3-->