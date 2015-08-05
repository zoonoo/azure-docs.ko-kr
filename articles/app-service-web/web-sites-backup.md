<properties 
	pageTitle="Azure 앱 서비스에서 웹 앱을 백업" 
	description="Azure 앱 서비스에서 웹 앱의 백업을 만드는 방법에 대해 알아봅니다" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/03/2015" 
	ms.author="cephalin"/>

# Azure 앱 서비스에서 웹 앱을 백업


[Azure 앱 서비스 웹 앱](http://go.microsoft.com/fwlink/?LinkId=529714)의 백업 및 복원 기능을 사용하여 수동 또는 자동으로 웹 앱 백업을 쉽게 만들 수 있습니다. 웹 앱을 이전 상태로 복원하거나 원본 앱의 백업 중 하나를 기반으로 새 웹 앱을 만들 수 있습니다.

Azure 웹 앱을 백업에서 복원하는 방법에 대한 자세한 내용은 [웹 앱 복원](web-sites-restore.md)을 참조하세요.

<a name="whatsbackedup"></a>
## 백업 대상 
웹 앱은 다음 정보를 백업할 수 있습니다.

* 웹 앱 구성
* 웹 앱 파일 콘텐츠
* 앱에 연결된 SQL 서버 또는 MySQL 데이터베이스(백업에 포함할 대상을 선택할 수 있음)

이 정보는 지정한 Azure 저장소 계정 및 컨테이너로 백업됩니다.

> [AZURE.NOTE]각 백업은 증분 업데이트가 아니라 앱의 완전한 오프라인 복사본입니다.

<a name="requirements"></a>
## 요구 사항 및 제한 사항

* 백업 및 복원 기능은 사이트가 표준 모드에 있어야 합니다. 표준 모드를 사용하여 웹 앱을 확장하는 방법에 대한 자세한 내용은 [Azure 앱 서비스에서 웹 앱 확장](web-sites-scale.md)을 참조하세요. 프리미엄 모드에서는 표준 모드를 통해 매일 더 많은 수의 백업을 수행할 수 있습니다.

* 백업 및 복원 기능에는 백업할 웹 앱과 동일한 구독에 속해야 하는 Azure 저장소 계정과 컨테이너가 있어야 합니다. 아직 저장소 계정이 없는 경우, [Azure Preview 포털](http://portal.azure.com)의 **백업** 블레이드에서 **저장소 계정**을 클릭한 다음 **저장소 계정** 및 **컨테이너**를 **대상** 블레이드에서 선택하여 계정을 하나 만들 수 있습니다. Azure 저장소 계정에 대한 자세한 내용은 이 문서의 끝에 있는 [링크](#moreaboutstorage)를 참조하십시오.

* 백업 및 복원 기능은 최대 10GB 웹 사이트 및 데이터베이스 콘텐츠를 지원합니다. 페이로드가 이 제한을 초과하기 때문에 백업 기능을 계속할 수 없는 경우, 작업 로그에 오류가 표시됩니다.

<a name="manualbackup"></a>
## 수동 백업 만들기

1. Azure 포털의 웹 앱 블레이드에서 웹 앱을 선택 합니다. 이렇게 하면 웹 앱의 세부 정보에서 새 블레이드가 표시 됩니다.
2. **설정** 옵션을 선택합니다. **설정** 블레이드는 웹 앱을 수정할 수 있도록 표시됩니다.
	
	![백업 페이지][ChooseBackupsPage]

3. **설정** 블레이드에서 **백업** 옵션을 선택합니다. **백업** 블레이드가 표시됩니다.
	
4. **백업** 블레이드에서, **저장소 계정** 및 **컨테이너**를 선택하여 백업 대상을 선택합니다. 저장소 계정은 백업할 웹 앱과 동일한 구독에 속해야 합니다.
	
	![저장소 계정 선택][ChooseStorageAccount]
	
5. **백업 블레이드**의 **포함된 데이터베이스** 옵션에서 백업하려는 웹 앱(SQL Server 또는 MySQL)에 연결된 데이터베이스를 선택합니다.

	> [AZURE.NOTE]이 목록에 표시될 데이터베이스의 경우 연결 문자열은 포털의 **웹 앱 설정** 블레이드에서 **연결 문자열** 섹션에 있어야 합니다.
	
6. **백업** 블레이드에서 **백업 대상**을 선택합니다. 기존 저장소 계정과 컨테이너를 선택해야 합니다.
7. 명령 모음에서 **지금 백업**을 클릭합니다.
	
	![BackUpNow 단추][BackUpNow]
	
	백업 프로세스를 수행하는 동안 진행 메시지가 나타납니다.
	

언제든지 수동 백업을 만들 수 있습니다.

<a name="automatedbackups"></a>
## 자동화된 백업 구성

1. **백업 블레이드**에서 **예약된 백업**을 ON으로 설정합니다.
	
	![자동 백업 사용][SetAutomatedBackupOn]
	
2. 웹 앱을 백업할 저장소 계정을 선택합니다. 저장소 계정은 백업할 웹 앱과 동일한 구독에 속해야 합니다.
	
	![저장소 계정 선택][ChooseStorageAccount]
	
3. **주기** 상자에서 자동 백업할 주기를 지정합니다. 일수는 1 이상 90 이하여야 합니다(하루에 한 번~90일에 한 번).
	
4. **시작** 옵션을 사용하여 자동화된 백업을 시작할 날짜와 시간을 지정합니다.
	
	> [AZURE.NOTE]Azure는 백업 시간을 UTC 형식으로 저장하지만 포털을 표시하는 데 사용하는 컴퓨터의 시스템 시간에 맞게 표시합니다.
	
5. **포함된 데이터베이스** 섹션에서 백업할 웹 앱에 연결할 데이터베이스(SQL 서버 또는 MySQL)를 선택합니다. 이 목록에 표시될 데이터베이스의 경우 연결 문자열은 포털의 **웹 앱 설정** 블레이드에서 **연결 문자열** 섹션에 있어야 합니다.
	
	> [AZURE.NOTE]백업 시 하나 이상의 데이터베이스를 포함하도록 선택하고 주기를 7일 미만으로 지정하면 잦은 백업이 데이터베이스 비용을 증가시킬 수 있다는 경고 메시지를 받습니다.
	
6. 또한 **보존(일)** 값을 백업을 보존할 기간(일)로 설정합니다.
7. 명령 모음에서 **저장** 단추를 클릭하여 구성 변경을 저장합니다(또는 저장하지 않는 경우 **취소** 선택).
	
	![저장 단추][SaveIcon]

<a name="notes"></a>
## 참고 사항

* 백업 및 복원 기능에 데이터베이스를 포함할 수 있도록 웹 앱의 **설정** 내 **웹 앱 설정** 블레이드에서 각 데이터베이스의 연결 문자열이 제대로 설정되어 있는지 확인합니다.
* 동일한 저장소 계정에 둘 이상의 웹 앱을 백업할 수 있더라도 유지 관리하기 쉽도록 각 웹 앱에 대해 별도의 저장소 계정을 만드는 것이 좋습니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

<a name="partialbackups"></a>
## 웹앱의 일부만 백업

웹앱의 모든 것을 백업하고 싶지 않을 때도 있습니다. 다음은 몇 가지 예입니다.

-	오래된 블로그 게시물이나 이미지처럼 변하지 않는 정적인 콘텐츠가 포함된 웹앱을 [매주 백업 설정](web-sites-backup.md#configure-automated-backups)합니다.
-	웹앱에는 10GB 이상의 콘텐츠가 있습니다(한 번에 백업할 수 있는 최대 용량).
-	로그 파일은 백업하지 않아도 됩니다.

부분 백업을 사용하여 백업할 파일을 정확히 선택할 수 있습니다.

### 백업에서 파일 제외

백업에서 파일 및 폴더를 제외하려면 웹앱의 wwwroot 폴더에 `_backup.filter` 파일을 만들고 제외하려는 파일 및 폴더 목록을 지정합니다. 이 곳은 [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console)을 통해 쉽게 액세스할 수 있습니다.

웹앱에 지난 몇 년 동안의 로그 파일과 정적 이미지가 포함되어 있다고 가정해 보겠습니다. 이러한 항목들은 앞으로도 변하지 않습니다. 여러분은 오래된 이미지가 포함된 웹앱을 이미 완벽히 백업해두었습니다. 이제 매일 웹앱을 백업하려고 하지만 변경되지 않는 정적 이미지 파일 또는 로그 파일을 저장하는 비용은 지불하고 싶지 않습니다.

![Logs 폴더][LogsFolder]	
![Images 폴더][ImagesFolder]
	
다음 단계는 백업에서 이들 파일을 제외하는 방법을 보여줍니다.

1. `http://{yourapp}.scm.azurewebsites.net/DebugConsole`(으)로 이동하여 백업에서 제외하고자 하는 폴더를 찾습니다. 이 예제에서는 해당 UI에 표시된 다음 파일 및 폴더를 제외하고자 합니다.

		D:\home\site\wwwroot\Logs
		D:\home\LogFiles
		D:\home\site\wwwroot\Images\2013
		D:\home\site\wwwroot\Images\2014
		D:\home\site\wwwroot\Images\brand.png

	[AZURE.NOTE]마지막 줄은 개별 파일 및 폴더를 제외할 수 있음을 보여줍니다.

2. `_backup.filter`(이)라고 이름의 파일을 만들어서 이 파일에 앞서 언급한 목록을 넣고 `D:\home`을(를) 제거합니다. 줄당 하나의 디렉터리 또는 파일을 나열하십시오. 파일의 내용은 다음과 같아야 합니다.

    \site\wwwroot\Logs \LogFiles \site\wwwroot\Images\2013 \site\wwwroot\Images\2014 \site\wwwroot\Images\brand.png

3. [ftp](web-sites-deploy.md#ftp) 또는 기타 모든 방법을 사용하여 이 파일을 해당 사이트의 `D:\home\site\wwwroot` 디렉터리에 업로드합니다. 원한다면 `http://{yourapp}.scm.azurewebsites.net/DebugConsole`에 파일을 직접 만들어서 여기에 콘텐츠를 삽입할 수도 있습니다.

4. 이제 백업을 평소와 같이 [수동](#create-a-manual-backup) 또는 [자동](#configure-automated-backups)으로 실행합니다.

이제 `_backup.filter`에 지정된 모든 파일 및 폴더가 백업에서 제외됩니다. 이 예제에서는 로그 파일과 2013 및 2014 이미지 파일은 이제 더 이상 백업되지 않습니다. brand.png로 마찬가지입니다.

>[AZURE.NOTE][정기 백업을 복원](web-sites-restore.md)할 때와 동일한 방법으로 사이트의 부분 백업을 복원합니다. 복원 프로세스는 올바른 일을 수행합니다.
>
>전체 백업이 복원되면 해당 사이트의 모든 콘텐츠가 백업에 있는 항목들로 대체됩니다. 파일이 사이트에 있지만 백업에 없는 경우 해당 파일은 삭제됩니다. 하지만 부분 백업이 복원되면 블랙 리스트 디렉터리에 위치한 모든 콘텐츠 또는 블랙 리스트에 포함된 모든 파일이 그대로 유지됩니다.

<a name="aboutbackups"></a>

## 백업 저장 방법

웹앱에 대해 하나 이상의 백업을 수행한 후, 백업은 웹앱뿐만 아니라 저장소 계정의 **컨테이너** 블레이드에서 볼 수 있습니다. 저장소 계정에서, 각 백업은 백업 데이터가 포함된 .zip 파일과 그 .zip 파일 콘텐츠의 매니페스트가 포함된 .xml 파일로 구성되어 있습니다. 실제로 웹앱 복원을 수행하지 않고 백업에 액세스하고자 한다면 이들 파일의 압축을 풀고 찾아볼 수 있습니다.

웹앱에 대한 데이터베이스 백업이 .zip 파일의 루트에 저장됩니다. SQL 데이터베이스의 경우 이 파일은 BACPAC 파일(파일 확장명 없음)이며, 가져올 수 있습니다. BACPAC 내보내기를 기반으로 새 SQL 데이터베이스를 만들려면 [새 사용자 데이터베이스 생성을 위해 BACPAC 파일 가져오기](http://technet.microsoft.com/library/hh710052.aspx)를 참조하세요.

> [AZURE.WARNING]**websitebackups** 컨테이너에 있는 파일을 변경하면 백업이 잘못되어 복원할 수 없게 됩니다.

<a name="bestpractices"></a>
##모범 사례

장애나 자연 재해에 대비하려면 기존 백업 및 복원 전략을 세워서 미리 준비하는 것이 좋습니다.

백업 전략은 다음과 유사해야 합니다.

-	최소 한 번의 웹앱 전체 백업을 수행합니다.
-	전체 백업 후 웹앱의 부분 백업을 수행합니다.

복원 전략은 다음과 유사해야 합니다.
 
-	웹앱에 대해 [스테이징 슬롯](web-sites-staged-publishing.md)을 생성합니다.
-	스테이징 슬롯에서 웹앱의 전체 백업을 복원합니다.
-	스테이징 슬롯에서도 전체 백업 복원 위에 최신 부분 백업을 복원합니다.
-	복원을 테스트하여 스테이징 앱이 올바르게 동작하는지 확인하십시오.
-	스테이징된 웹앱을 프로덕션 슬롯에 [스왑](web-sites-staged-publishing.md#Swap)합니다.

>[AZURE.NOTE]항상 복원 프로세스를 테스트합니다. 자세한 내용은 [매우 유용한 기능](http://axcient.com/blog/one-thing-can-derail-disaster-recovery-plan/)을 참조하세요. 예를 들어, [Ghost](https://ghost.org/)와 같은 특정 블로깅 플랫폼에서는 백업 중에 어떻게 동작하는지에 대해 명시적인 지침을 가지고 있습니다. 복원 프로세스를 테스트함으로써 장애나 재해에 의해 피해를 입기 전에 이러한 지침을 적용할 수 있습니다.

<a name="nextsteps"></a>
## 다음 단계
웹앱을 백업에서 복원하는 방법에 대한 자세한 내용은 [Azure 앱 서비스에서 웹 복원](web-sites-restore.md)을 참조하세요.

Azure에 등록하려면 [Microsoft Azure 무료 평가판](/pricing/free-trial/)을 참조하세요.


<a name="moreaboutstorage"></a>
### 저장소 계정에 대한 자세한 정보

[저장소 계정 정의](../storage-whatis-account.md)

[방법: 저장소 계정 만들기](../storage-create-storage-account/)

[저장소 계정을 모니터링하는 방법](../storage-monitor-storage-account.md)

[Azure 저장소 청구 이해](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

## 변경된 내용
* 웹 사이트에서 앱 서비스로의 변경에 대한 지침은 [Azure 앱 서비스와 이 서비스가 기존 Azure 서비스에 미치는 영향](http://go.microsoft.com/fwlink/?LinkId=529714)을 참조하세요.
* 이전 포털에서 새 포털로의 변경에 대한 지침은 [미리 보기 포털 탐색에 대한 참조](http://go.microsoft.com/fwlink/?LinkId=529715)를 참조하세요.

<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount.png
[IncludedDatabases]: ./media/web-sites-backup/03IncludedDatabases.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow.png
[BackupProgress]: ./media/web-sites-backup/05BackupProgress.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn.png
[Frequency]: ./media/web-sites-backup/07Frequency.png
[StartDate]: ./media/web-sites-backup/08StartDate.png
[StartTime]: ./media/web-sites-backup/09StartTime.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
 

<!----HONumber=July15_HO4-->