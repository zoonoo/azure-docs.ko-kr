
아래 지침과 스크린샷은 Windows 스토어 클라이언트 테스트에 적용되지만 Azure 모바일 서비스에서 지원하는 다른 플랫폼에서도 테스트할 수 있습니다.

1. Visual Studio에서 클라이언트 앱을 실행하고 앞서 만든 Dave라는 사용자 계정으로 인증을 시도합니다. 

    ![](./media/mobile-services-aad-rbac-test-app/dave-login.png)

2. Dave는 영업 그룹에 대한 멤버 자격이 없습니다. 따라서 역할 기반 액세스 권한 검사 결과 테이블 작업에 대한 액세스가 거부됩니다. 클라이언트 앱을 닫습니다.

    ![](./media/mobile-services-aad-rbac-test-app/unauthorized.png)

3. Visual Studio에서 클라이언트 앱을 다시 실행합니다. 이번에는 앞서 만든 Bob이라는 사용자 계정으로 인증합니다.

    ![](./media/mobile-services-aad-rbac-test-app/bob-login.png)

4. Bob은 영업 그룹에 대한 멤버 자격을 갖고 있습니다. 따라서 역할 기반 액세스 권한 검사 결과 테이블 작업에 대한 액세스가 허용됩니다.

    ![](./media/mobile-services-aad-rbac-test-app/success.png)

<!---HONumber=62-->