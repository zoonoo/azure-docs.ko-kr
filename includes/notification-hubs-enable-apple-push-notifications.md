
APNS(Apple Push Notification Service)는 인증서를 사용하여 푸시 알림을 인증합니다. 알림을 보내고 받는 데 필요한 푸시 인증서를 만들려면 다음 지침을 따르세요. 공식 APNS 기능 설명서는 [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584)(영문)를 참조하세요.

##인증서 서명 요청 파일 생성

먼저, 서명된 푸시 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1. **Utilities** 폴더 또는 **Other** 폴더에서 Keychain Access 도구를 실행합니다.

2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. **User Email Address**와 **Common Name**을 선택하고, **Saved to disk**가 선택되어 있는지 확인한 후 **Continue**를 클릭합니다. **CA Email Address** 필드는 필요하지 않으므로 비워둡니다.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	CSR 파일이 선택한 위치에 저장됩니다. 기본 위치는 바탕 화면입니다. 이 파일을 저장한 위치를 기억해두세요.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만들 것입니다.

##푸시 알림에 대해 앱 등록

iOS 앱으로 푸시 알림을 보내려면 Apple에 응용 프로그램을 등록하고 푸시 알림도 등록해야 합니다.

1. 아직 앱을 등록하지 않은 경우 Apple Developer Center의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 새 앱을 등록합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. **App ID Description** 이름에 설명이 포함된 앱 이름을 입력합니다.

	**Explicit App ID**에 [앱 배포 가이드](http://go.microsoft.com/fwlink/?LinkId=613485)에 설명된 대로 `<Organization Identifier>.<Product Name>` 형식으로 **Bundle Identifier**를 입력합니다. 사용하는 *Organization Identifier* 및 *Product Name*은 XCode 프로젝트를 만들 때 사용할 조직 식별자 및 제품 이름과 일치해야 합니다. 아래 스크린샷에서는 조직 식별자로 *NotificationHubs*가 사용되고 제품 이름으로 *GetStarted*가 사용되었습니다. 이 값이 XCode 프로젝트에서 사용할 값과 일치하면 XCode에서 올바른 게시 프로필을 사용할 수 있습니다.
	
	App Services 섹션에서 **Push Notifications** 옵션을 선택하고 **Continue**를 클릭합니다.

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

   	그러면 앱 ID가 생성되고 정보를 확인하라는 요청이 표시됩니다. **제출**을 클릭합니다.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


   	**Submit**를 클릭하면 아래와 같은 **Registration complete** 화면이 표시됩니다. **Done**을 클릭합니다.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. 방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

   	앱 ID를 클릭하면 앱 세부 정보가 표시됩니다. **Edit** 단추를 클릭합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. 화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	"OS 인증서 추가" assistant가 표시됩니다.

    > [AZURE.NOTE]이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인하면 됩니다.

5. **Choose File**을 클릭하고, 첫 번째 작업에서 만든 CSR 파일이 저장된 위치로 이동하여 파일을 선택하고, **Generate**를 클릭합니다.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. 포털에서 인증서가 생성되면 **Download** 단추를 클릭하고 **Done**을 클릭합니다.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	서명 인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]기본적으로 다운로드된 개발 증명서 파일은 이름이 **aps_development.cer**로 지정됩니다.

7. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다.

   	아래와 같이 새 인증서가 Keychain에 설치됩니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]인증서의 이름은 다를 수 있지만 **Apple Development iOS Push Services:**가 앞에 옵니다.

이 인증서는 나중에 APNS 인증이 가능한 .p12 파일을 생성하는 데 사용됩니다.

##앱용 프로비저닝 프로필 만들기

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 돌아가서 **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 실행됩니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.


3. **App ID** 드롭다운 목록에서 방금 만든 앱 ID를 선택하고 **Continue**를 클릭합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. **Select certificates** 화면에서 코드 서명에 사용되는 일반적인 개발 인증서를 선택하고 **Continue**를 클릭합니다. 이는 방금 만든 푸시 인증서가 아닙니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. 마지막으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


  	새 프로비저닝 프로필이 생성됩니다.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

<!---HONumber=July15_HO2-->