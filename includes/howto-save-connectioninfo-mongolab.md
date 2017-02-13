MongoLab URI를 코드에 붙여 넣을 수 있지만 관리하기 쉽도록 환경에서 구성하는 것이 좋습니다. 이렇게 하면 URI가 변경될 경우 코드로 이동하지 않고 Azure 포털을 통해 업데이트할 수 있습니다.

1. Azure 포털에서 **Web Apps**를 선택합니다.
2. 웹 앱 목록에서 웹 앱의 이름을 클릭합니다.  
   ![WebAppEntry][entry-website]  
   웹 앱 대시보드가 표시됩니다.
3. 메뉴 모음에서 **Configure** 을 클릭합니다.  
   ![WebAppDashboardConfig][focus-mongolab-websitedashboard-config]
4. 연결 문자열 섹션까지 아래로 스크롤합니다.  
   ![WebAppConnectionStrings][focus-mongolab-websiteconnectionstring]
5. **이름**에 MONGOLAB_URI를 입력합니다.
6. **Value**에 이전 섹션에서 얻은 연결 문자열을 붙여 넣습니다.
7. **형식** 드롭다운 목록에서 기본값 **SQLAzure** 대신 **사용자 지정**을 선택합니다.
8. 도구 모음에서 **저장**을 클릭합니다.  
   ![SaveWebApp][button-website-save]

**참고:** Azure는 **CUSTOMCONNSTR\_** 접두사를 변수에 추가합니다. 위의 코드에서 **CUSTOMCONNSTR\_MONGOLAB_URI**를 참조하는 것은 이 때문입니다.

[entry-website]: ./media/howto-save-connectioninfo-mongolab/entry-website.png
[focus-mongolab-websitedashboard-config]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png
[focus-mongolab-websiteconnectionstring]: ./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png
[button-website-save]: ./media/howto-save-connectioninfo-mongolab/button-website-save.png


<!--HONumber=Jan17_HO3-->


