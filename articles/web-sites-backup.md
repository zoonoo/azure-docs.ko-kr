<properties 
	pageTitle="Azure 웹 사이트 백업" 
	description="Azure 웹 사이트의 백업을 만드는 방법에 대해 알아봅니다." 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="9/19/2014" 
	ms.author="cephalin"/>

#Azure 웹 사이트 백업

Azure 웹 사이트 백업 및 복원 기능을 통해 웹 사이트 백업을 수동이나 자동으로 쉽게 만들 수 있습니다. 웹 사이트를 이전 상태로 복원하거나 원본 사이트 백업 중 하나를 기반으로 새 웹 사이트를 만들 수 있습니다. 


백업에서 Azure 웹 사이트 복원에 대한 자세한 내용은 [Azure 웹 사이트 복원](http://azure.microsoft.com/documentation/articles/web-sites-restore/)을 참조하세요.

##이 문서의 내용

- [간편한 자동 백업(비디오)](#video)
- [백업 대상](#whatsbackedup)
- [요구 사항 및 제한 사항](#requirements)
- [수동 백업을 만들려면](#manualbackup)
- [자동 백업을 구성하려면](#automatedbackups)
- [백업 저장 방법](#aboutbackups)
- [참고](#notes)
- [다음 단계](#nextsteps)
	- [저장소 계정에 대한 자세한 정보](#moreaboutstorage)

<a name="video"></a>
##간편한 자동 백업(비디오)

이 비디오에서는 Eduardo Laureano와 Scott Hanselman이 Azure 웹 사이트 백업을 소개합니다. (소요 시간: 11분 43초)  

> [AZURE.VIDEO azure-websites-automatic-and-easy-backup]

<a name="whatsbackedup"></a>
##백업 대상 
Azure 웹 사이트에서는 다음 정보를 백업합니다.

* 웹 사이트 구성
* 웹 사이트 파일 내용
* 사이트에 연결된 SQL 서버 또는 MySQL 데이터베이스(백업에 포함할 대상을 선택할 수 있음)

이 정보는 지정하는 Azure 저장소 계정에 백업됩니다. 

> [AZURE.NOTE] 각 백업은 증분 업데이트가 아니라 웹 사이트의 완전한 오프라인 복사본입니다.

<a name="requirements"></a>
##요구 사항 및 제한 사항

* 백업 및 복원 기능은 사이트가 표준 계층에 있어야 합니다. 표준 계층을 사용하는 웹 사이트의 크기 조정에 대한 자세한 내용은 [웹 사이트 크기를 조정하는 방법](http://azure.microsoft.com/documentation/articles/web-sites-scale/)을 참조하세요. 

* 백업 및 복원 기능에는 백업할 웹 사이트와 동일한 구독에 속해야 하는 Azure 저장소 계정이 있어야 합니다. 아직 저장소 계정이 없는 경우에는 Azure 포털의 왼쪽 창에서 **저장소** 단추(표 아이콘)를 클릭한 후 아래의 명령 모음에서 **새로 만들기**를 선택하여 저장소 계정을 만들 수 있습니다. Azure 저장소 계정에 대한 자세한 내용은 이 문서의 끝 부분에 있는 [링크]를(#moreaboutstorage) 참조하세요.

<a name="manualbackup"></a>
## 수동 백업을 만들려면

1. 웹 사이트의 Azure 포털에서 **백업** 탭을 선택합니다.
	
	![백업 페이지][ChooseBackupsPage]
	
2. 웹 사이트를 백업할 저장소 계정을 선택합니다. 저장소 계정은 백업하려는 웹 사이트와 동일한 구독에 속해야 합니다.
	
	![저장소 계정 선택][ChooseStorageAccount]
	
3. **포함된 데이터베이스** 옵션에서 백업할 웹 사이트에 연결할 데이터베이스(SQL 서버 또는 MySQL)를 선택합니다. 
	
	![포함할 데이터베이스 선택][IncludedDatabases]

	> [AZURE.NOTE] 	이 목록에 표시될 데이터베이스의 경우 연결 문자열이 포털에서 구성 탭의 **연결 문자열** 섹션에 있어야 합니다.
	
4. 명령 모음에서 **지금 백업**을 클릭합니다.
	
	![지금 백업 단추][BackUpNow]
	
	백업 프로세스를 수행하는 동안 진행 메시지가 나타납니다.
	
	![백업 진행 메시지][BackupProgress]
	
언제든지 수동 백업을 만들 수 있습니다. 미리 보는 동안 24시간 안에 2개 이하의 수동 백업을 만들 수 있습니다(변경 예정).  

<a name="automatedbackups"></a>
## 자동 백업을 구성하려면

1. 백업 페이지에서 **자동 백업**을 ON으로 설정합니다.
	
	![자동 백업 사용][SetAutomatedBackupOn]
	
2. 웹 사이트를 백업할 저장소 계정을 선택합니다. 저장소 계정은 백업하려는 웹 사이트와 동일한 구독에 속해야 합니다.
	
	![저장소 계정 선택][ChooseStorageAccount]
	
3. **주기** 상자에서 자동 백업할 주기를 지정합니다. (미리 보는 동안 일수만 사용 가능한 시간 단위입니다.)
	
	![백업 주기 선택][Frequency]
	
	일수는 1 이상 90 이하여야 합니다(하루에 한 번~90일에 한 번).
	
4. **시작 날짜** 옵션을 사용하여 자동 백업을 시작할 날짜와 시간을 지정합니다. 
	
	![시작 날짜 선택][StartDate]
	
	시간은 30분씩 늘릴 수 있습니다.
	
	![시작 시간 선택][StartTime]
	
	> [AZURE.NOTE] Azure는 백업 시간을 UTC 형식으로 저장하지만 포털을 표시하는 데 사용하는 컴퓨터의 시스템 시간에 맞게 표시합니다.
	
5. **포함된 데이터베이스** 섹션에서 백업할 웹 사이트에 연결할 데이터베이스(SQL 서버 또는 MySQL)를 선택합니다. 이 목록에 표시될 데이터베이스의 경우 연결 문자열이 포털에서 구성 탭의 **연결 문자열** 섹션에 있어야 합니다.
	
	![포함할 데이터베이스 선택][IncludedDatabases]
	
	> [AZURE.NOTE] 백업 시 하나 이상의 데이터베이스를 포함하도록 선택하고 주기를 7일 미만으로 지정하면 잦은 백업이 데이터베이스 비용을 증가시킬 수 있다는 경고 메시지를 받습니다.
	
6. 명령 모음에서 **저장** 단추를 클릭하여 구성 변경을 저장합니다(또는 저장하지 않는 경우 **취소** 선택).
	
	![저장 단추][SaveIcon]

<a name="aboutbackups"></a>
## 백업 저장 방법

만들어진 하나 이상의 백업은 저장소 계정의 컨테이너 탭에서 볼 수 있습니다. 백업은 **websitebackups**라는 컨테이너에 있습니다. 각 백업은 백업 데이터가 포함된 .zip 파일과 그 .zip 파일 내용의 매니페스트가 포함된 .xml 파일로 구성되어 있습니다. 

.zip 및 .xml 백업 파일 이름은 웹 사이트 이름 다음에 밑줄과 백업한 타임스탬프로 구성됩니다. 타임스탬프에는 YYYYMMDD 형식(공백 없이 숫자로)의 날짜와 UTC 형식의 24시간제(예: fabrikam_201402152300.zip)가 포함되어 있습니다. 실제로 웹 사이트 복원을 수행하지 않고 백업에 액세스하려는 경우 이러한 파일 내용의 압축을 풀고 검색할 수 있습니다.

zip 파일로 저장되는 XML 파일은 *backupdescription* > *databases* > *databasebackupdescription* > *filename* 아래의 데이터베이스 파일 이름을 나타냅니다.

데이터베이스 백업 파일 자체는 .zip 파일의 루트에 저장됩니다. SQL 데이터베이스의 경우 이 파일은 BACPAC 파일(파일 확장명 없음)이며, 가져올 수 있습니다. BACPAC 내보내기를 기반으로 새 SQL 데이터베이스를 만들려면 [BACPAC 파일을 가져와 새 사용자 데이터베이스 만들기](http://technet.microsoft.com/library/hh710052.aspx) 문서의 단계를 따르면 됩니다.

Azure 관리 포털을 사용하여 Azure 웹 사이트(데이터베이스 포함)를 복원하는 방법에 대한 자세한 내용은 [Microsoft Azure 웹 사이트 복원]( http://azure.microsoft.com/documentation/articles/web-sites-restore/)을 참조하세요.

> [AZURE.NOTE] **websitebackups** 컨테이너에 있는 파일을 변경하면 백업이 잘못되어 복원할 수 없게 됩니다.

<a name="notes"></a>
## 참고

* 백업 및 복원 기능이 데이터베이스를 포함할 수 있도록 웹 사이트의 구성 탭에서 각 데이터베이스의 연결 문자열을 제대로 설정했는지 확인하세요.
* 미리 보는 동안 저장소 계정에 저장한 백업 내용을 책임지고 관리합니다. 저장소 계정에서 백업을 삭제하고 다른 곳에 복사본을 만들지 않은 경우 나중에 백업을 복원할 수 없습니다. 
* 동일한 저장소 계정에 둘 이상의 웹 사이트를 백업할 수 있을지라도 유지 관리하기 쉽도록 각 웹 사이트에 대해 별도의 저장소 계정을 만드는 것이 좋습니다.
* 미리 보는 동안 백업 및 복원 작업은 Azure 관리 포털을 통해서만 사용할 수 있습니다.

<a name="nextsteps"></a>
## 다음 단계
백업에서 Azure 웹 사이트 복원에 대한 자세한 내용은 [Azure 웹 사이트 복원](http://azure.microsoft.com/documentation/articles/web-sites-restore/)을 참조하세요.

Azure에 등록하려면 [Microsoft Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/)을 참조하세요.


<a name="moreaboutstorage"></a>
### 저장소 계정에 대한 자세한 정보

[저장소 계정 정의](http://azure.microsoft.com/documentation/articles/storage-whatis-account/)

[방법: 저장소 계정 만들기](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/)

[저장소 계정을 모니터링하는 방법](http://azure.microsoft.com/documentation/articles/storage-monitor-storage-account/)

[Azure 저장소 대금 청구 이해](http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

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


<!--HONumber=42-->
