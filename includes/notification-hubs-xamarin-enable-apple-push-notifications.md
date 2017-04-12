
APNS(Apple Push Notification Service)를 통해 푸시 알림용 앱을 등록하려면 Apple 개발자 포털에서 프로젝트에 대한 푸시 인증서, 앱 ID 및 프로비전 프로필을 만들어야 합니다.

앱 ID에는 앱이 푸시 알림을 보내고 받도록 하는 구성 설정이 포함됩니다. 이러한 설정에는 앱이 푸시 알림을 보내고 받을 때 APNS에서 인증하는 데 필요한 푸시 알림 인증서가 포함됩니다.

이러한 개념에 대한 자세한 내용은 [Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) 공식 설명서를 참조하세요.

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>푸시 인증서에 대한 인증서 서명 요청 파일 생성
다음 단계에서는 APNS에서 사용되는 푸시 인증서를 생성하는 인증서 서명 요청을 만드는 과정을 설명합니다.

1. Mac에서 Keychain Access 도구를 실행합니다. **Utilities** 폴더 또는 **Other** 폴더에서 열 수 있습니다.

2. **Keychain 액세스**를 선택하고 **인증서 도우미**를 확장한 다음 **인증 기관에서 인증서 요청...**을 선택합니다.

      ![인증서 요청](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. **사용자 전자 메일 주소** 및 **일반적인 이름**을 선택합니다.

4. **디스크에 저장됨**을 선택했는지 확인한 다음 **계속**을 선택합니다. **CA 전자 메일 주소** 필드는 필요하지 않으므로 비워둡니다.

      ![인증서 정보](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. **다른 이름으로 저장**에서 CSR(인증서 서명 요청) 파일의 이름을 입력합니다.
5. **어디에서**에서 위치를 선택한 다음 **저장**을 선택합니다.

      ![인증서 서명 요청 저장](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      CSR 파일이 선택한 위치에 저장 합니다. (기본 위치는 바탕 화면입니다.) 이 파일을 저장한 위치를 기억해두세요.

## <a name="register-your-app-for-push-notifications"></a>푸시 알림에 대해 앱 등록
Apple 응용 프로그램에 대한 새 명시적 앱 ID를 만든 다음 푸시 알림용으로 구성합니다.  

1. Apple 개발자 센터에서 [iOS 프로비전 포털](http://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동한 다음 Apple ID로 로그인합니다.

2. **식별자**를 선택한 다음 **앱 ID**를 선택합니다.

3. **+** 기호를 선택하여 새 앱을 등록합니다.

      ![새 앱 등록](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. 새 앱에 다음과 같은 세 개의 필드를 업데이트한 다음 **계속**을 선택합니다.

   * **이름**: **앱 ID 설명** 섹션에서 앱에 대한 설명이 포함된 이름을 입력합니다.

   * **번들 식별자**: **명시적 앱 ID** 섹션에 [앱 배포 가이드](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)에 설명된 대로 `<Organization Identifier>.<Product Name>` 형식으로 **번들 식별자**를 입력합니다. 앱의 XCode, Xamarin 또는 Cordova 프로젝트에서 사용된 것과 일치해야 합니다.

   * **푸시 알림**: **App Services** 섹션에서 **푸시 알림** 옵션을 선택합니다.

     ![앱 ID 등록](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. **앱 ID 화면 확인**에서 설정을 검토합니다. 설정을 확인한 후에 **등록**을 선택합니다.

6. 새 앱 ID를 제출한 후 **Registration complete** 화면이 표시됩니다. **완료**를 선택합니다.

7. 개발자 센터의 **앱 ID**에서 만든 앱 ID를 찾아 해당 행을 선택합니다. 앱 ID 행을 선택하면 앱 세부 정보가 표시됩니다. 그러면 아래쪽에 있는 **편집** 단추를 선택합니다.

8. 화면 아래로 스크롤하여 **개발 SSL 인증서** 섹션에서 **인증서 만들기...** 단추를 선택합니다.

      ![개발 푸시 SSL 인증서](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 "Add iOS Certificate" assistant가 표시됩니다.

   > [!NOTE]
   > 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때 동일한 프로세스를 사용합니다. 알림을 보낼 때 동일한 인증서 유형을 사용하는지만 확인합니다.
   >

9. **파일 선택**을 선택하고 푸시 인증서에 대한 CSR을 저장한 위치로 이동합니다. 그런 다음 **생성**을 선택합니다.

      ![인증서 생성](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. 포털에서 인증서가 만들어진 후에 **다운로드** 단추를 선택합니다.

      ![인증서 준비](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       서명 인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.

      ![다운로드 폴더](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > 기본적으로 다운로드된 개발 인증서 파일은 이름이 **aps_development.cer**로 지정됩니다.
   >
   >
11. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다. 다음 스크린샷에서 표시된 것처럼 새 인증서가 Keychain에 설치됩니다.

       ![Keychain 액세스](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > 인증서의 이름은 다를 수 있지만 **Apple 개발 iOS 푸시 서비스:**가 앞에 옵니다.
   >
   >
12. **Keychain 액세스**에서는 **인증서** 범주에서 만든 새 푸시 인증서를 선택합니다. **내보내기**를 선택하고 파일의 이름을 지정한 다음 **.p12** 형식 및 **저장**을 선택합니다.

    내보낸 .p12 푸시 인증서의 파일 이름과 위치를 기억합니다. Azure 클래식 포털에서 업로드하여 APNS에서 인증할 수 있도록 하는 데 사용됩니다. **.p12** 형식 옵션을 사용할 수 없는 경우 Keychain 액세스를 다시 시작해야 합니다.

## <a name="create-a-provisioning-profile-for-the-app"></a>앱용 프로비저닝 프로필 만들기
1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 프로비전 포털</a>로 돌아가서 **프로비전 프로필** 및 **모두**를 차례로 선택하고 **+** 단추를 선택하여 프로필을 만듭니다. **iOS 프로비전 프로필 추가** 프로필 도구가 시작됩니다.

      ![프로비전 프로필](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. **개발**에서 프로비전 프로필 유형으로 **iOS 앱 개발**을 선택하고 **계속**을 클릭합니다.

3. **앱 ID** 드롭다운 목록에서 만든 앱 ID를 선택하고 **계속**을 선택합니다.

      ![앱 ID 선택](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. **인증서 선택** 화면에서 코드 서명에 사용하는 개발 인증서를 선택하고 **계속**을 선택합니다. 이 인증서는 만든 푸시 인증서가 아닌 서명 인증서입니다.

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. 테스트에 사용할 **장치**를 선택하고 **계속**을 선택합니다.

     ![프로비전 프로필 추가](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. 마지막으로 **프로필 이름**에서 프로필의 이름을 선택하고 **생성**을 선택합니다.

      ![프로필 이름 지정](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
