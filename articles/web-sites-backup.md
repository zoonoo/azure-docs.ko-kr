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
	ms.date="03/24/2015" 
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

* 백업 및 복원 기능에는 백업할 웹 앱과 동일한 구독에 속해야 하는 Azure 저장소 계정과 컨테이너가 있어야 합니다. 아직 저장소 계정이 없는 경우, [Azure 포털](http://go.microsoft.com/fwlink/?LinkId=529715)의 **백업** 블레이드에서 **저장소 계정**을 클릭한 다음 **저장소 계정** 및 **컨테이너**를 **대상** 블레이드에서 선택하여 계정을 하나 만들 수 있습니다. Azure 저장소 계정에 대한 자세한 내용은 이 문서의 끝에 있는 [링크](#moreaboutstorage)를 참조하십시오.

* 백업 및 복원 기능은 최대 10GB 웹 사이트 및 데이터베이스 콘텐츠를 지원합니다. 페이로드가 이 제한을 초과하기 때문에 백업 기능을 계속할 수 없는 경우, 작업 로그에 오류가 표시됩니다.

<a name="manualbackup"></a>
## 수동 백업 만들기

1. Azure 포털의 웹 앱 블레이드에서 웹 앱을 선택 합니다. 이렇게 하면 웹 앱의 세부 정보에서 새 블레이드가 표시 됩니다.
2. **설정** 옵션을 선택합니다. **설정** 블레이드는 웹 앱을 수정할 수 있도록 표시됩니다.
	
	![백업 페이지][ChooseBackupsPage]

3. **설정** 블레이드에서 **백업** 옵션을 선택합니다.  **백업** 블레이드가 표시됩니다.
	
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
	
3. **주기** 상자에서 자동 백업할 주기를 지정합니다. 일수는 1 이상 90 이하여야 합니다(하루에 한 번\~90일에 한 번).
	
4. **시작** 옵션을 사용하여 자동화된 백업을 시작할 날짜와 시간을 지정합니다.
	
	> [AZURE.NOTE]Azure는 백업 시간을 UTC 형식으로 저장하지만 포털을 표시하는 데 사용하는 컴퓨터의 시스템 시간에 맞게 표시합니다.
	
5. **포함된 데이터베이스** 섹션에서 백업할 웹 앱에 연결할 데이터베이스(SQL 서버 또는 MySQL)를 선택합니다. 이 목록에 표시될 데이터베이스의 경우 연결 문자열은 포털의 **웹 앱 설정** 블레이드에서 **연결 문자열** 섹션에 있어야 합니다.
	
	> [AZURE.NOTE]백업 시 하나 이상의 데이터베이스를 포함하도록 선택하고 주기를 7일 미만으로 지정하면 잦은 백업이 데이터베이스 비용을 증가시킬 수 있다는 경고 메시지를 받습니다.
	
6. 또한 **보존(일)** 값을 백업을 보존할 기간(일)로 설정합니다.
7. 명령 모음에서 **저장** 단추를 클릭하여 구성 변경을 저장합니다(또는 저장하지 않는 경우 **취소** 선택).
	
	![저장 단추][SaveIcon]

<a name="aboutbackups"></a>
## 백업 저장 방법

하나 이상의 백업을 수행한 후, 백업은 웹 앱뿐만 아니라 **저장소 계정**의 **컨테이너** 블레이드에서 볼 수 있습니다. **저장소 계정**에서, 각 백업은 백업 데이터가 포함된 .zip 파일과 그 .zip 파일 콘텐츠의 매니페스트가 포함된 .xml 파일로 구성되어 있습니다.

.zip 및 .xml 백업 파일 이름은 웹 앱 이름 다음에 밑줄과 백업한 타임스탬프로 구성됩니다. 타임스탬프에는 YYYYMMDD 형식(공백 없이 숫자로)의 날짜와 UTC 형식의 24시간제(예: fabrikam_201402152300.zip)가 포함되어 있습니다. 실제로 웹 사이트 복원을 수행하지 않고 백업에 액세스하려는 경우 이러한 파일 콘텐츠의 압축을 풀고 검색할 수 있습니다.

zip 파일로 저장되는 XML 파일은 *backupdescription* > *databases* > *databasebackupdescription* > *filename* 아래의 데이터베이스 파일 이름을 나타냅니다.

데이터베이스 백업 파일 자체는 .zip 파일의 루트에 저장됩니다. SQL 데이터베이스의 경우 이 파일은 BACPAC 파일(파일 확장명 없음)이며, 가져올 수 있습니다. BACPAC 내보내기를 기반으로 새 SQL 데이터베이스를 만들려면 [BACPAC 파일을 가져와 새 사용자 데이터베이스 만들기](http://technet.microsoft.com/library/hh710052.aspx) 문서의 단계를 따르면 됩니다.

Azure 포털을 사용한 웹 앱(데이터베이스 포함) 복원에 대한 자세한 내용은 [Azure 앱 서비스에서 앱 웹 복원](web-sites-restore.md)을 참조하세요.

> [AZURE.NOTE]**websitebackups** 컨테이너에 있는 파일을 변경하면 백업이 잘못되어 복원할 수 없게 됩니다.

<a name="notes"></a>
## 참고 사항

* 백업 및 복원 기능에 데이터베이스를 포함할 수 있도록 웹 앱의 **설정** 내 **웹 앱 설정** 블레이드에서 각 데이터베이스의 연결 문자열이 제대로 설정되어 있는지 확인합니다.
* 동일한 저장소 계정에 둘 이상의 웹 앱을 백업할 수 있더라도 유지 관리하기 쉽도록 각 웹 앱에 대해 별도의 저장소 계정을 만드는 것이 좋습니다.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 앱 서비스를 시작하려면 [앱 서비스 평가](http://go.microsoft.com/fwlink/?LinkId=523751)로 이동합니다. 앱 서비스에서 단기 스타터 웹 앱을 즉시 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

<a name="nextsteps"></a>
## 다음 단계
Azure 웹 앱을 백업에서 복원하는 방법에 대한 자세한 내용은 [Azure 앱 서비스에서 웹 복원](web-sites-restore.md)을 참조하세요.

Azure에 등록하려면 [Microsoft Azure 무료 평가판](/pricing/free-trial/)을 참조하세요.


<a name="moreaboutstorage"></a>
### 저장소 계정에 대한 자세한 정보

[저장소 계정 정의](storage-whatis-account.md)

[방법: 저장소 계정 만들기](../storage-create-storage-account/)

[저장소 계정을 모니터링하는 방법](storage-monitor-storage-account.md)

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

<!--HONumber=54-->