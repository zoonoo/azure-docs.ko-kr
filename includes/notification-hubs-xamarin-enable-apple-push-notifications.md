

APNS(Apple Push Notification Service)를 통해 푸시 알림용 앱을 등록하려면 Apple 개발자 포털에서 프로젝트에 대한 새 푸시 인증서, 앱 ID 및 프로비저닝 프로필을 만들어야 합니다. 앱 ID에는 앱이 푸시 알림을 보내고 받도록 하는 구성 설정이 포함됩니다. 이러한 설정에는 푸시 알림을 보내고 받을 때 APNS(Apple Push Notification Service)에서 인증하는 데 필요한 푸시 알림 인증서가 포함됩니다. 이러한 개념에 대한 자세한 내용은 [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584) 공식 설명서를 참조하세요.

#### <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>푸시 인증서에 대한 인증서 서명 요청 파일 생성
다음 단계는 인증서 서명 요청을 만들 수 있도록 안내합니다. APNS에서 사용할 푸시 인증서를 생성하는 데 사용됩니다.

1. Mac에서 Keychain Access 도구를 실행합니다. **Utilities** 폴더 또는 **Other** 폴더에서 열 수 있습니다.
2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. **User Email Address**와 **Common Name**을 선택하고, **Saved to disk**가 선택되어 있는지 확인한 후 **Continue**를 클릭합니다. **CA Email Address** 필드는 필요하지 않으므로 비워둡니다.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)
4. **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)
   
      CSR 파일이 선택한 위치에 저장됩니다. 기본 위치는 바탕 화면입니다. 이 파일을 저장한 위치를 기억해두세요.

#### <a name="register-your-app-for-push-notifications"></a>푸시 알림에 대해 앱 등록
Apple 응용 프로그램에 대한 새 명시적 앱 ID를 만들고 또한 푸시 알림용으로 구성합니다.  

1. Apple Developer Center의 [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 차례로 클릭한 다음 **+** 기호를 클릭하여 새 앱을 등록합니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)
2. 새 앱에 다음과 같은 세 개의 필드를 업데이트한 다음 **Continue**를 클릭합니다.
   
   * **Name**: **App ID Description** 섹션의 **Name** 필드에서 앱에 대한 설명이 포함된 이름을 입력합니다.
   * **번들 식별자**: **명시적 앱 ID** 섹션에 [앱 배포 가이드](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)에 설명된 대로 `<Organization Identifier>.<Product Name>` 형식으로 **번들 식별자**를 입력합니다. 또한 앱용 XCode, Xamarin 또는 Cordova 프로젝트에서 사용된 것과 일치해야 합니다.
   * **Push Notifications**: **App Services** 섹션에서 **Push Notifications** 옵션을 선택합니다.
     
     ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)
3. 앱 ID 확인 화면에서 설정을 검토하고 확인한 후 **Register**를 클릭합니다.
4. 새 앱 ID를 제출한 후 **Registration complete** 화면이 표시됩니다. **Done**을 클릭합니다.
5. 개발자 센터의 앱 ID에서 방금 만든 앱 ID를 찾아 해당 행을 클릭합니다. 앱 ID 행을 클릭하면 앱 세부 정보가 표시됩니다. 아래쪽에 있는 **Edit** 단추를 클릭합니다.
6. 화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)
   
       This displays the "Add iOS Certificate" assistant.
   
   > [!NOTE]
   > 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인하면 됩니다.
   > 
   > 
7. **Choose File**을 클릭하고 푸시 인증서에 대한 CSR을 저장한 위치를 찾습니다. 그런 다음 **Generate**를 클릭합니다.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)
8. 포털에서 인증서가 만들어지면 **Download** 단추를 클릭합니다.
   
      ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)
   
       This downloads the signing certificate and saves it to your computer in your Downloads folder.
   
      ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)
   
   > [!NOTE]
   > 기본적으로 다운로드된 개발 인증서 파일은 이름이 **aps_development.cer**로 지정됩니다.
   > 
   > 
9. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다. 아래와 같이 새 인증서가 Keychain에 설치됩니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)
   
   > [!NOTE]
   > 인증서의 이름은 다를 수 있지만 **Apple Development iOS Push Services:**가 앞에 옵니다.
   > 
   > 
10. Keychain Access에서는 **Certificates** 범주에서 방금 만든 새 푸시 인증서를 Ctrl+클릭합니다. **내보내기**를 클릭하고 파일의 이름을 지정한 후 **.p12** 형식을 선택하고 **저장**을 클릭합니다.
    
    내보낸 .p12 푸시 인증서의 파일 이름과 위치를 기억합니다. Azure 클래식 포털에서 업로드하여 APNS에서 인증할 수 있도록 하는 데 사용됩니다. **.p12** 형식 옵션을 사용할 수 없는 경우 Keychain Access를 다시 시작해야 할 수 있습니다.

#### <a name="create-a-provisioning-profile-for-the-app"></a>앱용 프로비저닝 프로필 만들기
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 돌아가서 **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 실행됩니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)
2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.
3. **App ID** 드롭다운 목록에서 방금 만든 앱 ID를 선택하고 **Continue**를 클릭합니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)
4. **Select certificates** 화면에서 코드 서명에 사용되는 개발 인증서를 선택하고 **Continue**를 클릭합니다. 이 인증서는 방금 만든 푸시 인증서가 아닌 서명 인증서입니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)
       
5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)
6. 마지막으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**를 클릭합니다.
   
       ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

