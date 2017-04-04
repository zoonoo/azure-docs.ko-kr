---
title: "Python SDK를 사용하여 Azure Data Lake Store 시작 | Microsoft Docs"
description: "Python SDK로 Data Lake Store 계정 및 파일 시스템을 사용하는 방법을 알아봅니다."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 75f6de6f-6fd8-48f4-8707-cb27d22d27a6
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: a939a0845d7577185ff32edd542bcb2082543a26
ms.openlocfilehash: 8a3f3d8bfe670f2a4d1a4642b2380764aa6daeb4
ms.lasthandoff: 02/17/2017


---

# <a name="get-started-with-azure-data-lake-store-using-python"></a>Python을 사용하여 Azure Data Lake Store 시작

> [!div class="op_single_selector"]
> * [포털](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Azure용 Python SDK 및 Azure Data Lake Store를 사용하여 폴더 만들기, 데이터 파일 업로드 및 다운로드 등의 기본 작업을 수행하는 방법에 대해 알아봅니다. Data Lake에 대한 자세한 내용은 [Azure Data Lake Store](data-lake-store-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* **Python**. Python을 [여기](https://www.python.org/downloads/)에서 다운로드할 수 있습니다. 이 문서에서는 Python 3.5.2를 사용합니다.

* **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory 응용 프로그램을 만듭니다**. Azure AD 응용 프로그램을 사용하여 Azure AD로 Data Lake Store 응용 프로그램을 인증합니다. Azure AD로 인증하는 여러 접근 방법에는 **최종 사용자 인증** 또는 **서비스 간 인증**이 있습니다. 인증 하는 방법에 대한 지침 및 자세한 내용은 [Azure Active Directory를 사용하여 Data Lake Store로 인증](data-lake-store-authenticate-using-active-directory.md)을 참조하세요.

## <a name="install-the-modules"></a>모듈 설치

Python을 사용한 Data Lake Store 작업을 위해서는&3;가지 모듈을 설치해야 합니다.

* `azure-mgmt-resource` 모듈. Active Directory 등 Azure 모듈을 포함합니다...
* `azure-mgmt-datalake-store` 모듈. Azure Data Lake Store 계정 관리 작업이 포함됩니다. 이 모듈에 대한 자세한 내용은 [Azure Data Lake Store 관리 모듈 참조](http://azure-sdk-for-python.readthedocs.io/en/latest/sample_azure-mgmt-datalake-store.html)를 참조하세요.
* `azure-datalake-store` 모듈. Azure Data Lake Store 파일 시스템 작업이 포함됩니다. 이 모듈에 대한 자세한 내용은 [Azure Data Lake Store 파일 시스템 모듈 참조](http://azure-datalake-store.readthedocs.io/en/latest/)를 참조하세요.

다음 명령을 사용하여 모듈을 설치합니다.

```
pip install azure-mgmt-resource
pip install azure-mgmt-datalake-store
pip install azure-datalake-store
```

## <a name="create-a-new-python-application"></a>새 Python 응용 프로그램 만들기

1. 원하는 IDE에서 **mysample.py**와 같이 새 Python 응용 프로그램을 만듭니다.

2. 다음 줄을 추가하여 필요한 모듈을 가져옵니다.

    ```
    ## Use this only for Azure AD service-to-service authentication
    from azure.common.credentials import ServicePrincipalCredentials

    ## Use this only for Azure AD end-user authentication
    from azure.common.credentials import UserPassCredentials

    ## Use this only for Azure AD multi-factor authentication
    from msrestazure.azure_active_directory import AADTokenCredentials

    ## Required for Azure Data Lake Store account management
    from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
    from azure.mgmt.datalake.store.models import DataLakeStoreAccount

    ## Required for Azure Data Lake Store filesystem management
    from azure.datalake.store import core, lib, multithread

    # Common Azure imports
    from azure.mgmt.resource.resources import ResourceManagementClient
    from azure.mgmt.resource.resources.models import ResourceGroup

    ## Use these as needed for your application
    import logging, getpass, pprint, uuid, time
    ```

3. mysample.py의 변경 내용을 저장합니다.

## <a name="authentication"></a>인증

이 섹션에서는 Azure AD로 인증하는 다양한 방법에 대해 설명합니다. 제공되는 옵션은 다음과 같습니다.

* 최종 사용자 인증
* 서비스 간 인증
* Multi-Factor Authentication

계정 관리와 파일 시스템 관리 모듈 모두에 대해 이러한 인증 옵션을 사용해야 합니다.

### <a name="end-user-authentication-for-account-management"></a>계정 관리를 위한 최종 사용자 인증

이를 사용하여 계정 관리 작업(Data Lake Store 계정 만들기/삭제 등)을 위해 Azure AD에 인증합니다. Azure AD 사용자에 대한 사용자 이름과 암호를 제공해야 합니다. 사용자는 Multi-Factor Authentication에 대해 구성되지 않아야 합니다.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="end-user-authentication-for-filesystem-operations"></a>파일 시스템 작업에 대한 최종 사용자 인증

이를 사용하여 파일 시스템 작업(폴더 만들기, 파일 업로드 등)을 위해 Azure AD에 인증합니다. 기존 Azure AD **네이티브 클라이언트**와 함께 사용합니다. 자격 증명을 제공하는 Azure AD 사용자는 Multi-Factor Authentication에 대해 구성되어야 합니다.

    tenant_id = 'FILL-IN-HERE'
    client_id = 'FILL-IN-HERE'
    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    token = lib.auth(tenant_id, user, password, client_id)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>계정 관리를 위해 클라이언트 암호로 서비스 간 인증

이를 사용하여 계정 관리 작업(Data Lake Store 계정 만들기/삭제 등)을 위해 Azure AD에 인증합니다. 다음 코드 조각은 응용 프로그램/서비스 주체에 대한 클라이언트 비밀을 사용하여 비대화형으로 응용 프로그램을 인증하는 데 사용될 수 있습니다. 기존 Azure AD "Web App" 응용 프로그램과 함께 사용합니다.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

### <a name="service-to-service-authentication-with-client-secret-for-filesystem-operations"></a>파일 시스템 작업을 위해 클라이언트 암호로 서비스 간 인증

이를 사용하여 파일 시스템 작업(폴더 만들기, 파일 업로드 등)을 위해 Azure AD에 인증합니다. 다음 코드 조각은 응용 프로그램/서비스 주체에 대한 클라이언트 비밀을 사용하여 비대화형으로 응용 프로그램을 인증하는 데 사용될 수 있습니다. 기존 Azure AD "Web App" 응용 프로그램과 함께 사용합니다.

    token = lib.auth(tenant_id = 'FILL-IN-HERE', client_secret = 'FILL-IN-HERE', client_id = 'FILL-IN-HERE')

### <a name="multi-factor-authentication-for-account-management"></a>계정 관리를 위한 Multi-Factor Authentication

이를 사용하여 계정 관리 작업(Data Lake Store 계정 만들기/삭제 등)을 위해 Azure AD에 인증합니다. 다음 코드 조각은 Multi-Factor Authentication을 사용하여 응용 프로그램을 인증하는 데 사용할 수 있습니다. 기존 Azure AD "Web App" 응용 프로그램과 함께 사용합니다.

    authority_host_url = "https://login.microsoftonline.com"
    tenant = "FILL-IN-HERE"
    authority_url = authority_host_url + '/' + tenant
    client_id = 'FILL-IN-HERE'
    redirect = 'urn:ietf:wg:oauth:2.0:oob'
    RESOURCE = 'https://management.core.windows.net/'
    
    context = adal.AuthenticationContext(authority_url)
    code = context.acquire_user_code(RESOURCE, client_id)
    print(code['message'])
    mgmt_token = context.acquire_token_with_device_code(RESOURCE, code, client_id)
    credentials = AADTokenCredentials(mgmt_token, client_id)

### <a name="multi-factor-authentication-for-filesystem-management"></a>파일 시스템 관리를 위한 Multi-Factor Authentication

이를 사용하여 파일 시스템 작업(폴더 만들기, 파일 업로드 등)을 위해 Azure AD에 인증합니다. 다음 코드 조각은 Multi-Factor Authentication을 사용하여 응용 프로그램을 인증하는 데 사용할 수 있습니다. 기존 Azure AD "Web App" 응용 프로그램과 함께 사용합니다.

    token = lib.auth(tenant_id='FILL-IN-HERE')

## <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기

Azure Resource Group을 만들려면 다음 코드 조각을 사용합니다.

    ## Declare variables
    subscriptionId= 'FILL-IN-HERE'
    resourceGroup = 'FILL-IN-HERE'
    location = 'eastus2'
    
    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )
    
    ## Create an Azure Resource Group
    resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )

## <a name="create-clients-and-data-lake-store-account"></a>클라이언트 및 Data Lake Store 계정 만들기

다음 코드 조각은 Data Lake Store 계정 클라이언트를 먼저 만듭니다. 클라이언트 개체를 사용하여 Data Lake Store 계정을 만들 수 있습니다. 마지막으로 코드 조각은 파일 시스템 클라이언트 개체를 만듭니다.

    ## Declare variables
    subscriptionId = 'FILL-IN-HERE'
    adlsAccountName = 'FILL-IN-HERE'

    ## Create management client object
    adlsAcctClient = DataLakeStoreAccountManagementClient(credentials, subscriptionId)

    ## Create a Data Lake Store account
    adlsAcctResult = adlsAcctClient.account.create(
        resourceGroup,
        adlsAccountName,
        DataLakeStoreAccount(
            location=location
        )
    ).wait()

    ## Create a filesystem client object
    adlsFileSystemClient = core.AzureDLFileSystem(token, store_name=adlsAccountName)

## <a name="list-the-data-lake-store-accounts"></a>Data Lake Store 계정 나열

    ## List the existing Data Lake Store accounts
    result_list_response = adlsAcctClient.account.list()
    result_list = list(result_list_response)
    for items in result_list:
        print(items)

## <a name="create-a-directory"></a>디렉터리 만들기

    ## Create a directory
    adlsFileSystemClient.mkdir('/mysampledirectory')

## <a name="upload-a-file"></a>파일 업로드


    ## Upload a file
    multithread.ADLUploader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)


## <a name="download-a-file"></a>파일 다운로드

    ## Download a file
    multithread.ADLDownloader(adlsFileSystemClient, lpath='C:\\data\\mysamplefile.txt.out', rpath='/mysampledirectory/mysamplefile.txt', nthreads=64, overwrite=True, buffersize=4194304, blocksize=4194304)

## <a name="delete-a-directory"></a>디렉터리 삭제

    ## Delete a directory
    adlsFileSystemClient.rm('/mysampledirectory', recursive=True)

## <a name="see-also"></a>참고 항목

- [데이터 레이크 저장소의 데이터 보호](data-lake-store-secure-data.md)
- [Azure 데이터 레이크 분석에 데이터 레이크 저장소 사용](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [데이터 레이크 저장소와 함께 Azure HDInsight 사용](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Data Lake Store .NET SDK 참조](https://msdn.microsoft.com/library/mt581387.aspx)
- [Data Lake Store REST 참조](https://msdn.microsoft.com/library/mt693424.aspx)

