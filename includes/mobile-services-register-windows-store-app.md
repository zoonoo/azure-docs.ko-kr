
1. 앱을 아직 등록하지 않은 경우 Windows 스토어 앱용 개발자 센터에 있는 [앱 제출 페이지]로 이동하여 Microsoft 계정으로 로그온한 후 **앱 이름**을 클릭합니다.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. **고유 이름을 예약하여 새 앱 만들기**를 선택하고 **계속**을 클릭한 다음 **앱 이름**에서 사용자의 앱 이름을 입력하고 **앱 이름 예약**을 클릭한 다음 **저장**을 클릭합니다.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	이렇게 하면 앱을 새로 Windows 스토어에 등록하게 됩니다.

3. Visual Studio 2013에서 [모바일 서비스 시작] 자습서를 완료했을 때 생성된 프로젝트를 엽니다.

4. 솔루션 탐색기에서 Windows 스토어 앱 프로젝트를 마우스 오른쪽 단추로 클릭하고 **스토어**를 클릭한 후 **응용 프로그램을 스토어에 연결...**을 클릭합니다.

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	**응용 프로그램을 Windows 스토어에 연결** 마법사가 나타납니다.

5. 마법사에서 **로그인**을 클릭한 다음 Microsoft 계정으로 로그인하여 2단계에서 예약한 앱을 선택하고 **다음**을 클릭한 다음 **연결**을 클릭합니다.

   	이렇게 하면 필요한 Windows 스토어 등록 정보가 응용 프로그램 매니페스트에 추가됩니다.

6. (선택 사항) 4~5단계를 반복하여 유니버설 Windows 앱의 Windows Phone 스토어 프로젝트도 등록합니다.

6. 새 앱의 Windows 개발자 센터 페이지로 돌아가서 **Services**를 클릭합니다.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. Services 페이지에서 **Azure Mobile Services** 아래의 **Live Services site**를 클릭합니다.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. **API 설정**을 클릭하고 **모바일 또는 데스크톱 클라이언트 앱** 사용을 선택하여 모바일 서비스 URL을 **대상 도메인**으로 설정하고 **리디렉션 URL**에서 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/`의 값을 지정한 다음 **저장**을 클릭합니다.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. **앱 설정**에서 **클라이언트 ID**, **클라이언트 암호** 및 **SID(보안 식별자)**의 값을 적어 둡니다.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]클라이언트 암호와 패키지 SID는 중요한 보안 자격 증명입니다. 다른 사람과 공유하지 말고 앱과 함께 분산하지 마십시오.

10. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

11. **ID** 탭을 클릭하고 4단계 WNS에서 얻은 **클라이언트 암호** 값과 **패키지 SID** 값을 입력한 후 **저장**을 클릭합니다.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. **ID** 탭을 클릭합니다. **클라이언트 암호** 및 **패키지 SID** 값은 이전 단계에서 이미 설정했습니다. 앞에서 적어 둔 **클라이언트 ID**를 입력한 후 **저장**을 클릭합니다.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
이제 앱에서 인증하는 데 Microsoft 계정을 사용할 준비가 되었습니다.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started/#create-new-service
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Azure 관리 포털]: https://manage.windowsazure.com/

<!---HONumber=62-->