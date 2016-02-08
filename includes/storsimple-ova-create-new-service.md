#### 새 서비스를 만들려면

1.  이 URL [https://manage.windowsazure.com/](https://manage.windowsazure.com/)에서 Microsoft 계정 자격 증명을 사용하여 Azure 클래식 포털에 로그온합니다.

2.  포털에서 **새로 만들기 > 데이터 서비스 > StorSimple Manager > 빠른 생성**을 클릭합니다.

3.  표시되는 양식에서 다음을 수행합니다.

	1.  서비스에 고유한 **이름**을 지정합니다. 이 이름은 서비스를 식별하는 데 사용할 수 있는 친숙한 이름입니다. 이름은 문자, 숫자 및 하이픈이 될 수 있는 2자에서 50자 사이여야 합니다. 이름은 문자 또는 숫자로 시작하고 끝나야 합니다.

	2.  StorSimple 가상 장치를 관리하는 서비스의 경우 **관리되는 장치 유형** 드롭다운 목록에서 **가상 장치 시리즈**를 선택합니다.

	3.  서비스의 **위치**를 지정합니다. 위치는 장치를 배포하려는 지리적 영역을 가리킵니다.

	 -   Azure에 StorSimple 장치에 배포하려는 다른 작업이 있는 경우에는 해당 데이터 센터를 사용하는 것이 좋습니다.

   	 -   StorSimple Manager 및 Azure 저장소는 두 곳의 다른 위치에 있을 수 있습니다. そのような場合、StorSimple Manager と Azure Storage のアカウントを別々に作成する必要があります。 Azure 저장소 계정을 만들려면 포털의 Azure 저장소 서비스로 이동하고 [Azure 저장소 계정 만들기](storage-create-storage-account.md#create-a-storage-account)의 단계를 수행합니다. 이 계정을 만든 후에 [서비스에 대한 새 저장소 계정 구성](#optional-step-configure-a-new-storage-account-for-the-service)의 단계를 수행하여 StorSimple Manager 서비스에 계정을 추가합니다.
   	 
   	 -   미리 보기에서는 StorSimple Manager 서비스를 미국 서부 및 일본 동부에서만 만들 수 있습니다.
	
	1.  드롭다운 목록에서 **구독**을 선택합니다. 구독은 대금 청구 계정에 연결됩니다. 이 필드는 구독이 하나만 있는 경우에는 나타나지 않습니다.

	1.  **새 Azure 저장소 계정 만들기**를 선택하여 서비스를 포함하는 저장소 계정을 자동으로 만듭니다. 이 저장소 계정에는 "storsimplebwv8c6dcnf"와 같은 특수한 이름이 주어집니다. 다른 위치에 있는 데이터가 필요하면 확인란의 선택을 취소합니다.

	1.  **StorSimple 관리자 만들기**를 클릭하여 서비스를 만듭니다.

		![](./media/storsimple-ova-create-new-service/image1-include.png)

	**서비스** 방문 페이지로 이동됩니다. 서비스 만들기에는 몇 분 정도 소요됩니다. 서비스가 성공적으로 만들어진 후 적절한 알림이 표시됩니다.

	![](./media/storsimple-ova-create-new-service/image2-include.png)

	서비스의 상태가 **활성**으로 변경됩니다.

<!---HONumber=AcomDC_0128_2016-->