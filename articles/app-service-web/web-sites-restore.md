<properties 
	pageTitle="Azure 앱 서비스에서 웹 앱 보안 복원" 
	description="백업에서 웹앱을 복원하는 방법에 대해 알아봅니다." 
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
	ms.date="09/16/2015" 
	ms.author="cephalin"/>

# Azure 앱 서비스에서 웹 앱 보안 복원

이 문서에서는 [앱 서비스 웹앱](http://go.microsoft.com/fwlink/?LinkId=529714) 백업 기능을 사용하여 이전에 백업한 웹앱을 복원하는 방법을 보여 줍니다. 자세한 내용은 [앱 서비스 웹앱 백업](web-sites-backup.md)을 참조하세요.

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

웹 앱 복원 기능을 통해 요청 시 웹 앱을 이전 상태로 복원하거나 원본 웹 앱의 백업 중 하나를 기반으로 새 웹 웹을 만들 수 있습니다. 최신 버전과 병행하여 실행되는 웹 앱을 새로 만드는 것이 A/B를 테스트하는 데 유용할 수 있습니다.

[Azure Preview 포털](http://portal.azure.com)의 **백업** 블레이드에서 사용할 수 있는 웹앱 복원 기능은 표준 및 프리미엄 모드에서만 제공됩니다. 표준 및 프리미엄 모드를 사용하는 앱의 크기 조정에 대한 자세한 내용은 [Azure 앱 서비스에서 웹앱 크기 조정](web-sites-scale.md)을 참조하세요. 프리미엄 모드를 사용하면 표준 모드에서 수행되는 것보다 더 많은 매일 백업이 수행됩니다.

<a name="PreviousBackup"></a>
## 이전에 만든 백업에서 웹 앱을 복원하려면

1. Azure 포털에서 웹앱의 **설정** 블레이드에서 **백업** 옵션을 클릭하여 **백업** 블레이드를 표시합니다. 이 블레이드에서 스크롤하여 백업 목록의 **백업 시간** 및 **상태**에 기반하여 백업 항목 중 하나를 선택합니다.
	
	![백업 원본 선택][ChooseBackupSource]
	
2. **백업** 블레이드 위쪽에서 **지금 복원**을 선택합니다.

	![지금 복원 선택][ChooseRestoreNow]

3. **복원** 블레이드에서 기존 웹 앱을 복원하려면 표시된 세부 정보를 모두 확인한 다음 **확인**을 클릭합니다.
	
복원 블레이드에서 **웹앱** 부분을 선택한 다음 **새 웹앱 만들기** 부분을 선택하여 새 웹앱으로 웹앱을 **복원**할 수도 있습니다.
	
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
	
1. 웹앱 복원 작업의 성공 또는 실패에 대한 자세한 내용을 보려면 기본 **찾아보기** 블레이드의 **감사 로그** 부분을 선택합니다. 
	
	**오디오 로그** 블레이드는 단계, 상태, 리소스 및 시간 세부 정보와 함께 작업의 모든 것을 표시합니다.
	
2. 블레이드를 스크롤하여 웹 앱에 관련된 작업을 찾습니다.
3. 작업에 대한 추가 세부 정보를 보려면 목록에서 해당 작업을 선택합니다.
	
세부 정보 블레이드가 해당 작업과 관련된 사용 가능한 정보를 표시합니다.
	
>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 시작 웹앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.
	
## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
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
 

<!---HONumber=Oct15_HO3-->