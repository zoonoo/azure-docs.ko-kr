1.  [Azure 관리 포털][Azure 관리 포털]의 디렉터리 페이지에서 **응용 프로그램** 탭을 클릭합니다.

2.  통합 응용 프로그램 등록을 클릭합니다.

3.  응용 프로그램 페이지에서 **구성**을 클릭하고 페이지의 **키** 섹션까지 아래로 스크롤합니다.
4.  새 키에 대해 **1년** 기간을 클릭합니다. **저장**을 클릭하면 포털에 새 키 값이 표시됩니다.
5.  저장한 후 표시되는 **클라이언트 ID** 및 **키**를 복사합니다. 키 값은 저장한 후 한 번만 표시됩니다.

    ![][0]

6.  통합 응용 프로그램 구성 페이지의 맨 아래까지 아래로 스크롤한 다음 응용 프로그램에 대해 **디렉터리 데이터 읽기** 권한을 사용하도록 설정하고 **저장**을 클릭합니다.

    ![][1]

7.  [Azure 관리 포털][Azure 관리 포털]에서 모바일 서비스로 다시 이동한 다음 **구성** 탭을 클릭합니다. **앱 설정** 섹션까지 아래로 스크롤하고 다음 앱 설정을 추가한 다음 **저장**을 클릭합니다.

    | 앱 설정 이름        | 설명                                                                 |
    |---------------------|----------------------------------------------------------------------|
    | AAD\_CLIENT\_ID     | 위 단계 중 통합 앱에서 복사한 클라이언트 ID입니다.                   |
    | AAD\_CLIENT\_KEY    | 위 단계 중 AAD 통합 앱에서 생성한 앱 키입니다.                       |
    | AAD\_TENANT\_DOMAIN | AAD 도메인 이름입니다. "mydomain.onmicrosoft.com"과 유사해야 합니다. |
    | AAD\_GROUP\_ID      | 이전 섹션에서 확인한 Sales 그룹의 그룹 ID입니다.                     |

    ![][2]

  [Azure 관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/client-id-and-key.png
  [1]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/app-perms.png
  [2]: ./media/mobile-services-generate-aad-app-registration-access-key-rbac/aad-app-settings.png
