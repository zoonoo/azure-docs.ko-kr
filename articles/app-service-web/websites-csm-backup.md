<properties
	pageTitle="REST를 사용하여 앱 서비스 앱 백업 및 복원"
	description="Azure 앱 서비스에서 RESTful API 호출을 사용하여 앱을 백업하고 복원하는 방법 알아보기"
	services="app-service"
	documentationCenter=""
	authors="nking92"
	manager="edlauare"
    editor="" />

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="nicking"/>

# REST를 사용하여 앱 서비스 앱 백업 및 복원
[Azure 서비스 앱](https://azure.microsoft.com/services/app-service/web/)을 Azure 저장소에 blob로 백업할 수 있습니다. 또한 백업에 앱의 데이터베이스를 포함할 수 있습니다. 앱이 실수로 삭제되거나 앱을 이전 버전으로 되돌려야 할 경우 이전 백업으로 복원할 수 있습니다. 필요할 때 언제든지 백업할 수 있으며, 적당한 간격으로 백업을 예약할 수도 있습니다.

이 문서에서는 RESTful API 요청을 사용하여 앱을 백업 및 복원하는 방법을 설명합니다. Azure 포털을 통해 그래픽 방식으로 앱 백업을 만들고 관리하려면 [Azure 앱 서비스에서 웹앱 백업](web-sites-backup.md)을 참조하세요.

<a name="gettingstarted"></a>
## 시작하기
REST 요청을 보내려면 앱의 **이름**, **리소스 그룹** 및 **구독 id**를 알아야 합니다. 이 정보는 [Azure 포털](https://portal.azure.com)의 **앱 서비스** 블레이드에서 앱을 클릭하여 확인할 수 있습니다. 이 문서의 예에서는 `backuprestoreapiexamples.azurewebsites.net` 웹 사이트를 구성하겠습니다. Default-Web-WestUS 리소스 그룹에 저장되며 ID가 00001111-2222-3333-4444-555566667777인 구독에서 실행됩니다.

![샘플 웹 사이트 정보][SampleWebsiteInformation]

<a name="backup-restore-rest-api"></a>
## REST API 백업 및 복원
이제 REST API를 사용하여 앱을 백업 및 복원하는 몇 가지 예를 살펴보겠습니다. 각 예제에는 URL 및 HTTP 요청 본문이 들어 있을 것입니다. 샘플 URL에 {subscriptionId}처럼 중괄호로 묶인 자리 표시자가 들어 있을 것입니다. 이러한 자리 표시자에 앱의 해당 정보를 입력해야 합니다. 예제 URL에 표시되는 각 자리 표시자에 대한 다음 설명을 참조하세요.

* subscriptionId – 앱이 포함된 Azure 구독의 ID
* resourceGroupName – 앱이 포함된 리소스 그룹의 이름
* sitename – 앱의 이름
* backupId – 앱 백업의 ID

HTTP 요청에 포함할 수 있는 여러 선택적 매개 변수를 포함하여 API에 대한 전체 설명서는 [Azure 리소스 탐색기](https://resources.azure.com/)를 참조하세요.

<a name="backup-on-demand"></a>
## 주문형 앱 백업
앱을 즉시 백업하려면 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{sitename}/backup/`에 **POST** 요청을 보내세요.

다음은 예제 웹 사이트를 사용한 URL의 모습입니다. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backup/`

요청 본문에 JSON 개체를 넣어서 백업을 저장하는 데 사용할 저장소 계정을 지정해야 합니다. JSON 개체에 **storageAccountUrl**이라는 속성이 있어야 합니다. 이 속성에는 백업 Blob를 보관할 Azure 저장소 컨테이너에 대한 쓰기 액세스 권한을 부여하는 [SAS URL](../storage/storage-dotnet-shared-access-signature-part-1.md)이 있습니다. 데이터베이스를 백업하려면 백업할 데이터베이스의 이름, 유형 및 연결 문자열이 들어 있는 목록을 제공해야 합니다.

```
{
    "properties":
    {
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        “databases”: [
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”,
                "connectionString": "<your database connection string>"
            }
        ]
    }
}
```

요청이 수신되면 그 즉시 앱 백업이 시작됩니다. 백업 프로세스가 완료될 때까지 시간이 오래 걸릴 수 있습니다. HTTP 응답에는 또 다른 요청에서 백업 상태를 보는 데 사용할 수 있는 ID가 포함되어 있습니다. 다음은 백업 요청에 대한 HTTP 응답 본문의 예입니다.

```
{
    "id": "/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples",
    "name": " backuprestoreapiexamples ",
    "type": "Microsoft.Web/sites",
    "location": "WestUS",
    "properties":    {
        "id": 1,
        "storageAccountUrl": “https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl”,
        "blobName": “backup_201509291825.zip”,
        "name": "backup_201509291825",
        "status": 4,
        "sizeInBytes": 0,
        "created": "2015-09-29T18:25:26.3992707Z",
        "log": null,
        "databases": [
            {
                "databaseType": "SqlAzure",
                "name": "MyDatabase1",
                "connectionString": "<your database connection string>"
            }
        ],
        "scheduled": false,
        "correlationId": "ea730f4d-dd06-4f7f-a090-9aa09k662h36",
    }
}
```

>[AZURE.NOTE] 오류 메시지는 HTTP 응답의 로그 속성에서 확인할 수 있습니다.

<a name="schedule-automatic-backups"></a>
## 자동 백업 예약
주문형 앱 백업 외에도 자동으로 백업하도록 예약할 수 있습니다.

### 새로운 자동 백업 일정 설정
백업 일정을 설정하려면 `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup`에 **PUT**을 보냅니다.

다음은 예제 웹 사이트의 URL 모습입니다. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup`

요청 본문에는 백업 구성을 지정하는 JSON 개체가 있어야 합니다. 다음은 필요한 매개 변수가 모두 포함된 예입니다.

```
{
    "location": "WestUS",
    "properties": // Represents an app restore request
    {
        "backupSchedule": { // Required for automatically scheduled backups
            "frequencyInterval": "7",
            "frequencyUnit": "Day",
            "keepAtLeastOneBackup": "True",
            "retentionPeriodInDays": "10",
        },
        "enabled": "True", // Must be set to true to enable automatic backups
        "name": “mysitebackup”,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

이 예에서는 7일마다 자동으로 앱을 백업하도록 구성합니다. 매개 변수 **frequencyInterval** 및 **frequencyUnit**이 백업 빈도를 결정합니다. **frequencyUnit**에 유효한 값은 **시간** 및 **일**입니다. 예를 들어 12시간마다 앱을 백업하려면 frequencyInterval을 12로, frequencyUnit을 시간으로 설정합니다.

기존 백업은 저장소 계정에서 자동으로 제거됩니다. **retentionPeriodInDays** 매개 변수를 설정하여 백업 보존 기간을 제어할 수 있습니다. 백업 보존 기간에 관계없이 하나 이상의 백업을 항상 저장하려면 **keepAtLeastOneBackup**을 true로 설정합니다.

### 자동 백업 일정 가져오기
앱의 백업 구성을 가져오려면 URL ` https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/config/backup/list`에 **POST** 요청을 보냅니다.

예제 사이트의 URL은 `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/config/backup/list`입니다.

<a name="get-backup-status"></a>
## 백업 상태 가져오기
앱의 크기에 따라 백업을 완료하는 데 다소 시간이 걸릴 수 있습니다. 또한 백업이 실패하거나, 시간이 초과되거나, 부분적으로 성공할 수 있습니다. 모든 앱의 백업 상태를 보려면 URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups`에 **GET** 요청을 보냅니다.

특정 웹앱의 백업 상태를 보려면 URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`에 GET 요청을 보냅니다.

다음은 예제 웹 사이트의 URL 모습입니다. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

응답 본문에는 이 예와 비슷한 JSON 개체가 들어 있을 것입니다.

```
{
    "properties":  {
        "id": 1,
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl",
        "blobName": "backup_201509291734.zip",
        "name": "backup_201509291734",
        "status": 2,
        "sizeInBytes": 151973,
        "created": "2015-09-29T17:34:57.2091605",
        "scheduled": false,
        "lastRestoreTimeStamp": null,
        "finishedTimeStamp": "2015-09-29T17:35:11.3293602",
        "correlationId": "2379fc13-418a-4b9c-920d-d06e73c5028d",
        "websiteSizeInBytes": 209920
    }
}
```

백업 상태가 열거 형식입니다. 다음과 같은 상태가 가능합니다.

* 0 – InProgress: 백업이 시작되었지만 아직 완료되지 않았습니다.
* 1 – Failed: 백업이 실패했습니다.
* 2 – Succeeded: 백업이 완료되었습니다.
* 3 – TimedOut: 백업이 시간 내에 완료되지 않아 취소되었습니다.
* 4 – Created: 대기열에 백업 요청이 생성되었지만 아직 시작되지 않았습니다.
* 5 – Skipped: 일정에서 너무 많은 백업을 트리거하여 백업이 진행되지 않았습니다.
* 6 – PartiallySucceeded: 백업을 성공했지만 일부 파일을 읽을 수 없어 백업하지 못했습니다. 이 문제는 일반적으로 파일에 배타적 잠금이 설정된 경우에 발생합니다.
* 7 – DeleteInProgress: 백업을 삭제하라는 요청이 있었지만 아직 삭제되지 않았습니다.
* 8 – DeleteFailed: 백업을 삭제하지 못했습니다. 백업을 만드는 데 사용된 SAS URL이 만료된 것이 원인일 수 있습니다.
* 9 – Deleted: 백업이 삭제되었습니다.

<a name="restore-app"></a>
## 백업으로 앱 복원
앱이 삭제되었거나 앱을 이전 버전으로 되돌리고 싶은 경우 백업으로 앱을 복원할 수 있습니다. 복원을 호출하려면 URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/restore`에 **POST** 요청을 보냅니다.

다음은 예제 웹 사이트의 URL 모습입니다. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/restore`

요청 본문에서 복원 작업의 속성이 포함된 JSON 개체를 보냅니다. 다음은 필요한 속성이 모두 포함된 예제입니다.

```
{
    "location": "WestUS",
    "properties":
    {
        "blobName": "backup_201509280431.zip",
        "databases": [ // Not required unless you’re restoring databases
            {
                “databaseType”: “SqlAzure”,
                “name”: “MyDatabase1”
        }],
        "overwrite": "true",
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl" // SAS URL to storage container containing your website backup
    }
}
```

### 새 앱으로 복원
백업을 복원할 때 기존 앱을 덮어쓰지 않고 새 앱을 만들려는 경우가 종종 있습니다. 이렇게 하려면 만들고 싶은 새 앱을 가리키도록 요청 URL을 변경하고, JSON의 **덮어쓰기** 속성을 **false**로 변경합니다.

<a name="delete-app-backup"></a>
## 앱 백업 삭제
백업을 삭제하려면 URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{backupId}`에 **DELETE** 요청을 보냅니다.

다음은 예제 웹 사이트의 URL 모습입니다. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1`

<a name="manage-sas-url"></a>
## 백업의 SAS URL 관리
Azure 앱 서비스에서는 백업을 만들 때 제공된 SAS URL을 사용하여 Azure 저장소에서 백업을 삭제하려고 시도할 것입니다. 이 SAS URL이 더 이상 유효하지 않으면 REST API를 통해 백업을 삭제할 수 없습니다. 그러나 URL `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{name}/backups/{id}/list`에 **POST** 요청을 보내서 백업과 연결된 SAS URL을 업데이트할 수 있습니다.

다음은 예제 웹 사이트의 URL 모습입니다. `https://management.azure.com/subscriptions/00001111-2222-3333-4444-555566667777/resourceGroups/Default-Web-WestUS/providers/Microsoft.Web/sites/backuprestoreapiexamples/backups/1/list`

요청 본문에서 새 SAS URL이 포함된 JSON 개체를 보냅니다. 다음은 예제입니다.

```
{
    "properties":
    {
        "storageAccountUrl": "https://account.blob.core.windows.net/backups?sv=2015-02-21&sr=c&sig=DzlkBl7h32C8qCv%2BifdBRxE63r4iv0kZ9L7E0qP16sY%3D&se=2016-09-15T22%3A46%3A54Z&sp=rwdl"
    }
}
```

>[AZURE.NOTE] 보안을 위해 특정 백업에 대한 GET 요청을 보낼 때 백업과 연결된 SAS URL이 반환되지 않습니다. 백업과 연결된 SAS URL을 보고 싶으면 위와 동일한 URL에 POST 요청을 보내고 요청 본문에 빈 JSON 개체를 넣으세요. 서버의 응답에 SAS URL을 포함하여 해당 백업의 모든 정보가 들어 있을 것입니다.

<!-- IMAGES -->
[SampleWebsiteInformation]: ./media/websites-csm-backup/01siteconfig.png

<!---HONumber=AcomDC_0224_2016-->