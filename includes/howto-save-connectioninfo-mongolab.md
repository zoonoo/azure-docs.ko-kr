MongoLab URI를 코드에 붙여 넣을 수 있지만 관리하기 쉽도록 환경에서 구성하는 것이 좋습니다. 이렇게 하면 URI가 변경될 경우 코드로 이동하지 않고 Azure 포털을 통해 업데이트할 수 있습니다.

1.  Azure 포털에서 **웹 사이트**를 선택합니다. 
2.  웹 사이트 목록에서 웹 사이트의 이름을 클릭합니다.  
     ![WebSiteEntry](./media/howto-save-connectioninfo-mongolab/entry-website.png)
    
     웹 사이트 대시보드가 표시됩니다.

3.  메뉴 모음에서 **Configure**를 클릭합니다.  
     ![WebSiteDashboardConfig](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websitedashboard-config.png)

4.  연결 문자열 섹션까지 아래로 스크롤합니다.  
     ![WebSiteConnectionStrings](./media/howto-save-connectioninfo-mongolab/focus-mongolab-websiteconnectionstring.png)

5.  **Name**에 MONGOLAB\_URI를 입력합니다. 
6.  **Value**에 이전 섹션에서 얻은 연결 문자열을 붙여 넣습니다. 
7.  **Type** 드롭다운 목록에서 기본값 **SQLAzure** 대신 **Custom**을 선택합니다. 
8.  도구 모음에서 **Save**를 클릭합니다.  
     ![SaveWebSite](./media/howto-save-connectioninfo-mongolab/button-website-save.png)

**참고:** Azure에서 이 변수에 **CUSTOMCONNSTR\_** 접두사를 추가합니다. 위 코드에서 **CUSTOMCONNSTR\_MONGOLAB\_URI**를 참조하는 것은 이 때문입니다.