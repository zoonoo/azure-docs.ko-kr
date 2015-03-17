## <a name="create-account"> </a>Azure 저장소 계정 만들기

Azure 저장소를 사용하려면 저장소 계정이 있어야 합니다. 다음 
단계에 따라 저장소 계정을 만들 수 있습니다. 또는
Azure 서비스 관리 클라이언트 라이브러리 또는 서비스 관리[REST API]를 사용하여 저장소 계정을 만들 수도 있습니다.

1.  [Azure 관리 포털]에 로그인합니다.

2.  탐색 창 맨 아래에 있는 **새로 만들기**를 클릭합니다.

	![+new][plus-new]

3.  **데이터 서비스**, **저장소** 및 **빠른 생성**을 차례로 클릭합니다.

	![Quick create dialog][quick-create-storage]

4.  URL에서 저장소 계정의 URI에 사용할 하위 도메인 이름을
    입력합니다. 이 입력에는 3-24자의 소문자와 숫자를 사용할 수
    있습니다. 이 값은 UIR 내에서 구독에 대한 Blob, 큐 또는 테이블
    리소스를 주소 지정하는 데 사용되는 호스트 이름이
    됩니다.

5.  저장소를 찾을 영역/선호도 그룹을
    선택합니다. Azure 응용 프로그램에서 저장소를 사용하려는 경우
    응용 프로그램을 배포할 곳과 동일한 지역을
    선택합니다.

6. 선택적으로, 계정에 필요한 복제 유형을 선택할 수 있습니다. 지역 중복 복제는 기본값이며 최대의 영속성을 제공합니다. 복제 옵션에 대한 자세한 내용은 [Azure 저장소 중복 옵션](http://msdn.microsoft.com/library/azure/dn727290.aspx) 및 [Azure 저장소 팀 블로그](http://blogs.msdn.com/b/windowsazurestorage/)(영문)를 참조하세요.

6.  **저장소 계정 만들기**를 클릭합니다.

[REST API]: http://msdn.microsoft.com/library/windowsazure/hh264518.aspx
[Azure 관리 포털]: http://manage.windowsazure.com
[plus-new]: ./media/storage-create-account-include/plus-new.png
[quick-create-storage]: ./media/storage-create-account-include/quick-storage-2.png
<!--HONumber=42-->
