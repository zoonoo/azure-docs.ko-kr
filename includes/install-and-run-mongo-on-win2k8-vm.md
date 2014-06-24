다음 단계에 따라 Windows Server 2008 R2를 실행 중인 가상 컴퓨터에 MongoDB를 설치 및 실행하십시오.
<div class="dev-callout">
<b>경고</b>
<p>인증 및 IP 주소 바인딩과 같은 MongoDB 보안 기능은 기본적으로 사용하도록 설정되어 있지 않습니다. MongoDB를 프로덕션 환경에 배포하기 전에 보안 기능을 사용하도록 설정해야 합니다.  자세한 내용은 <a  href="http://www.mongodb.org/display/DOCS/Security+and+Authentication">보안 및 인증</a>(영문)을 참조하십시오.</p>
</div>

 1.  원격 데스크톱을 사용하여 가상 컴퓨터에 연결한 후 **시작** 메뉴에서 Internet Explorer를 엽니다.
2.  오른쪽 위에 있는 **도구** 버튼을 선택합니다. **인터넷 옵션**에서 **보안** 탭을 선택한 후 **신뢰할 수 있는
    사이트** 아이콘을 선택하고 마지막으로 **사이트** 버튼을 클릭합니다. 신뢰할 수 있는 사이트 목록에
    *http://\*.mongodb.org*를 추가합니다.
3.  [Downloads- MongoDB][1](영문)로 이동합니다.
4.  **Production Release (Recommended)** 섹션에서 최신 릴리스를 찾아 Windows 64-bit
    열에 있는 **\*2008+** 링크를 클릭합니다. **다른 이름으로 저장**을 클릭하여 압축 파일을 데스크톱에
    저장합니다.
5.  Zip 파일을 마우스 오른쪽 단추로 클릭하고 **압축 풀기...**를 선택합니다. "C:"를 지정하고 **압축 풀기**를
    클릭합니다. 파일의 압축을 푼 후 설치 폴더 이름을 보다 간단하게 바꿀 수 있습니다. 예를 들어 "MongoDB"
6.  위 단계에서 만든 데이터 디스크(예: **F:** 드라이브)에 MongoDB 데이터 및 로그 디렉터리를 만듭니다.
    **시작** 메뉴에서 **명령 프롬프트** 메뉴를 선택하여 명령 프롬프트 창을 엽니다. 다음을 입력합니다.
    
         C:\> F:
         F:\> mkdir \MongoData
         F:\> mkdir \MongoLogs

7.  데이터베이스를 실행하려면 다음을 실행합니다.
    
         F:\> C:
         C:\> cd \MongoDB\bin
         C:\my_mongo_dir\bin> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log
    
    mongod.exe 서버가 시작되고 저널 파일을 사전 할당되면 모든 로그 메시지가
    *F:\\MongoLogs\\mongolog.log* 파일로 보내집니다. MongoDB가 저널 파일을 사전 할당하고 연결이
    수신될 때까지 몇 분이 걸릴 수 있습니다.

8.  MongoDB 관리 셸을 시작하려면 **시작** 메뉴에서 명령 창을 하나 더 열어 다음을 입력합니다.
    
         C:\> cd \my_mongo_dir\bin  
         C:\my_mongo_dir\bin> mongo  
         >db  
         test  	  
         > db.foo.insert( { a : 1 } )  
         > db.foo.find()  
         { _id : ..., a : 1 }  
         > show dbs  
         ...  
         > show collections  
         ...  
         > help  
    
    이렇게 하면 데이터베이스가 만들어집니다.

9.  (옵션) mongod.exe를 Windows 서비스로 설치 및 실행할 수 있습니다. mongod.exe를 서비스로
    설치하려면 명령 프롬프트에서 다음을 실행합니다.
    
         C:\mongodb\bin>mongod --logpath "c:\mongodb\logs\logfile.log" --logappend --dbpath "c:\data" --install 
    
    이렇게 하면 이름이 "Mongo DB"인 서비스가 생성되고 "Mongo DB"에 대한 설명이 지정됩니다. 실행 중인
    서비스는 명령 창에 결과가 표시되지 않으므로 **--logpath** 옵션으로 로그 파일을 지정해야 합니다.
    **--logpath** 옵션은 서비스를 다시 시작할 때 기존 로그 파일에 출력을 추가하도록 지정합니다.
    **--dbpath** 옵션은 데이터 디렉터리 위치를 지정합니다. 서비스 관련 명령줄 옵션에 대한 자세한 내용은
    [Service-related command line options][2](영문)를 참조하십시오.

10. 이제 MongoDB가 설치되고 실행됩니다. 원격으로 MongoDB에 연결하려면 Windows 방화벽에 있는 포트를 열어야
    합니다. **시작** 메뉴에서 **관리자 도구**를 선택한 후 **고급 보안이 포함된 Windows 방화벽**을
    선택합니다.

11. 왼쪽 창에서 **인바운드 규칙**을 선택합니다. 오른쪽에 있는 **작업** 창에서 **새 규칙...**을 선택합니다.
    
    ![Windows
    방화벽](./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png)
    
    **새 인바운드 규칙 마법사**에서 **포트**를 선택한 후 **다음**을 클릭합니다.
    
    ![Windows
    방화벽](./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png)
    
    **TCP**를 선택한 후 **특정 로컬 포트**를 선택합니다. "27107" 포트(MongoDB의 수신 포트)를 지정하고
    **다음**을 클릭합니다.
    
    ![Windows
    방화벽](./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png)
    
    **연결 허용**을 선택하고 **다음**을 클릭합니다.
    
    ![Windows
    방화벽](./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png)
    
    다시 **다음**을 클릭합니다.
    
    ![Windows
    방화벽](./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png)
    
    "MongoPort"와 같은 규칙 이름을 지정하고 **마침**을 클릭합니다.
    
    ![Windows
    방화벽](./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png)

12. MongoDB가 설치되면 MongoDB에 원격으로 액세스할 수 있도록 끝점을 구성해야 합니다. 관리 포털에서
    **Virtual Machines**를 클릭한 후 새로운 가상 컴퓨터의 이름과 **Endpoints**를 차례로
    클릭합니다.
    
    ![끝점](./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint.png)

13. 페이지 맨 아래에 있는 **Add Endpoint**를 클릭합니다. **Add Endpoint**를 선택하고
    **Next**를 클릭합니다.
    
    ![끝점](./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png)

14. 이름이 "Mongo"이고 프로토콜이 **TCP**이며 **Public** 및 **Private** 포트를 "27017"로
    설정한 끝점을 추가합니다. 이렇게 하면 MongoDB에 원격으로 액세스할 수 있습니다.
    
    ![끝점](./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint3.png)



[1]: http://www.mongodb.org/downloads
[2]: http://www.mongodb.org/display/DOCS/Windows+Service
