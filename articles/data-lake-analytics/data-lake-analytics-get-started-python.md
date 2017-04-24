---
title: "Python을 사용하여 Azure Data Lake Analytics 시작 | Microsoft Docs"
description: "Python을 사용하여 Data Lake Store 계정을 만들고 작업을 제출하는 방법을 알아봅니다. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 5618650671badfc54860c3ad8af5d1e727d3d8c9
ms.openlocfilehash: 40ccfc59cccd86a7634ec89656571b3cd23566b4
ms.lasthandoff: 11/23/2016


---


# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-python"></a>자습서: Python을 사용하여 Azure Data Lake Analytics 시작
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Python을 사용하여 Azure Data Lake 분석 계정을 만들고, [U-SQL](data-lake-analytics-u-sql-get-started.md)로 Data Lake Analytics 작업을 정의하고, 작업을 Data Lake Analytics 계정에 제출하는 방법에 대해 알아봅니다. 데이터 레이크 분석에 대한 자세한 내용은 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.

이 자습서에서는 TSV(탭 분리 값) 파일을 읽고 CSV(쉼표로 구분된 값) 파일로 변환하는 작업을 개발합니다. 지원되는 다른 도구를 사용하여 같은 자습서를 진행하려면 이 섹션의 맨 위에 있는 탭을 클릭하세요.

##<a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 항목이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
- **Azure Active Directory 응용 프로그램** Azure AD 응용 프로그램을 사용하여 Azure AD로 Data Lake Store 응용 프로그램을 인증합니다. Azure AD로 인증하는 여러 접근 방법에는 최종 사용자 인증 또는 서비스 간 인증이 있습니다. 인증 하는 방법에 대한 지침 및 자세한 내용은 [Azure Active Directory를 사용하여 Data Lake Store로 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요.
- **[Python](https://www.python.org/downloads/)**. 64비트 버전을 사용해야 합니다. 이 문서에서는 Python 버전 3.5.2를 사용합니다.


## <a name="install-azure-python-sdk"></a>Azure Python SDK 설치

Python을 사용한 Data Lake Store 작업을 위해서는 3가지 모듈을 설치해야 합니다.

azure-mgmt-datalake-store 모듈에는 Azure Data Lake Store 계정 관리 작업이 포함됩니다. azure-mgmt-resource 모듈에는 Active Directory에 대한 다른 Azure 모듈이 포함됩니다. azure-datalake-store 모듈에는 Azure Data Lake Store 파일 시스템 작업이 포함됩니다. azure-datalake-analytics 모듈에는 Azure Data Lake Analytics 작업이 포함됩니다. 다음 명령을 사용하여 모듈을 설치합니다.

    pip install azure-mgmt-resource
    pip install azure-mgmt-datalake-store
    pip install azure-mgmt-datalake-analytics
    pip install azure-datalake-store

## <a name="create-a-python-application"></a>Python 응용 프로그램 만들기

1. mysample.py와 같이 새 Python 응용 프로그램을 만들기 위해 원하는 IDE를 사용합니다.

2. 다음 줄을 추가하여 필요한 모듈을 가져옵니다.

        ## Use this only for Azure AD service-to-service authentication
        from azure.common.credentials import ServicePrincipalCredentials

        ## Use this only for Azure AD end-user authentication
        from azure.common.credentials import UserPassCredentials

        ## Required for Azure Resource Manager
        from azure.mgmt.resource.resources import ResourceManagementClient
        from azure.mgmt.resource.resources.models import ResourceGroup

        ## Required for Azure Data Lake Store account management
        from azure.mgmt.datalake.store import DataLakeStoreAccountManagementClient
        from azure.mgmt.datalake.store.models import DataLakeStoreAccount

        ## Required for Azure Data Lake Store filesystem management
        from azure.datalake.store import core, lib, multithread

        ## Required for Azure Data Lake Analytics account management
        from azure.mgmt.datalake.analytics.account import DataLakeAnalyticsAccountManagementClient
        from azure.mgmt.datalake.analytics.account.models import DataLakeAnalyticsAccount, DataLakeStoreAccountInfo

        ## Required for Azure Data Lake Analytics job management
        from azure.mgmt.datalake.analytics.job import DataLakeAnalyticsJobManagementClient
        from azure.mgmt.datalake.analytics.job.models import JobInformation, JobState, USqlJobProperties

        ## Required for Azure Data Lake Analytics catalog management
        from azure.mgmt.datalake.analytics.catalog import DataLakeAnalyticsCatalogManagementClient

        ## Use these as needed for your application
        import logging, getpass, pprint, uuid, time

3. Python 응용 프로그램 파일에 변경 내용을 저장합니다.

## <a name="authentication"></a>인증

다음 방법 중 하나를 사용하여 인증합니다.

### <a name="end-user-authentication-for-account-management"></a>계정 관리를 위한 최종 사용자 인증

이 방법으로 계정 관리 작업을 위해 Azure AD에 인증합니다(Data Lake Store 계정 만들기/삭제 등). Azure AD 사용자에 대한 사용자 이름과 암호를 제공해야 합니다. 사용자 계정은 Multi-Factor Authentication으로 구성할 수 없으며 계정은 Microsoft 계정/Live ID(예: @outlook.com, 및 @live.com.)가 될 수 없습니다.

    user = input('Enter the user to authenticate with that has permission to subscription: ')
    password = getpass.getpass()

    credentials = UserPassCredentials(user, password)

### <a name="service-to-service-authentication-with-client-secret-for-account-management"></a>계정 관리를 위해 클라이언트 암호로 서비스 간 인증

이 방법으로 계정 관리 작업을 위해 Azure AD에 인증합니다(Data Lake Store 계정 만들기/삭제 등). 다음 코드 조각은 응용 프로그램/서비스 주체에 대한 클라이언트 비밀을 사용하여 비대화형으로 응용 프로그램을 인증하는 데 사용될 수 있습니다. 기존 Azure AD "Web App" 응용 프로그램과 함께 사용합니다.

    credentials = ServicePrincipalCredentials(client_id = 'FILL-IN-HERE', secret = 'FILL-IN-HERE', tenant = 'FILL-IN-HERE')

## <a name="create-resource-management-group"></a>리소스 관리 그룹 만들기

Azure Resource Group을 만들려면 다음 코드 조각을 사용합니다.

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'

    ## Create management client object
    resourceClient = ResourceManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Resource Group
    armGroupResult = resourceClient.resource_groups.create_or_update(
        resourceGroup,
        ResourceGroup(
            location=location
        )
    )


## <a name="create-data-lake-store-account"></a>Data Lake Store 계정 만들기

각 Data Lake Analytics 계정에는 Data Lake Store 계정이 필요합니다. 지침은 [Data Lake Store 계정 만들기](../data-lake-store/data-lake-store-get-started-portal.md#create-an-azure-data-lake-store-account)를 참조하세요.



## <a name="create-data-lake-analytics-account"></a>데이터 레이크 분석 계정 만들기

    ## Declare variables
    subscriptionId= '<Azure Subscription ID>'
    resourceGroup = '<Azure Resource Group Name>'
    location = '<Location>' # i.e. 'eastus2'
    adlsAccountName = '<Azure Data Lake Store Account Name>'
    adlaAccountName = '<Azure Data Lake Analytics Account Name>'

    ## Create management client object
    adlaAcctClient = DataLakeAnalyticsAccountManagementClient(
        credentials,
        subscriptionId
    )

    ## Create an Azure Data Lake Analytics account
    adlaAcctResult = adlaAcctClient.account.create(
        resourceGroup,
        adlaAccountName,
        DataLakeAnalyticsAccount(
            location=location,
            default_data_lake_store_account=adlsAccountName,
            data_lake_store_accounts=[DataLakeStoreAccountInfo(name=adlsAccountName)]
        )
    ).wait()


##<a name="submit-data-lake-analytics-jobs"></a>데이터 레이크 분석 작업 제출

데이터 레이크 분석 작업은 U-SQL 언어로 작성됩니다. U-SQL에 대한 자세한 내용은 [U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md) 및 [U-SQL 언어 참조](http://go.microsoft.com/fwlink/?LinkId=691348)를 참조하세요.

    ## Declare variables
    adlsAccountName = '<Azure Data Lake Store Account Name>'

    ## Create management client object
    adlaJobClient = DataLakeAnalyticsJobManagementClient(
        credentials,
        'azuredatalakeanalytics.net'
    )

    ## Submit a U-SQL job
    jobId = str(uuid.uuid4())
    jobResult = adlaJobClient.job.create(
        adlaAccountName,
        jobId,
        JobInformation(
            name='Sample Job',
            type='USql',
            properties=USqlJobProperties(
                script='DROP DATABASE IF EXISTS FOO; CREATE DATABASE FOO;'
            )
        )
    )

    ## Print the job result
    while(jobResult.state != JobState.ended):
        print('Job is not yet done, waiting for 3 seconds. Current state: ' + jobResult.state.value)
        time.sleep(3)
        jobResult = adlaJobClient.job.get(adlaAccountName, jobId)
        
    print ('Job finished with result: ' + jobResult.result.value)

## <a name="next-steps"></a>다음 단계

- 다른 도구를 사용하여 같은 자습서를 보려면 페이지 맨 위의 탭 선택기를 클릭합니다.
- 더 복잡한 쿼리를 보려면 [Azure 데이터 레이크 분석을 사용하여 웹 사이트 로그 분석](data-lake-analytics-analyze-weblogs.md)을 참조하세요.
- U-SQL 응용 프로그램 개발을 시작하려면 [Visual Studio용 Data Lake 도구를 사용하여 U-SQL 스크립트 개발](data-lake-analytics-data-lake-tools-get-started.md)을 참조하세요.
- U-SQL을 알아보려면 [Azure Data Lake Analytics U-SQL 언어 시작](data-lake-analytics-u-sql-get-started.md)을 참조하세요.
- 관리 작업을 보려면 [Azure Portal을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)를 참조하세요.
- 데이터 레이크 분석에 대한 개요를 보려면 [Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)를 참조하세요.


