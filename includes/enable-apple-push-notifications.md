
APNS(Apple 푸시 알림 서비스)는 인증서를 사용하여 모바일 서비스를 인증합니다. 필요한 인증서를 만들어 모바일 서비스에 업로드하려면 해당 지침을 따르세요. 공식 APNS 기능 설명서는 [Apple 푸시 알림 서비스](http://go.microsoft.com/fwlink/p/?LinkId=272584)를 참조하세요.

## <a id="certificates"></a>인증서 서명 요청 파일 생성

먼저, 서명된 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1. Utilities 폴더에서 Keychain Access 도구를 실행합니다.

2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. **User Email Address**와 **Common Name**을 선택하고, **Saved to disk**가 선택되어 있는지 확인한 후 **Continue**를 클릭합니다. **CA Email Address** 필드는 필요하지 않으므로 비워둡니다.

  	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. **다른 이름으로 저장**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고 **위치**에서 위치를 선택한 후 **저장**을 클릭합니다.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

  	CSR 파일이 선택한 위치에 저장됩니다. 기본 위치는 바탕 화면입니다. 이 파일을 저장한 위치를 기억해두세요.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만들 것입니다.

## <a id="register"></a>푸시 알림에 대해 앱 등록

모바일 서비스에서 iOS 앱으로 푸시 알림을 보내려면 Apple에 앱을 등록하고 푸시 알림도 등록해야 합니다.  

1. 아직 앱을 등록하지 않은 경우 Apple Developer Center의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 프로비전 포털</a>로 이동하여 Apple ID로 로그온하고 **식별자**와 **앱 ID**를 클릭한 다음 마지막으로 **+** 기호를 클릭하여 새 앱을 등록합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [AZURE.NOTE] <i>MobileServices.Quickstart</i> 외의 <strong>번들 식별자</strong> 값을 입력하는 경우 Xcode 프로젝트에서도 번들 식별자 값을 업데이트해야 합니다. 빠른 시작 프로젝트에서 이미 사용되는 정확한 번들 식별자 값을 사용하는 것이 좋습니다.

2. **설명**에 앱의 이름을 입력하고 **번들 식별자**에 _MobileServices.Quickstart_ 값을 입력한 다음 "앱 서비스" 섹션에서 "푸시 알림" 옵션을 선택하고, **계속**을 클릭합니다. 이 예제에서는 **MobileServices.Quickstart** ID를 사용하지만 실제로 이 ID를 사용해서는 안 됩니다. 모든 사용자는 고유한 앱 ID를 사용해야 하기 때문입니다. 사용자의 전체 이름 또는 이니셜을 앱 이름 뒤에 추가하는 것이 좋습니다.


   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

   그러면 앱 ID가 생성되고 정보를 **제출**하라는 요청이 표시됩니다. **Submit**를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)

   **Submit**를 클릭하면 아래와 같은 **Registration complete** 화면이 표시됩니다. **완료**를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)

3. 방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

   앱 ID를 클릭하면 앱 및 앱 ID에 대한 세부 정보가 표시됩니다. **Settings** 단추를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. 화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

   그러면 "iOS 인증서 추가" 도우미가 표시됩니다.

    > [AZURE.NOTE] 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 서비스로 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5. **Choose File**을 클릭하고, 첫 번째 작업에서 만든 CSR 파일이 저장된 위치로 이동하여 파일을 선택하고, **Generate**를 클릭합니다.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. 포털에서 인증서가 생성되면 **다운로드** 단추와 **완료**를 차례로 클릭합니다.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

   그러면 서명 인증서가 다운로드되어 컴퓨터의 Downloads 폴더에 저장됩니다.

  ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

    > [AZURE.NOTE] 기본적으로 다운로드된 개발 증명서 파일은 이름이 **aps_development.cer**로 지정됩니다.

7. 다운로드한 푸시 인증서 **aps_development.cer**을 두 번 클릭합니다.

   아래와 같이 새 인증서가 Keychain에 설치됩니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

    > [AZURE.NOTE] 인증서의 이름은 다를 수 있지만 **Apple Development iOS Push Services:**가 앞에 옵니다.

나중에 이 인증서를 사용하여 .p12 파일을 생성하고 모바일 서비스에 업로드하여 APNS에서의 인증에 사용하게 됩니다.

## <a id="profile"></a>앱용 프로비저닝 프로필 만들기

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 프로비전닝 포털</a>로 돌아가서 **프로비저닝 프로필**, **모두**를 차례로 선택한 후에 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisiong Profile** 마법사가 실행됩니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. **App ID** 드롭다운 목록에서 모바일 서비스 Quickstart 앱의 앱 ID를 선택하고 **Continue**를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. **Select certificates** 화면에서 앞서 만든 인증서를 선택하고 **Continue**를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. 마지막으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

  새 프로비저닝 프로필이 생성됩니다.

7. Xcode에서 Organizer를 열고 Devices view를 선택하고, 왼쪽 창의 **Library** 섹션에서 **Provisioning Profiles**를 선택하고, 가운데 창의 아래쪽에서 **Refresh** 단추를 클릭합니다.

8. 또는 Xcode 메뉴에서 **Preferences**, **Accounts**를 차례로 선택합니다. 왼쪽 패널에서 Apple 개발자 ID를 선택합니다. 오른쪽에 있는 **View Details** 단추를 클릭합니다. 팝오버 창에서 둥근 **Refresh** 단추를 클릭합니다. 프로비저닝 프로필 목록이 새로 고쳐집니다. 이 프로세스는 몇 분이 걸릴 수 있습니다. 새 프로비저닝 프로필이 표시될 때까지 **Refresh**를 2-3회 클릭하는 것이 좋습니다. 또한 이 Xcode 프로젝트의 번들 식별자가 지금까지 만든 앱 ID 및 프로비저닝 프로필과 연결된 번들 식별자와 같은지 확인합니다.

    ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. **Targets** 아래에서 **Quickstart**를 클릭하고 **Code Signing Identity**를 확장한 다음, **Debug** 아래에서 새 프로필을 선택합니다. 이제 Xcode 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 다음 단계에서는 인증서를 Azure에 업로드해야 합니다.

   ![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)

<!--HONumber=49-->