이 단계에서는 동일한 네트워크에서 실행 중인 클라이언트 응용 프로그램을 사용하여 가용성 그룹 수신기를 테스트합니다.

클라이언트 연결의 경우 다음 요구 사항을 확인하세요.

- 수신기에 대한 클라이언트 연결은 AlwaysOn 가용성 복제본을 호스트하는 클라우드 서비스가 아닌 다른 클라우드 서비스에 있는 컴퓨터에서 발생해야 합니다.

- AlwaysOn 복제본이 서로 다른 서브넷에 있는 경우 클라이언트는 연결 문자열에 "MultisubnetFailover=True"를 지정해야 합니다. 이렇게 하면 서로 다른 서브넷에 있는 복제본에 대한 연결 시도가 병렬로 실행됩니다. 이 시나리오에는 지역 간 AlwaysOn 가용성 그룹 배포가 포함됩니다.

예를 들어 동일한 Azure VNet의 VM(단, 복제본은 호스트하지 않음) 중 하나에서 수신기에 연결합니다. 이 테스트를 완료하는 쉬운 방법은 가용성 그룹 수신기에 SSMS를 연결해 보는 것입니다. 다른 간단한 방법은 다음과 같이 [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx)를 실행하는 것입니다.

	sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [AZURE.NOTE]EndpointPort 값이 1433이면 호출에서 이를 지정하지 않아도 됩니다. 또한 이전 호출에서는 클라이언트 컴퓨터가 동일한 도메인에 가입되어 있고 Windows 인증을 사용하여 호출자에게 데이터베이스에 대한 권한이 부여되었다고 가정합니다.

수신기를 테스트할 때 클라이언트가 장애 조치(failover)에서 수신기에 연결할 수 있도록 하기 위해 가용성 그룹을 장애 조치(failover)해야 합니다.

<!---HONumber=Oct15_HO3-->