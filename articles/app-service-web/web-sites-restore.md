<properties 
	pageTitle="Azure 앱 서비스에서 앱 복원" 
	description="백업에서 앱을 복원하는 방법에 대해 알아봅니다." 
	services="app-service" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="cephalin"/>

# Azure 앱 서비스에서 앱 복원

이 문서에서는 [앱 서비스](app-service-value-prop-what-is) 백업 기능을 사용하여 이전에 백업한 앱 서비스를 복원하는 방법을 보여 줍니다. 자세한 내용은 [앱 서비스 백업](web-sites-backup.md)을 참조하세요.

앱 서비스 복원 기능을 사용하면 요청 시 연결된 데이터베이스(SQL 데이터베이스 또는 MySQL)와 함께 앱을 이전 상태로 복원하거나, 원래 앱의 백업 중 하나를 기반으로 새 앱을 만들 수 있습니다. 최신 버전과 병행하여 실행되는 앱을 새로 만드는 것이 A/B를 테스트하는 데 유용할 수 있습니다.

[Azure 포털](http://portal.azure.com)의 **백업** 블레이드에서 사용할 수 있는 앱 서비스 복원 기능은 표준 및 프리미엄 가격 책정 계층에서만 사용할 수 있습니다. 표준 및 프리미엄 계층을 사용하는 앱의 크기 조정에 대한 자세한 내용은 [Azure 앱 서비스에서 앱 크기 조정](web-sites-scale.md)을 참조하세요. 프리미엄 계층을 사용하면 표준 계층에서 수행되는 것보다 더 많은 매일 백업이 수행됩니다.

<a name="PreviousBackup"></a>
## 이전에 만든 백업에서 앱을 복원하려면

1. Azure 포털에서 앱의 **설정** 블레이드에서 **백업**을 클릭하여 **백업** 블레이드를 표시합니다. 그런 다음 명령 모음에서 **지금 복원**을 클릭합니다. 
	
	![지금 복원 선택][ChooseRestoreNow]

3. **복원** 블레이드에서 먼저 백업 소스를 선택합니다.

	![](./media/web-sites-restore/021ChooseSource.png)
	
	**앱 백업** 옵션에는 앱 자체에서 직접 만든 모든 백업이 표시됩니다. 앱에서는 이러한 백업만 인식하기 때문입니다. 백업을 쉽게 선택할 수 있습니다. **저장소** 옵션에서는 **백업** 블레이드에 구성된 저장소 계정 및 컨테이너에서 실제 백업 ZIP 파일을 선택할 수 있습니다. 컨테이너에 다른 앱의 백업 파일이 있는 경우 이러한 파일도 선택하여 복원할 수 있습니다.

4. 그런 다음 **복원 대상**에서 앱 복원 대상을 지정합니다.

	![](./media/web-sites-restore/022ChooseDestination.png)
	
	>[AZURE.WARNING]**덮어쓰기**를 선택하면 기존 앱과 관련된 모든 데이터가 지워집니다. **확인**을 클릭하기 전에 수행하려는 작업이 정확히 맞는지 확인하세요.
	
	**기존 앱**을 선택하여 같은 리소스 그룹에 있는 다른 앱으로 앱 백업을 복원할 수 있습니다. 이 옵션을 사용하려면 리소스 그룹에 미러링 데이터베이스가 앱 백업에 정의된 것으로 구성되어 있는 다른 앱이 이미 만들어져 있어야 합니다.
	
5. **확인**을 클릭합니다.

<a name="StorageAccount"></a>
## 저장소 계정에서 백업 다운로드 또는 삭제
	
1. Azure 포털의 기본 **찾아보기** 블레이드에서 **저장소 계정**을 선택합니다.
	
	기존 저장소 계정 목록이 표시됩니다.
	
2. 다운로드 또는 삭제하려는 백업을 포함하는 저장소 계정을 선택합니다.
	
	**저장소** 블레이드가 표시됩니다.

3. **저장소** 블레이드에서 **컨테이너** 부분을 선택하여 **컨테이너** 블레이드를 표시합니다.
	
	컨테이너 목록이 표시됩니다. 이 목록은 이 컨테이너가 마지막으로 수정된 날짜 및 URL도 보여줍니다.
	
	![컨테이너 보기][ViewContainers]

4. 목록에서 컨테이너를 선택하고 각 파일의 크기와 함께 파일 이름 목록을 보여주는 블레이드를 표시합니다.
	
5. 파일을 선택하여 파일 **다운로드** 또는 **삭제**를 선택할 수 있습니다. 두 가지 기본 파일 형식(.zip 파일 및 .xml 파일)이 있습니다.

<a name="OperationLogs"></a>
## 감사 로그 보기
	
1. 앱 복원 작업의 성공 또는 실패에 대한 자세한 내용을 보려면 기본 **찾아보기** 블레이드의 **감사 로그** 부분을 선택합니다. 
	
	**오디오 로그** 블레이드는 단계, 상태, 리소스 및 시간 세부 정보와 함께 작업의 모든 것을 표시합니다.
	
2. 블레이드를 스크롤하여 앱에 관련된 작업을 찾습니다.
3. 작업에 대한 추가 세부 정보를 보려면 목록에서 해당 작업을 선택합니다.
	
세부 정보 블레이드가 해당 작업과 관련된 사용 가능한 정보를 표시합니다.
	
>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

## 다음 단계

또한 REST API를 사용하여 앱 서비스 앱을 백업 및 복원할 수 있습니다([REST를 사용하여 앱 서비스 앱 백업 및 복원](websites-csm-backup.md) 참조).

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png
 

<!---HONumber=AcomDC_0114_2016-->