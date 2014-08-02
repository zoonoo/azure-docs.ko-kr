

1.  앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터에 있는 [앱 제출 페이지](http://go.microsoft.com/fwlink/p/?LinkID=266582)로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  **앱 이름**에 앱의 이름을 입력하고 **앱 이름 예약**을 클릭한 후 **저장**을 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

  이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3.  Visual Studio 2012 Express for Windows 8에서 [모바일 서비스 시작](/en-us/documentation/articles/mobile-services-windows-store-get-started/) 자습서를 완료할 때 만든 프로젝트를 엽니다.

4.  솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png)

  **응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

1.  마법사에서 **로그인**을 클릭한 후 Microsoft 계정에 로그인합니다.

2.  2단계에서 등록한 앱을 선택하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

  이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.    

3.  새 앱의 Windows 개발자 센터 페이지로 돌아가서 **서비스**를 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

4.  서비스 페이지에서 **Azure 모바일 서비스** 아래의 **Live 서비스 사이트**를 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  **Authenticating your service**를 클릭하고 **Client secret** 및 **Package security identifier (SID)**를 적어둡니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    **보안 정보**

    클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마십시오.

6.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

1.  **푸시** 탭을 클릭하고 **향상된 푸시 사용**을 클릭한 다음 **예**를 클릭하여 구성 변경을 적용합니다.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    이렇게 하면 알림 허브에서 제공하는 향상된 푸시 알림 기능을 사용하도록 모바일 서비스의 구성이 업데이트됩니다. 일부 알림 허브는 유료 모바일 서비스에서 무료로 사용할 수 있습니다. 자세한 내용은 [모바일 서비스 가격 정보](http://go.microsoft.com/fwlink/p/?LinkID=311786)를 참조하십시오.

    **중요**

    이 작업은 푸시 자격 증명을 다시 설정하여 스크립트에서 푸시 메서드의 동작을 변경합니다. 이러한 변경은 되돌릴 수 없습니다. 이 메서드를 사용하여 프로덕션 모바일 서비스에 알림 허브를 추가하지 마십시오. 프로덕션 모바일 서비스에서 향상된 푸시 알림을 사용하도록 설정하는 방법에 대한 지침은 [이 지침](http://go.microsoft.com/fwlink/p/?LinkId=391951)을 참조하십시오.

2.  4단계의 WNS에서 얻은 **클라이언트 암호** 및 **패키지 SID** 값을 입력한 다음 **저장**을 클릭합니다.

  ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

  > [WACOM.NOTE] 포털의 **푸시** 탭에서 향상된 푸시 알림에 대한 WNS 자격 증명을 설정하는 경우 앱을 사용하여 알림 허브를 구성하기 위해 자격 증명을 알림 허브와 공유합니다.
