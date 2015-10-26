Azure 복제본을 호스트하는 각 VM에 대해 부하가 분산된 끝점을 만들어야 합니다. 여러 지역에 복제본이 있는 경우 해당 지역에 대한 각 복제본은 동일한 VNet의 동일한 클라우드 서비스에 있어야 합니다. 여러 Azure 지역에 걸쳐 있는 가용성 그룹 복제본을 만들려면 여러 VNet을 구성해야 합니다. VNet 간 연결 구성에 대한 자세한 내용은 [VNet 간 연결 구성](../articles/vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md)을 참조하세요.

1. Azure 포털에서 복제본을 호스트하는 각 VM으로 이동하고 세부 정보를 봅니다.

1. 각 VM에 대한 **끝점** 탭을 클릭합니다.

1. 사용할 수신기 끝점의 **이름** 및 **공용 포트**가 사용 중이지 않은지 확인합니다. 아래 예제에서는 이름이 "MyEndpoint"이고 포트는 "1433"입니다.

1. 로컬 클라이언트에서 [최신 PowerShell 모듈](http://azure.microsoft.com/downloads/)을 다운로드하고 설치합니다.

1. **Azure PowerShell**을 시작합니다. 새 PowerShell 세션이 Azure 관리 모듈이 로드된 상태로 열립니다.

1. **Get-AzurePublishSettingsFile**을 실행합니다. 이 cmdlet은 게시 설정 파일을 로컬 디렉터리에 다운로드하도록 브라우저로 안내합니다. Azure 구독에 대한 로그인 자격 증명을 묻는 메시지가 표시될 수 있습니다.

1. 다운로드한 게시 설정 파일의 경로와 함께 **Import-AzurePublishSettingsFile** 명령을 사용합니다.

		Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>

	게시 설정 파일을 가져오면 PowerShell 세션에서 Azure 구독을 관리할 수 있습니다.

<!---HONumber=Oct15_HO3-->