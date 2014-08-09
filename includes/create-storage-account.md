저장소 작업을 사용하려면 Azure 저장소 계정이 필요합니다. 다음 단계에 따라 저장소 계정을 만들 수 있습니다. [REST API를 사용][1]하여 저장소 계정을 만들 수도 있습니다.

1.  [Azure 관리 포털][2]에 로그인합니다.

2.  탐색 창 맨 아래에 있는 **NEW**를 클릭합니다.
    
    ![+새로 만들기](./media/create-storage-account/plus-new.png)

3.  **ATA SERVICES**, **STORAGE**, **QUICK CREATE**를 차례로 클릭합니다.
    
    ![빨리 만들기 대화 상자](./media/create-storage-account/quick-storage-2.png)

4.  URL에서 저장소 계정의 URI에 사용할 하위 도메인 이름을 입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수 있습니다. 이 값은 구독에 대한 Blob, 큐 또는 테이블 리소스의 주소를 지정하는 데 사용되는 URI 내의 호스트 이름이 됩니다.

5.  저장소를 찾을 영역/선호도 그룹을 선택합니다. Azure 응용 프로그램에서 저장소를 사용하는 경우 응용 프로그램을 배포할 영역과 동일한 영역을 선택합니다.

6.  선택적으로, 지역에서 복제를 사용하도록 설정할 수 있습니다.

7.  **CREATE STORAGE ACCOUNT**를 클릭합니다.



[1]: http://msdn.microsoft.com/ko-kr/library/windowsazure/hh264518.aspx
[2]: http://manage.windowsazure.com