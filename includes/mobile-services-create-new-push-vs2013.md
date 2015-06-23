다음 단계에서는 앱을 Windows 스토어에 등록하고, 푸시 알림을 사용하도록 모바일 서비스를 구성하고, 앱에 코드를 추가하여 장치 채널을 알림 허브에 등록합니다. Visual Studio 2013에서는 사용자가 제공하는 자격 증명을 통해 Azure와 Windows 스토어에 연결합니다. 

1. Visual Studio 2013에서 솔루션 탐색기를 열어 마우스 오른쪽 단추로 Windows 스토어 앱을 클릭한 후 **추가**를 클릭하고 **푸시 알림...**을 클릭합니다. 

	![Add Push Notification wizard in Visual Studio 2013](./media/mobile-services-create-new-push-vs2013/mobile-add-push-notifications-vs2013.png)

	푸시 알림 추가 마법사를 시작합니다.

2. **다음**을 클릭하고 Windows 스토어 계정에 로그인하여 **새 이름 예약**에 이름을 입력한 후 **예약**을 클릭합니다.

	이렇게 하면 새 앱이 등록됩니다.

3. **앱 이름** 목록에서 새로 등록한 앱을 클릭한 후 **다음**을 클릭합니다.

4. **서비스 선택** 페이지에서 모바일 서비스 이름을 클릭한 후 **다음** 및 **마침**을 클릭합니다. 

	모바일 서비스에서 사용하는 알림 허브가 WNS(Windows 알림 서비스) 등록을 통해 업데이트됩니다. 이제 Azure 알림 허브를 사용하여 WNS를 통해 모바일 서비스에서 앱으로 알림을 보낼 수 있습니다. 

	>[AZURE.NOTE]이 자습서에서는 모바일 서비스 백 엔드에서 알림을 보내는 방법을 보여 줍니다. 이와 동일한 알림 허브 등록을 사용하여 모든 백 엔드 서비스에서 알림을 보낼 수 있습니다. 자세한 내용은 [알림 허브 개요](http://msdn.microsoft.com/library/azure/jj927170.aspx)를 참조하세요.

5. 마법사가 완료되면 Visual Studio에서 **푸시 설정이 거의 완료됨**이라는 새 페이지가 열립니다. 이 페이지에는 이 자습서와 다른 알림을 보내도록 모바일 서비스 프로젝트를 구성하기 위한 또 다른 방법이 자세히 설명되어 있습니다. 

	푸시 알림 추가 마법사를 통해 범용 Windows 앱 솔루션에 추가되는 코드는 플랫폼에 따라 다릅니다. 이 섹션의 뒷부분에서는 범용 앱을 보다 손쉽게 관리할 수 있도록 모바일 서비스 클라이언트 코드를 공유하여 이러한 중복성을 없앱니다.  

<!-- URLs. -->
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started/
[데이터 시작]: /develop/mobile/tutorials/get-started-with-data-dotnet/
[Visual Studio 2013에서 publishsettings 파일 가져오기]: /documentation/articles/mobile-services-windows-how-to-import-publishsettings/

<!--HONumber=47-->
