
1. 모바일 서비스가 IIS Express에서 실행 중이면 중지해야 합니다. IIS Express 트레이 아이콘을 마우스 오른쪽 단추로 클릭하고 모바일 서비스에 대해 **중지**를 클릭합니다.

    ![](./media/mobile-services-how-to-configure-iis-express/iis-express-tray-stop-site.png)

2. 명령 프롬프트 창에서 **ipconfig** 명령을 실행하여 워크스테이션에 대해 유효한 로컬 IP 주소를 찾습니다.

    ![](./media/mobile-services-how-to-configure-iis-express/ipconfig.png)

3. Visual Studio에서 IIS Express의 applicationhost.config 파일을 엽니다. 이 파일은 사용자 프로필 디렉터리의 다음 하위 디렉터리에 있습니다.

        C:\Users\<your profile name>\Documents\IISExpress\config\applicationhost.config

4. 서비스에 대해 원격 연결 요청을 허용하도록 IIS Express를 구성합니다. 이렇게 하려면 applicationhost.config 파일에서 모바일 서비스의 사이트 요소를 찾아 위에 기록해 둔 IP 주소를 사용하는 포트에 대해 새  `binding` 요소를 추가합니다. 그런 다음 applicationhost.config 파일을 저장합니다. 

    업데이트된 사이트 요소는 다음과 유사합니다.

        <site name="todolist_Service(1)" id="2">
            <application path="/" applicationPool="Clr4IntegratedAppPool">
                <virtualDirectory path="/" physicalPath="C:\Archive\GetStartedDataWP8\C#\todolist_Service" />
            </application>
            <bindings>
                <binding protocol="http" bindingInformation="*:58203:localhost" />
                <binding protocol="http" bindingInformation="*:58203:192.168.137.72" />
            </bindings>
        </site>

5. Windows 방화벽 콘솔을 열고 포트에 대한 연결을 허용하도록 새 포트 규칙을 만듭니다. 새 Windows 방화벽 포트 규칙을 만드는 방법에 대한 자세한 내용은 [새 Windows 방화벽 포트 규칙을 추가하는 방법]을 참조하십시오.

    >[AZURE.NOTE] 테스트 컴퓨터가 도메인에 가입되어 있으면 방화벽 예외가 도메인 정책에 의해 제어될 수 있습니다. 이런 경우 도메인 관리자에게 문의하여 컴퓨터에서 포트에 대한 예외를 얻어야 합니다.

    이제 모바일 서비스를 호스트하는 IIS Express로 테스트하도록 구성되어 있어야 합니다. 

    >[AZURE.NOTE] 서비스를 로컬로 테스트하는 작업을 완료했으면 만들었던 Windows 방화벽 규칙을 삭제해야 합니다. 


<!-- URLs. -->
[새 Windows 방화벽 포트 규칙을 추가하는 방법]:  http://go.microsoft.com/fwlink/?LinkId=392240

<!--HONumber=42-->
