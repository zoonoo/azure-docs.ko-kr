<properties 
   pageTitle="REST API를 사용하여 Data Lake 저장소 시작 | Microsoft Azure" 
   description="WebHDFS REST API를 사용하여 Data Lake 저장소에 대한 작업 수행" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/08/2016"
   ms.author="nitinme"/>

# REST API를 사용하여 Azure Data Lake 저장소 시작

> [AZURE.SELECTOR]
- [포털](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.JS](data-lake-store-manage-use-nodejs.md)

이 문서에서는 WebHDFS REST API 및 Data Lake 저장소 REST API를 사용하여 Azure Data Lake 저장소에 대한 계정 관리 및 파일 시스템 작업을 수행하는 방법을 알아봅니다. Azure Data Lake 저장소는 계정 관리 작업을 위해 자체 REST API를 제공합니다. 그러나 Data Lake 저장소는 HDFS 및 Hadoop 에코시스템과 호환되기 때문에 파일 시스템 작업에 WebHDFS REST API를 사용하도록 지원합니다.

>[AZURE.NOTE] Data Lake 저장소의 REST API 지원에 대한 자세한 내용은 [Azure Data Lake 저장소 REST API 참조](https://msdn.microsoft.com/library/mt693424.aspx)를 참조하세요.

## 필수 조건

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- 데이터 레이크 저장소 공개 미리 보기를 위해 **Azure 구독을 사용하도록 설정합니다**. [지침](data-lake-store-get-started-portal.md#signup)을 참조하세요.
- **Azure Active Directory 응용 프로그램 만들기**. [포털을 사용하여 Active Directory 응용 프로그램 및 서비스 사용자 만들기](../resource-group-create-service-principal-portal.md)를 참조하세요. 응용 프로그램을 만든 후 응용 프로그램과 관련된 다음 값을 검색합니다.
	- 응용 프로그램에 대한 클라이언트 ID 및 테넌트 ID 가져오기
	- 인증 키 만들기
	- 위임된 권한 설정

	이러한 값을 검색하는 방법에 대한 지침은 위에 제공된 링크에서 제공됩니다.
- **역할에 Azure Active Directory 응용 프로그램 할당**. 역할은 Azure Active Directory 응용 프로그램에 권한을 부여하려는 범위 수준에 있을 수 있습니다. 예를 들어 구독 수준 또는 리소스 그룹 수준에서 응용 프로그램을 할당할 수 있습니다. 지침은 [역할에 응용 프로그램 할당](../resource-group-create-service-principal-portal.md#assign-application-to-role)을 참조하세요.
- [cURL](http://curl.haxx.se/). 이 문서에서는 cURL을 사용하여 Data Lake 저장소 계정에 대해 REST API 호출을 수행하는 방법을 설명합니다.

## Azure Active Directory를 사용하여 인증하려면 어떻게 해야 하나요?

Azure Active Directory를 사용하여 인증하는 접근 방식에는 두 가지 접근 방식이 있습니다.

* **대화형** - 응용 프로그램에서 사용자에게 로그인하라는 메시지를 표시합니다. 자세한 내용은 [권한 부여 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx)을 참조하세요.

* **비대화형** - 응용 프로그램이 자체 자격 증명을 제공합니다. 자세한 내용은 [자격 증명을 사용하여 서비스 간 호출](https://msdn.microsoft.com/library/azure/dn645543.aspx)을 참조하세요.

이 문서에서는 **비대화형** 접근 방식을 사용합니다. 이를 위해 다음과 같은 POST 요청을 실행해야 합니다.

	curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

이 요청의 출력에는 이후에 REST API 호출을 사용하여 전달할 권한 부여 토큰(아래 출력의 `access-token`)이 포함됩니다. 이 인증 토큰은 이 문서의 뒷부분에서 필요하므로 텍스트 파일에 저장해 두세요.

	{"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

## Data Lake 저장소 계정 만들기

이 작업은 [여기](https://msdn.microsoft.com/library/mt694078.aspx)에 정의된 REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@C:\temp\input.json

위 명령에서 <`REDACTED`>를 이전에 검색한 권한 부여 토큰으로 바꿉니다. 이 명령에 대한 요청 페이로드는 위의 `-d` 매개 변수에 대해 제공된 **input.json** 파일에 포함됩니다. input.json 파일의 내용은 다음과 유사합니다.

	{
	"location": "eastus2",
	"tags": {
		"department": "finance"
		},
	"properties": {}
	}	

## Data Lake 저장소 계정에서 폴더 만들기

이 작업은 [여기](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

위 명령에서 <`REDACTED`>를 이전에 검색한 권한 부여 토큰으로 바꿉니다. 이 명령은 Data Lake 저장소 계정의 루트 폴더 아래에 **mytempdir**이라는 디렉터리를 만듭니다.

작업이 성공적으로 완료되면 다음과 유사한 응답이 표시됩니다.

	{"boolean":true}

## Data Lake 저장소 계정의 폴더 나열

이 작업은 [여기](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

다음 cURL 명령을 사용합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

위 명령에서 <`REDACTED`>를 이전에 검색한 권한 부여 토큰으로 바꿉니다.

작업이 성공적으로 완료되면 다음과 유사한 응답이 표시됩니다.

	{
	"FileStatuses": {
		"FileStatus": [{
			"length": 0,
			"pathSuffix": "mytempdir",
			"type": "DIRECTORY",
			"blockSize": 268435456,
			"accessTime": 1458324719512,
			"modificationTime": 1458324719512,
			"replication": 0,
			"permission": "777",
			"owner": "NotSupportYet",
			"group": "NotSupportYet"
		}]
	}
	}

## Data Lake 저장소 계정에 데이터 업로드

이 작업은 [여기](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

WebHDFS REST API를 사용하여 데이터를 업로드하는 작업은 아래에 설명된 대로 2단계 프로세스입니다.

1. 업로드할 파일 데이터를 보내지 않고 HTTP PUT 요청을 제출합니다. 다음 명령에서 **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

		curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

	이 명령의 출력에는 다음과 유사한 임시 리디렉션 URL이 포함됩니다.

		HTTP/1.1 100 Continue

		HTTP/1.1 307 Temporary Redirect
		...
		...
		Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
		...
		...

2. 이제 응답의 **Location** 속성에 나열된 URL에 대해 또 다른 HTTP PUT 요청을 제출해야 합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

		curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

	다음과 유사하게 출력됩니다.

		HTTP/1.1 100 Continue

		HTTP/1.1 201 Created
		...
		...

## Data Lake 저장소 계정에서 데이터 읽기

이 작업은 [여기](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

Data Lake 저장소 계정에서 데이터를 읽는 작업은 2단계 프로세스입니다.

* 먼저 끝점 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`에 대해 GET 요청을 제출합니다. 다음 GET 요청을 제출할 위치가 반환됩니다.
* 그러면 끝점 `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`에 대해 GET 요청을 제출합니다. 파일 내용이 표시됩니다.

그러나 첫 번째 단계와 두 번째 단계 간에 입력 매개 변수의 차이가 없으므로 `-L` 매개 변수를 사용하여 첫 번째 요청을 제출할 수 있습니다. `-L` 옵션은 기본적으로 두 요청을 하나로 결합하며 cURL이 새 위치에서 요청을 다시 실행하도록 만듭니다. 마지막으로, 모든 요청 호출의 출력이 아래와 유사하게 표시됩니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

다음과 유사한 결과가 표시됩니다.

	HTTP/1.1 307 Temporary Redirect
	...
	Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
	...
	
	HTTP/1.1 200 OK
	...
	
	Hello, Data Lake Store user!

## Data Lake 저장소 계정에서 파일 이름 바꾸기

이 작업은 [여기](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

파일의 이름을 바꾸려면 다음 cURL 명령을 사용합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

다음과 유사한 결과가 표시됩니다.

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Data Lake 저장소 계정에서 파일 삭제

이 작업은 [여기](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory)에 정의된 WebHDFS REST API 호출을 기반으로 합니다.

파일을 삭제하려면 다음 cURL 명령을 사용합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

다음과 유사한 출력이 표시됩니다.

	HTTP/1.1 200 OK
	...
	
	{"boolean":true}

## Data Lake 저장소 계정 삭제

이 작업은 [여기](https://msdn.microsoft.com/library/mt694075.aspx)에 정의된 REST API 호출을 기반으로 합니다.

Data Lake 저장소 계정을 삭제하려면 다음 cURL 명령을 사용합니다. **<yourstorename>**을 Data Lake 저장소 이름으로 바꿉니다.

	curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

다음과 유사한 출력이 표시됩니다.

	HTTP/1.1 200 OK
	...
	...

## 참고 항목

- [Azure Data Lake 저장소와 호환되는 오픈 소스 빅 데이터 응용 프로그램](data-lake-store-compatible-oss-other-applications.md)
 

<!---HONumber=AcomDC_0413_2016-->