

1. 앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터에 있는 [앱 제출 페이지]로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. **앱 이름**에 앱의 이름을 입력하고 **앱 이름 예약**을 클릭한 후 **저장**을 클릭합니다.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3. Visual Studio 2013에서 **모바일 서비스 시작** 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

4. 솔루션 탐색기에서 오른쪽 마우스 단추로 프로젝트를 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	**응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

5. 마법사에서 **로그인**을 클릭한 후 Microsoft 계정에 로그인합니다.

6. 2단계에서 등록한 앱을 선택하고 **다음**을 클릭한 후 **연결**을 클릭합니다.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

7. (옵션) 4~6단계를 반복하여 범용 Windows 앱의 Windows Phone Store 프로젝트도 등록합니다.

8. 새 앱의 Windows 개발자 센터 페이지로 돌아가서 **Services**를 클릭합니다.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. Services 페이지에서 **Azure Mobile Services** 아래의 **Live Services site**를 클릭합니다.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. **Authenticating your service**를 클릭하고 **Client secret** 및 **Package security identifier (SID)**를 적어둡니다.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE]클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마십시오.

11. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. **푸시** 탭을 클릭하고 WNS에서 얻은 **클라이언트 암호**와 **패키지 SID** 값을 입력한 후 **저장**을 클릭합니다.

	>[AZURE.NOTE]이전 모바일 서비스를 사용하여 이 자습서를 완료하는 경우 **푸시** 탭의 맨 아래에 **향상된 푸시 사용**이라는 링크가 표시될 수 있습니다. 이 링크를 클릭하면 알림 허브와 통합되도록 모바일 서비스가 업그레이드됩니다. 이 변경 작업은 되돌릴 수 없습니다. 프로덕션 모바일 서비스에서 향상된 푸시 알림을 설정하는 방법에 대한 자세한 내용은 <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">이 지침</a>을 참조하세요.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]포털의 **푸시** 탭에서 향상된 푸시 알림에 대한 WNS 자격 증명을 설정하는 경우 앱에 대한 알림 허브를 구성하기 위해 자격 증명을 알림 허브와 공유합니다.

<!-- URLs. -->
[Get started with Mobile Services]: ../articles/mobile-services-windows-store-get-started.md
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 관리 포털]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO3-->