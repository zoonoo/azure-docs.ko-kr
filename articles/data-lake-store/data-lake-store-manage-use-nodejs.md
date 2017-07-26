---
title: "Node.js용 Azure SDK를 사용하여 Azure Data Lake Store 시작 | Microsoft 문서"
description: "Node.js를 사용하여 Data Lake Store 계정 및 파일 시스템을 사용하는 방법에 대해 알아봅니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 8c7a2e6ca061bbfa077592efb73d592906c3d070
ms.contentlocale: ko-kr
ms.lasthandoff: 06/08/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Node.js용 Azure SDK를 사용하여 Azure Data Lake Store 시작
> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

> [!NOTE]
> 대량의 데이터(큰 파일, 많은 수의 파일 또는 둘 다)를 업로드 및 다운로드하는 경우 [Python SDK](data-lake-store-get-started-python.md), [.NET SDK](data-lake-store-get-started-net-sdk.md), [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md) 또는 [Azure PowerShell](data-lake-store-get-started-powershell.md)을 사용하는 것이 좋습니다. 이러한 옵션은 여러 스레드를 사용하여 데이터 이동을 병렬화할 때 더 나은 성능을 제공합니다.
> 
> 

Node.js용 Azure SDK를 사용하여 Azure Data Lake Store 계정을 만들고 기본 작업(예: 폴더 만들기, 데이터 파일 업로드 및 다운로드, 계정 삭제 등)을 수행하는 방법에 대해 알아봅니다. Data Lake Store에 대한 자세한 내용은 [Data Lake Store 개요](data-lake-store-overview.md)를 참조하세요. 현재는 SDK에서 다음을 지원합니다.

* **Node.js 버전: 0.10.0 이상**
* **계정에 대한 REST API 버전: 2015-10-01-preview**
* **FileSystem용 REST API 버전: 2015-10-01-preview**

## <a name="prerequisites"></a>필수 조건
이 문서를 시작하기 전에 다음이 있어야 합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* **Azure Active Directory 응용 프로그램을 만듭니다**. Azure AD 응용 프로그램을 사용하여 Azure AD로 Data Lake Store 응용 프로그램을 인증합니다. Azure AD로 인증하는 여러 접근 방법에는 **최종 사용자 인증** 또는 **서비스 간 인증**이 있습니다. 지침 및 인증 방법에 대한 자세한 내용은 [최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md) 또는 [서비스 간 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="how-to-install"></a>설치 방법
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Azure Active Directory를 사용하여 인증
아래 코드 조각은 Azure AD를 사용하여 Data Lake Store에서 인증하는 별도의 두 가지 방법을 보여 줍니다. Data Lake Store 인증에 사용하는 다양한 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Data Lake Store로 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

아래 코드 조각에서도 Azure AD 도메인 이름, Azure AD 앱의 클라이언트 ID 등의 입력이 필요합니다. 이러한 모든 세부 정보는 사용자가 만들어야 하는 Azure AD 응용 프로그램에서 가져올 수 있습니다. 자세한 내용은 위 링크에도 포함되어 있습니다.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Data Lake Store 클라이언트 만들기
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Data Lake 저장소 계정 만들기
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="create-a-file-with-content"></a>콘텐츠를 포함하는 파일 만들기
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>파일 및 폴더 목록 가져오기
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>참고 항목
* [Node.js용 Microsoft Azure SDK](https://github.com/azure/azure-sdk-for-node)
* [Node.js용 Microsoft Azure SDK - Data Lake 분석 관리](https://www.npmjs.com/package/azure-arm-datalake-analytics)


