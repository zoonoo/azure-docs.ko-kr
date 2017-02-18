---
title: "REST API를 사용하여 Data Lake Analytics 시작 | Microsoft Docs"
description: "WebHDFS REST API를 사용하여 Data Lake Analytics에 대한 작업 수행"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5e133d92-baaa-44c9-890c-ab2d85c91122
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/03/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2fea3686b1484406d31c5447c7d3d7e2451b827e
ms.openlocfilehash: 1898b3d6aa1a9ccbc9f4427cf994c02f9fa35abd


---
# <a name="get-started-with-azure-data-lake-analytics-using-rest-apis"></a>REST API을 사용하여 Azure Data Lake Analytics 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

WebHDFS REST API 및 Data Lake Analytics REST API를 사용하여 Data Lake Analytics 계정, 작업 및 카탈로그를 관리하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 조건
* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Active Directory 응용 프로그램을 만듭니다**. Azure AD 응용 프로그램을 사용하여 Azure AD로 Data Lake Analytics 응용 프로그램을 인증합니다. Azure AD로 인증하는 여러 접근 방법에는 **최종 사용자 인증** 또는 **서비스 간 인증**이 있습니다. 인증하는 방법에 대한 지침 및 자세한 내용은 [Azure Active Directory를 사용하여 Data Lake Analytics로 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요.
* [cURL](http://curl.haxx.se/). 이 문서에서는 cURL을 사용하여 Data Lake Analytics 계정에 대해 REST API 호출을 수행하는 방법을 설명합니다.

## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory를 사용하여 인증
Azure Active Directory로 인증하는 방법에는 두 가지가 있습니다.

### <a name="end-user-authentication-interactive"></a>최종 사용자 인증(대화형)
이 방법을 사용하여 응용 프로그램은 로그인하라는 메시지를 표시하고 모든 작업은 사용자의 컨텍스트에서 수행됩니다. 

대화형 인증을 위해 다음 단계를 수행합니다.

1. 응용 프로그램을 통해 다음 URL로 사용자를 리디렉션합니다.
   
        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>
   
   > [!NOTE]
   > \<REDIRECT-URI>는 URL에서 사용하도록 인코딩되어야 합니다. 따라서 https://localhost의 경우 `https%3A%2F%2Flocalhost`)를 사용합니다.
   > 
   > 
   
    이 자습서에서는 위의 URL에 있는 자리 표시자 값을 바꿀 수 있으며 이를 웹 브라우저의 주소 표시줄에 붙여 넣습니다. Azure 로그인을 사용하여 인증하도록 리디렉션됩니다. 성공적으로 로그인되면 응답은 브라우저의 주소 표시줄에 표시됩니다. 응답은 다음 형식으로 되어 있습니다.
   
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>
2. 응답에서 인증 코드를 캡처합니다. 이 자습서에서는 웹 브라우저의 주소 표시줄에서 인증 코드를 복사하고 아래와 같이 토큰 끝점에 대한 게시 요청에 전달할 수 있습니다.
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>
   
   > [!NOTE]
   > 이 경우에 \<REDIRECT-URI>는 인코딩되지 않아야 합니다.
   > 
   > 
3. 응답은 액세스 토큰(예: `"access_token": "<ACCESS_TOKEN>"`) 및 새로 고침 토큰(예: `"refresh_token": "<REFRESH_TOKEN>"`)을 포함하는 JSON 개체입니다. 응용 프로그램은 Azure Data Lake 저장소에 액세스할 때 액세스 토큰을 사용하고 액세스 토큰이 만료되면 다른 액세스 토큰을 가져오는 새로 고침 토큰을 사용합니다.
   
        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before":    "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}
4. 액세스 토큰이 만료되면 아래와 같이 새로 고침 토큰을 사용하여 새 액세스 토큰을 요청할 수 있습니다.
   
        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
             -F grant_type=refresh_token \
             -F resource=https://management.core.windows.net/ \
             -F client_id=<CLIENT-ID> \
             -F refresh_token=<REFRESH-TOKEN>

대화형 사용자 인증에 대한 자세한 내용은 [인증 코드 부여 흐름](https://msdn.microsoft.com/library/azure/dn645542.aspx)을 참조하세요.

### <a name="service-to-service-authentication-non-interactive"></a>서비스 간 인증(비대화형)
이 방법을 사용하여 응용 프로그램은 고유한 자격 증명을 제공하여 작업을 수행합니다. 이를 위해 다음과 같은 POST 요청을 실행해야 합니다. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

이 요청의 출력에는 이후에 REST API 호출을 사용하여 전달할 권한 부여 토큰(아래 출력의 `access-token` 에서 지정)이 포함됩니다. 이 인증 토큰은 이 문서의 뒷부분에서 필요하므로 텍스트 파일에 저장해 두세요.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

이 문서에서는 **비대화형** 접근 방식을 사용합니다. 비대화형(서비스 간 호출)에 대한 자세한 내용은 [자격 증명을 사용하여 서비스 간 호출](https://msdn.microsoft.com/library/azure/dn645543.aspx)을 참조하세요.

## <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기
Data Lake Analytics 계정을 만들기 전에 Azure 리소스 그룹과 Data Lake Store 계정을 만들어야 합니다.  [Data Lake Store 계정 만들기](../data-lake-store/data-lake-store-get-started-rest-api.md#create-a-data-lake-store-account)를 참조하세요.

다음 Curl 명령에서는 계정을 만드는 방법을 보여 줍니다.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<NewAzureDataLakeAnalyticsAccountName>?api-version=2016-11-01 -d@"C:\tutorials\adla\CreateDataLakeAnalyticsAccountRequest.json"

\<`REDACTED`\>을 권한 부여 토큰으로, \<`AzureSubscriptionID`\>를 구독 ID로, \<`AzureResourceGroupName`\>을 기존 Azure 리소스 그룹 이름으로, \<`NewAzureDataLakeAnalyticsAccountName`\>을 새 Data Lake Analytics 계정 이름으로 바꿉니다. 이 명령에 대한 요청 페이로드는 위의 `-d` 매개 변수에 대해 제공된 **CreateDatalakeAnalyticsAccountRequest.json** 파일에 포함됩니다. input.json 파일의 내용은 다음과 유사합니다.

    {  
        "location": "East US 2",  
        "name": "myadla1004",  
        "tags": {},  
        "properties": {  
            "defaultDataLakeStoreAccount": "my1004store",  
            "dataLakeStoreAccounts": [  
                {  
                    "name": "my1004store"  
                }     
            ]
        }  
    }  


## <a name="list-data-lake-analytics-accounts-in-a-subscription"></a>구독에 Data Lake Analytics 계정 나열
다음 Curl 명령에서는 구독에서 계정을 나열하는 방법을 보여 줍니다.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/providers/Microsoft.DataLakeAnalytics/Accounts?api-version=2016-11-01

\<`REDACTED`\>을 권한 부여 토큰으로 바꾸고 \<`AzureSubscriptionID`\>을 구독 ID로 바꿉니다. 다음과 유사하게 출력됩니다.

    {
        "value": [
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla0831.azuredatalakeanalytics.net",
                "accountId": "21e74660-0941-4880-ae72-b143c2615ea9",
                "creationTime": "2016-09-01T12:49:12.7451428Z",
                "lastModifiedTime": "2016-09-01T12:49:12.7451428Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla0831rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla0831",
            "name": "myadla0831",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            },
            {
            "properties": {
                "provisioningState": "Succeeded",
                "state": "Active",
                "endpoint": "myadla1004.azuredatalakeanalytics.net",
                "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
                "creationTime": "2016-10-04T20:46:42.287147Z",
                "lastModifiedTime": "2016-10-04T20:46:42.287147Z"
            },
            "location": "East US 2",
            "tags": {},
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
            "name": "myadla1004",
            "type": "Microsoft.DataLakeAnalytics/accounts"
            }
        ]
    }

## <a name="get-information-about-a-data-lake-analytics-account"></a>Data Lake Analytics 계정에 대한 정보 가져오기
다음 Curl 명령에서는 계정 정보를 가져오는 방법을 보여 줍니다.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>?api-version=2015-11-01

\<`REDACTED`\>을 권한 부여 토큰으로, \<`AzureSubscriptionID`\>를 구독 ID로, \<`AzureResourceGroupName`\>을 기존 Azure 리소스 그룹 이름으로, \<`DataLakeAnalyticsAccountName`\>을 기존 Data Lake Analytics 계정 이름으로 바꿉니다. 다음과 유사하게 출력됩니다.

    {
        "properties": {
            "defaultDataLakeStoreAccount": "my1004store",
            "dataLakeStoreAccounts": [
            {
                "properties": {
                "suffix": "azuredatalakestore.net"
                },
                "name": "my1004store"
            }
            ],
            "provisioningState": "Creating",
            "state": null,
            "endpoint": null,
            "accountId": "3ff9b93b-11c4-43c6-83cc-276292eeb350",
            "creationTime": null,
            "lastModifiedTime": null
        },
        "location": "East US 2",
        "tags": {},
        "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004",
        "name": "myadla1004",
        "type": "Microsoft.DataLakeAnalytics/accounts"
    }

## <a name="list-data-lake-stores-of-a-data-lake-analytics-account"></a>Data Lake Analytics 계정의 Data Lake Stores 나열
다음 Curl 명령에서는 계정의 Data Lake Stores를 나열하는 방법을 보여 줍니다.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/<AzureSubscriptionID>/resourceGroups/<AzureResourceGroupName>/providers/Microsoft.DataLakeAnalytics/accounts/<DataLakeAnalyticsAccountName>/DataLakeStoreAccounts/?api-version=2016-11-01

\<`REDACTED`\>을 권한 부여 토큰으로, \<`AzureSubscriptionID`\>를 구독 ID로, \<`AzureResourceGroupName`\>을 기존 Azure 리소스 그룹 이름으로, \<`DataLakeAnalyticsAccountName`\>을 기존 Data Lake Analytics 계정 이름으로 바꿉니다. 다음과 유사하게 출력됩니다.

    {
        "value": [
            {
            "properties": {
                "suffix": "azuredatalakestore.net"
            },
            "id": "/subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/myadla1004rg/providers/Microsoft.DataLakeAnalytics/accounts/myadla1004/dataLakeStoreAccounts/my1004store",
            "name": "my1004store",
            "type": "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts"
            }
        ]
    }

## <a name="submit-u-sql-jobs"></a>U-SQL 작업 제출
다음 Curl 명령에서는 U-SQL 작업을 제출하는 방법을 보여 줍니다.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs/<NewGUID>?api-version=2016-03-20-preview -d@"C:\tutorials\adla\SubmitADLAJob.json"

\<`REDACTED`\>을 권한 부여 토큰으로 바꾸고 \<`DataLakeAnalyticsAccountName`\>을 기존 Data Lake Analytics 계정 이름으로 바꿉니다. 이 명령에 대한 요청 페이로드는 위의 `-d` 매개 변수에 대해 제공된 **SubmitADLAJob.json** 파일에 포함됩니다. input.json 파일의 내용은 다음과 유사합니다.

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "properties": {
            "type": "USql",
            "script": "@searchlog =\n    EXTRACT UserId          int,\n            Start           DateTime,\n            Region          string,\n            Query          
        string,\n            Duration        int?,\n            Urls            string,\n            ClickedUrls     string\n    FROM \"/Samples/Data/SearchLog.tsv\"\n    US
        ING Extractors.Tsv();\n\nOUTPUT @searchlog   \n    TO \"/Output/SearchLog-from-Data-Lake.csv\"\nUSING Outputters.Csv();"
        }
    }

다음과 유사하게 출력됩니다.

    {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "myadl@SPI",
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "2016-10-05T13:54:59.9871859+00:00",
        "state": "Compiling",
        "result": "Succeeded",
        "stateAuditRecords": [
            {
            "newState": "New",
            "timeStamp": "2016-10-05T13:54:59.9871859+00:00",
            "details": "userName:myadl@SPI;submitMachine:N/A"
            }
        ],
        "properties": {
            "owner": "myadl@SPI",
            "resources": [],
            "runtimeVersion": "default",
            "rootProcessNodeId": "00000000-0000-0000-0000-000000000000",
            "algebraFilePath": "adl://myadls0831.azuredatalakestore.net/system/jobservice/jobs/Usql/2016/10/05/13/54/8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a/algebra.xml",
            "compileMode": "Semantic",
            "errorSource": "Unknown",
            "totalCompilationTime": "PT0S",
            "totalPausedTime": "PT0S",
            "totalQueuedTime": "PT0S",
            "totalRunningTime": "PT0S",
            "type": "USql"
        }
    }


## <a name="list-u-sql-jobs"></a>U-SQL 작업 나열
다음 Curl 명령에서는 U-SQL 작업을 나열하는 방법을 보여 줍니다.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/Jobs?api-version=2016-11-01 

\<`REDACTED`\>을 권한 부여 토큰으로 바꾸고 \<`DataLakeAnalyticsAccountName`\>을 기존 Data Lake Analytics 계정 이름으로 바꿉니다. 

다음과 유사하게 출력됩니다.

    {
    "value": [
        {
        "jobId": "65cf1691-9dbe-43cd-90ed-1cafbfb406fb",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someone@microsoft.com",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:46:53 GMT",
        "startTime": "Wed, 05 Oct 2016 13:47:33 GMT",
        "endTime": "Wed, 05 Oct 2016 13:48:07 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        },
        {
        "jobId": "8f8ebf8c-4b63-428a-ab46-a03d2cc5b65a",
        "name": "convertTSVtoCSV",
        "type": "USql",
        "submitter": "someoneadl@SPI",
        "account": null,
        "degreeOfParallelism": 1,
        "priority": 1000,
        "submitTime": "Wed, 05 Oct 2016 13:54:59 GMT",
        "startTime": "Wed, 05 Oct 2016 13:55:43 GMT",
        "endTime": "Wed, 05 Oct 2016 13:56:11 GMT",
        "state": "Ended",
        "result": "Succeeded",
        "errorMessage": null,
        "storageAccounts": null,
        "stateAuditRecords": null,
        "logFilePatterns": null,
        "properties": null
        }
    ],
    "nextLink": null,
    "count": null
    }


## <a name="get-catalog-items"></a>카탈로그 항목 가져오기
다음 Curl 명령에서는 카탈로그에서 데이터베이스를 가져오는 방법을 보여 줍니다.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<DataLakeAnalyticsAccountName>.azuredatalakeanalytics.net/catalog/usql/databases?api-version=2016-11-01

다음과 유사하게 출력됩니다.

    {
    "@odata.context":"https://myadla0831.azuredatalakeanalytics.net/sqlip/$metadata#databases","value":[
        {
        "computeAccountName":"myadla0831","databaseName":"mytest","version":"f6956327-90b8-4648-ad8b-de3ff09274ea"
        },{
        "computeAccountName":"myadla0831","databaseName":"master","version":"e8bca908-cc73-41a3-9564-e9bcfaa21f4e"
        }
    ]
    }

## <a name="see-also"></a>참고 항목
* 더 복잡한 쿼리를 보려면 [Azure Data Lake Analytics을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
* U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
* U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
* 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
* Data Lake Analytics에 대한 개요를 보려면 [Azure Data Lake Analytics 개요](data-lake-analytics-overview.md)를 참조하세요.
* 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
* 진단 정보를 기록하려면 [Azure Data Lake Analytics에 대한 진단 로그에 액세스](data-lake-analytics-diagnostic-logs.md)




<!--HONumber=Feb17_HO1-->


