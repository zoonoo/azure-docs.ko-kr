1. [Google 클라우드 콘솔](https://console.developers.google.com/project)로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 다음 **프로젝트 선택**을 클릭하고 **프로젝트 만들기**를 클릭합니다.
2. 프로젝트 이름을 입력하고 서비스 약관에 동의한 후 **Create**를 클릭합니다. 요청된 경우 SMS 확인을 수행하고 **Create**를 다시 클릭합니다.
3. **Projects** 섹션에 있는 프로젝트 번호를 기록해 둡니다.
   
    이 자습서 뒷부분에서 이 값을 클라이언트의 PROJECT\_ID 변수로 설정합니다.
4. **사용자 Google API** 아래에서 **API 사용 및 관리**를 클릭하고 **Android용 Cloud Messaging**을 클릭합니다. 다음 페이지에서 **API 사용**을 클릭합니다.
5. **자격 증명**을 클릭한 다음 **자격 증명 추가**->**API 키**를 클릭합니다.
6. **Create a new key**에서 **Server key**를 클릭합니다. 다음 창에서 **Create**를 클릭합니다.
7. **API key** 값을 기록해 둡니다.
   
    이 API 키를 사용하여 Azure 에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

<!---HONumber=AcomDC_1203_2015-->