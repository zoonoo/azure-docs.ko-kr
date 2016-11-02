
1. [Azure 포털](https://azure.portal.com/)에서 **찾아보기** > **앱 서비스**를 클릭하고, 모바일 앱 백 엔드 > **모든 설정**을 클릭한 다음 **모바일** 아래에서 **푸시**를 클릭합니다.

2. 푸시 알림 서비스에서 **Windows(WNS)**를 클릭하고, Live 서비스 사이트에서 가져온 **보안 키**(클라이언트 암호) 및 **패키지 SID**를 입력한 후 **저장**을 클릭합니다.

    ![포털에서 GCM API 키 설정하기](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

모바일 앱 백엔드는 이제 해당 알림 허브를 사용하는 Windows 앱에 WNS를 사용하여 푸시 알림을 전송하도록 구성됩니다.

<!---HONumber=AcomDC_1203_2015-->