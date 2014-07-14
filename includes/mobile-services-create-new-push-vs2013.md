먼저 Visual Studio 2013의 푸시 알림 추가 마법사를 사용하여 앱을 Windows 스토어에 등록하고, 푸시 알림을 사용하도록 모바일 서비스를 구성하고, 코드를 추가하여 장치 채널에 앱을 등록합니다.

1.  아직 완료하지 않았다면 [Visual Studio 2013에서 publishsettings 파일 가져오기](/en-us/documentation/articles/mobile-services-windows-how-to-import-publishsettings/) 단계에 따라 publisher.settings 파일을 Visual Studio로 가져오십시오.
    
    Visual Studio를 사용하여 Azure 구독으로 모바일 서비스를 만들고 관리하고 있는 경우에는 이를 실행하지 않아도 됩니다.

2.  Visual Studio 2013에서 솔루션 탐색기를 열어 마우스 오른쪽 단추로 프로젝트를 클릭한 후 **추가**를 클릭하고 **푸시 알림...**을 클릭합니다.
    
    ![mobile-add-push-notifications-vs2013](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)
    
    푸시 알림 추가 마법사를 시작합니다.

3.  **다음**을 클릭하고 Windows 스토어 계정에 로그인한 후 **Reserve a new name**에 이름을 입력한 후 **Reserve**를 클릭합니다.
    
    ![mobile-add-push-notifications-vs2013-2](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-2.png)
    
    이렇게 하면 새 앱이 등록됩니다.

4.  **앱 이름** 목록에서 새로 등록한 앱을 클릭한 후 **다음**을 클릭합니다.
    
    ![mobile-add-push-notifications-vs2013-3](../includes/media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013-3.png)

5.  **Select a service** 대화 상자에서 [모바일 서비스 시작하기](/en-us/develop/mobile/tutorials/get-started/) 또는 [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)를 완료할 때 작성했던 모바일 서비스 이름을 클릭한 후 **다음**과 **마침**을 클릭합니다.
    
    앱 패키지 SID와 클라이언트 암호를 등록하기 위해 모바일 서비스가 업데이트되고 새 **channels** 테이블이 생성됩니다. 이제 모바일 서비스가 WNS(Windows 푸시 알림 서비스)를 통해 앱에 알림을 전송할 수 있도록 구성되었습니다.
		<div class="dev-callout">

	**참고**

	아직 앱이 모바일 서비스에 연결되도록 구성되지 않은 경우 마법사에서 **데이터 시작하기**에 소개된 구성 작업을 완료합니다.
		</div>


<!-- URLs. -->