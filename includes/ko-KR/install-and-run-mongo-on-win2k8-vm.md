다음 단계에 따라 Windows Server를 실행하는 가상 컴퓨터에 MongoDB를 설치하고 실행하세요.

> [!IMPORTANT]
> 인증 및 IP 주소 바인딩과 같은 MongoDB 보안 기능은 기본적으로 사용하도록 설정되어 있지 않습니다. MongoDB를 프로덕션 환경에 배포하기 전에 보안 기능을 사용하도록 설정해야 합니다.  자세한 내용은 [보안 및 인증](http://www.mongodb.org/display/DOCS/Security+and+Authentication)을 참조하세요.
>
>

1. 원격 데스크톱을 사용하여 가상 컴퓨터에 연결한 후 가상 컴퓨터의 **시작** 메뉴에서 Internet Explorer를 엽니다.
2. 오른쪽 위에 있는 **도구** 버튼을 선택합니다.  **인터넷 옵션**에서 **보안** 탭을 선택한 후 **신뢰할 수 있는 사이트** 아이콘을 선택하고 마지막으로 **사이트** 단추를 클릭합니다. 신뢰할 수 있는 사이트 목록에 *https://\*.mongodb.org*를 추가합니다.
3. [다운로드 - MongoDB](https://www.mongodb.com/download-center#community)로 이동합니다.
4. **커뮤니티 서버**의 **현재 안정적인 릴리스**를 찾고 Windows 열에서 최신 **64비트** 버전을 선택합니다. MSI 설치 관리자를 다운로드하여 실행합니다.
5. 일반적으로 MongoDB는 C:\Program Files\MongoDB에 설치됩니다. 바탕 화면에서 환경 변수를 검색하고는 PATH 변수에 MongoDB 이진 파일 경로를 추가합니다. 예를 들어 컴퓨터의 C:\Program Files\MongoDB\Server\3.4\bin에서 이진 파일을 찾을 수 있습니다.
6. 앞의 단계에서 만든 데이터 디스크(예: **F:** 드라이브)에서 MongoDB 데이터 및 로그 디렉터리를 만듭니다. **시작**에서 **명령 프롬프트**를 선택하여 명령 프롬프트 창을 엽니다.  형식:

        C:\> F:
        F:\> mkdir \MongoData
        F:\> mkdir \MongoLogs
7. 데이터베이스를 실행하려면 다음을 실행합니다.

        F:\> C:
        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log

    mongod.exe 서버를 시작하고 저널 파일을 사전 할당하면 모든 로그 메시지가 *F:\MongoLogs\mongolog.log* 파일로 보내집니다. MongoDB가 저널 파일을 사전 할당하고 연결이 수신될 때까지 몇 분이 걸릴 수 있습니다. 명령 프롬프트는 MongoDB 인스턴스가 실행되는 동안 이 태스크에 계속 집중합니다.
8. MongoDB 관리 셸을 시작하려면 **시작**에서 명령 창을 하나 더 열어 다음 명령을 입력합니다.

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
9. 또는 mongod.exe를 서비스로 설치할 수 있습니다.

        C:\> mongod --dbpath F:\MongoData\ --logpath F:\MongoLogs\mongolog.log --logappend  --install

    이렇게 하면 "Mongo DB"에 대한 설명이 포함된 MongoDB라는 서비스가 설치됩니다. 실행 중인 서비스는 명령 창에 결과가 표시되지 않으므로 `--logpath` 옵션을 사용하여 로그 파일을 지정해야 합니다.  `--logappend` 옵션은 서비스를 다시 시작할 때 기존 로그 파일에 출력을 추가하도록 지정합니다.  `--dbpath` 옵션은 데이터 디렉터리의 위치를 지정합니다. 서비스 관련 명령줄 옵션에 대한 자세한 내용은 [서비스 관련 명령줄 옵션][MongoWindowsSvcOptions](영문)을 참조하세요.

    서비스를 시작하려면 이 명령을 실행 합니다.

        C:\> net start MongoDB
10. 이제 MongoDB가 설치되고 실행됩니다. 원격으로 MongoDB에 연결하려면 Windows 방화벽에 있는 포트를 열어야 합니다.  **시작** 메뉴에서 **관리 도구**를 선택한 후 **고급 보안이 포함된 Windows 방화벽**을 선택합니다.
11. a) 왼쪽 창에서 **인바운드 규칙**을 선택합니다.  오른쪽에 있는 **작업** 창에서 **새 규칙...**을 선택합니다.

    ![Windows 방화벽][Image1]

    b) **새 인바운드 규칙 마법사**에서 **포트**를 선택한 후 **다음**을 클릭합니다.

    ![Windows 방화벽][Image2]

    c) **TCP**를 선택한 후 **특정 로컬 포트**를 선택합니다.  "27017" 포트(MongoDB의 기본 수신 포트)를 지정하고 **다음**을 클릭합니다.

    ![Windows 방화벽][Image3]

    d) **연결 허용**을 선택하고 **다음**을 클릭합니다.

    ![Windows 방화벽][Image4]

    e) 다시 **다음**을 클릭합니다.

    ![Windows 방화벽][Image5]

    f) "MongoPort"와 같은 규칙 이름을 지정하고 **마침**을 클릭합니다.

    ![Windows 방화벽][Image6]

12. 가상 컴퓨터를 만들 때 MongoDB에 대한 끝점을 구성하지 않은 경우 지금 구성할 수 있습니다. 원격으로 MongoDB에 연결하려면 방화벽 규칙 및 끝점이 있어야 합니다.

  Azure Portal에서 **Virtual Machines(클래식)**를 클릭하고 새 가상 컴퓨터의 이름을 클릭한 다음 **끝점**을 클릭합니다.

    ![끝점][Image7]

13. **추가**를 클릭합니다.

14. 이름이 "Mongo"이고 프로토콜이 **TCP**이며 **공용** 및 **개인** 포트를 "27017"로 설정한 끝점을 추가합니다. 이 포트를 열면 MongoDB에 원격으로 액세스할 수 있습니다.

    ![끝점][Image9]

> [!NOTE]
> 27017 포트가 MongoDB 사용되는 기본 포트입니다. mongod.exe 서버를 시작할 때 `--port` 매개 변수를 지정하여 이 기본 포트를 변경할 수 있습니다. 방화벽에서 동일한 포트 번호 및 앞의 지침에 있는 "Mongo" 끝점을 제공해야 합니다.
>
>

[MongoDownloads]: http://www.mongodb.org/downloads

[MongoWindowsSvcOptions]: http://www.mongodb.org/display/DOCS/Windows+Service


[Image1]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall1.png
[Image2]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall2.png
[Image3]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall3.png
[Image4]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall4.png
[Image5]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall5.png
[Image6]: ./media/install-and-run-mongo-on-win2k8-vm/WinFirewall6.png
[Image7]: ./media/install-and-run-mongo-on-win2k8-vm/menusendpointadd.png
<!-- Removed 03/08/2017. Not in new portal. -->
<!-- [Image8]: ./media/install-and-run-mongo-on-win2k8-vm/WinVmAddEndpoint2.png
-->
[Image9]: ./media/install-and-run-mongo-on-win2k8-vm/newendpointdetails.png
