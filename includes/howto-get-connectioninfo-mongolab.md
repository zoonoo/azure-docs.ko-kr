MongoLab 데이터베이스를 프로비전하면 MongoLab에서 MongoDB의 표준 연결 문자열 형식으로 연결 URI를 Azure에 전송합니다. 이 값은 선택한 MongoDB 드라이버를 통해 MongoDB 연결을 시작하는 데 사용됩니다. 연결 문자열에 대한 자세한 내용은 mongodb.org의 [연결][연결]을 참조하세요.

**이 URI에는 데이터베이스 사용자 이름과 암호가 포함되어 있습니다. 중요한 정보로 처리하고 공유하지 마세요.**

다음 단계에 따라 Azure 포털에서 이 URI를 검색할 수 있습니다.

1.  **추가 기능**을 선택합니다.  
    ![AddonsButton][AddonsButton]
2.  추가 기능 목록에서 해당 MongoLab 서비스를 찾습니다.  
    ![MongolabEntry][MongolabEntry]
3.  추가 기능의 이름을 클릭하여 추가 기능 페이지에 액세스합니다.  
4.  **연결 정보**를 클릭합니다.  
    ![ConnectionInfoButton][ConnectionInfoButton]  
    해당 MongoLab URI가 표시됩니다.  
    ![ConnectionInfoScreen][ConnectionInfoScreen]
5.  MONGOLAB\_URI 값의 오른쪽에 있는 클립보드 단추를 클릭하여 전체 값을 클립보드로 복사합니다.

  [연결]: http://www.mongodb.org/display/DOCS/Connections
  [AddonsButton]: ./media/howto-get-connectioninfo-mongolab/button-addons.png
  [MongolabEntry]: ./media/howto-get-connectioninfo-mongolab/entry-mongolabaddon.png
  [ConnectionInfoButton]: ./media/howto-get-connectioninfo-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/howto-get-connectioninfo-mongolab/dialog-mongolab_connectioninfo.png
