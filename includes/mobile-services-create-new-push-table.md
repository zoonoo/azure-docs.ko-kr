1.  [Azure 관리 포털][1]에 로그인하여 **Mobile Services**를 클릭한 후 앱을 클릭합니다.
    
    ![](./media/mobile-services-create-new-push-table/mobile-services-selection.png)

2.  **Data** 탭을 클릭한 후 **Create**를 클릭합니다.
    
    ![](./media/mobile-services-create-new-push-table/mobile-create-table.png)
    
    **Create new table** 대화 상자가 표시됩니다.

3.  모든 권한에 기본 **Anybody with the application key** 설정을 유지하고 **Table name**에 *Registrations*를 입력한 후 확인 단추를 클릭합니다.
    
    ![](./media/mobile-services-create-new-push-table/mobile-create-registrations-table.png)

	푸시 알림을 보내기 위해 사용되는 채널 URI를 저장하는 **Registrations** 테이블이 생성됩니다.

다음 과정에서는 앱에서 푸시 알림을 사용하도록 수정합니다.

<!-- URLs -->



[1]: https://manage.windowsazure.com/