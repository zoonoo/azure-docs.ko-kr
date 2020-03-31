---
title: Azure 데이터 팩토리 문제 해결 | 마이크로 소프트 문서
description: Azure 데이터 팩터리에서 외부 제어 활동 문제를 해결하는 방법을 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065389"
---
# <a name="troubleshoot-azure-data-factory"></a>Azure 데이터 팩터리 문제 해결

이 문서에서는 Azure Data Factory에서 외부 제어 활동에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="connector-and-copy-activity"></a>커넥터 및 복사 활동

복사 작업을 사용하는 커넥터 문제(예: 발생 오류)는 [Azure 데이터 팩토리 커넥터 문제를](connector-troubleshoot-guide.md)참조하십시오.
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>오류 코드: 3200

- **메시지**: 오류 403.

- **원인**:`The Databricks access token has expired.`

- **권장 사항**: 기본적으로 Azure Databricks 액세스 토큰은 90일 동안 유효합니다. 새 토큰을 만들고 연결된 서비스를 업데이트합니다.


### <a name="error-code--3201"></a>오류 코드: 3201

- **메시지**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **원인**:`Bad authoring: Notebook path not specified correctly.`

- **권장 사항**: Databricks 활동에서 전자 필기장 경로를 지정합니다.

<br/>  

- **메시지**:`Cluster... does not exist.`

- **원인**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **권장 사항**: Databricks 클러스터가 있는지 확인합니다.

<br/>  

- **메시지**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **원인**:`Bad authoring.`

- **권장 사항**: 작업 영역 주소 지정 스키마 `dbfs:/folder/subfolder/foo.py` 또는 Databricks 파일 시스템에 저장된 파일에 대한 절대 경로를 지정합니다.

<br/>  

- **메시지**:`{0} LinkedService should have domain and accessToken as required properties.`

- **원인**:`Bad authoring.`

- **권장 사항**: [연결된 서비스 정의를](compute-linked-services.md#azure-databricks-linked-service)확인합니다.

<br/>  

- **메시지**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **원인**:`Bad authoring.`

- **권장 사항**: [연결된 서비스 정의를](compute-linked-services.md#azure-databricks-linked-service)확인합니다.

<br/>  

- **메시지**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **원인**:`Bad authoring.`

- **권장 사항**: 오류 메시지를 참조하십시오.

<br/>

### <a name="error-code--3202"></a>오류 코드: 3202

- **메시지**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **원인**:`Too many Databricks runs in an hour.`

- **권장 사항**: 이 Databricks 작업 영역을 사용하는 모든 파이프라인을 작업 생성 률에 대해 확인합니다.  파이프라인이 너무 많은 Databricks가 집계로 실행되는 경우 일부 파이프라인을 새 작업 영역으로 마이그레이션합니다.

<br/>  

- **메시지**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **원인**:`Authoring error: No value provided for the parameter.`

- **권장 사항**: 파이프라인 JSON을 검사하고 baseParameters 노트북의 모든 매개 변수가 비어 없는 값을 지정하도록 합니다.

<br/>  

- **메시지** `User: `: SimpleUserContext{userId=...,user@company.com이름= , orgId=...}` is not   authorized to access cluster.`

- **원인**: 액세스 토큰을 생성 한 사용자는 연결된 서비스에 지정된 Databricks 클러스터에 액세스 할 수 없습니다.

- **권장 사항**: 사용자가 작업 영역에 필요한 권한이 있는지 확인합니다.


### <a name="error-code--3203"></a>오류 코드: 3203

- **메시지**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **원인**: 클러스터가 종료되었습니다. 대화형 클러스터의 경우 경합 조건일 수 있습니다.

- **권장 사항**: 이 오류를 방지하는 가장 좋은 방법은 작업 클러스터를 사용하는 것입니다.


### <a name="error-code--3204"></a>오류 코드: 3204

- **메시지**:`Job execution failed.`

- **원인**: 오류 메시지는 예기치 않은 클러스터 상태 또는 특정 활동과 같은 다양한 문제를 나타냅니다. 대부분의 경우 오류 메시지가 전혀 나타나지 않습니다.

- **권장 사항**: 해당/A
            

## <a name="azure-data-lake-analytics"></a>Azure 데이터 레이크 분석

다음 표는 U-SQL에 적용됩니다.
      
### <a name="error-code--2709"></a>오류 코드: 2709

- **메시지**:`The access token is from the wrong tenant.`

- **원인**: 잘못된 Azure Active Directory(Azure AD) 테넌트입니다.

- **권장 사항**: 잘못된 Azure Active Directory(Azure AD) 테넌트입니다.

<br/>

- **메시지**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **원인**: 이 오류는 데이터 레이크 분석의 제한으로 인해 발생합니다.

- **권장 사항**: 데이터 팩터리 트리거 및 활동에 대한 동시성 설정을 변경하여 Data Lake Analytics에 제출된 작업 수를 줄입니다. 또는 데이터 레이크 분석의 제한을 늘릴 수 있습니다.

<br/>  

- **메시지**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **원인**: 이 오류는 데이터 레이크 분석의 제한으로 인해 발생합니다.

- **권장 사항**: 데이터 팩터리 트리거 및 활동에 대한 동시성 설정을 변경하여 Data Lake Analytics에 제출된 작업 수를 줄입니다. 또는 데이터 레이크 분석의 제한을 늘릴 수 있습니다.


### <a name="error-code--2705"></a>오류 코드: 2705

- **메시지**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소의 파일에 액세스할 수 없습니다.

- **권장 사항**: 사용자가 Data Lake Analytics 작업에 대해 제공하는 서비스 주체 또는 인증서가 루트 폴더의 데이터 레이크 분석 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인합니다.


### <a name="error-code--2711"></a>오류 코드: 2711

- **메시지**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소의 파일에 액세스할 수 없습니다.

- **권장 사항**: 사용자가 Data Lake Analytics 작업에 대해 제공하는 서비스 주체 또는 인증서가 루트 폴더의 데이터 레이크 분석 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인합니다.

<br/>  

- **메시지**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **원인**: U-SQL 파일에 대한 경로가 잘못되었거나 연결된 서비스 자격 증명에 액세스 권한이 없습니다.

- **권장 사항**: 연결된 서비스에 제공된 경로 및 자격 증명을 확인합니다.


### <a name="error-code--2704"></a>오류 코드: 2704

- **메시지**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소의 파일에 액세스할 수 없습니다.

- **권장 사항**: 사용자가 Data Lake Analytics 작업에 대해 제공하는 서비스 주체 또는 인증서가 루트 폴더의 데이터 레이크 분석 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인합니다.


### <a name="error-code--2707"></a>오류 코드: 2707

- **메시지**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **원인**: 연결된 서비스의 데이터 레이크 분석 계정이 잘못되었습니다.

- **권장 사항**: 올바른 계정이 제공되었는지 확인합니다.


### <a name="error-code--2703"></a>오류 코드: 2703

- **메시지**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- 원인 : 데이터 레이크 분석에서 오류가 **발생했습니다.**

- **권장 사항**: 예제와 같은 오류는 작업이 Data Lake Analytics에 제출되었고 스크립트가 실패했으므로 실패했습니다. 데이터 레이크 분석에서 조사합니다. 포털에서 Data Lake Analytics 계정으로 이동하여 파이프라인 실행 ID가 아닌 데이터 팩터리 작업 실행 ID를 사용하여 작업을 찾습니다. 이 작업은 오류에 대한 자세한 정보를 제공하며 문제 해결에 도움이 됩니다. 해결 방법이 명확하지 않은 경우 Data Lake Analytics 지원 팀에 문의하여 계정 이름과 작업 ID가 포함된 작업 URL을 제공합니다.
          

## <a name="azure-functions"></a>Azure 기능

### <a name="error-code--3602"></a>오류 코드: 3602

- **메시지**:`Invalid HttpMethod: '%method;'.`

- **원인**: 활동 페이로드에 지정된 http 메서드는 Azure Function Activity에서 지원되지 않습니다.

- **권장 사항**: 지원되는 Http 메서드는 PUT, POST, GET, DELETE, 옵션, HEAD 및 TRACE입니다.


### <a name="error-code--3603"></a>오류 코드: 3603

- **메시지**:`Response Content is not a valid JObject.`

- **원인**: 호출된 Azure 함수가 응답에서 JSON 페이로드를 반환하지 않았습니다. ADF Azure 함수 활동은 JSON 응답 콘텐츠만 지원합니다.

- **권장 사항**: 유효한 JSON 페이로드를 반환하는 Azure 함수 업데이트(예: C# 함수가 반환할\"\"수 있음)새 OkObjectResult("{Id\"\": 123}");


### <a name="error-code--3606"></a>오류 코드: 3606

- **메시지**: Azure 함수 활동 누락 기능 키입니다.

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 'functionKey'라는 속성이 있는지 확인하십시오.


### <a name="error-code--3607"></a>오류 코드: 3607

- **메시지**:`Azure function activity missing function name.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 'functionName'라는 속성이 있는지 확인하십시오.


### <a name="error-code--3608"></a>오류 코드: 3608

- **메시지**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **원인**: 활동 정의의 Azure 함수 세부 정보가 올바르지 않을 수 있습니다.

- **권장 사항**: azure 함수 세부 정보를 수정하고 다시 시도합니다.


### <a name="error-code--3609"></a>오류 코드: 3609

- **메시지**:`Azure function activity missing functionAppUrl.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의는 'functionAppUrl'라는 속성이 있는지 확인하십시오.


### <a name="error-code--3610"></a>오류 코드: 3610

- **메시지**:`There was an error while calling endpoint.`

- **원인**: 기능 URL이 올바르지 않을 수 있습니다.

- **권장 사항**: 활동 JSON의 'functionAppUrl'에 대한 값이 올바른지 확인하고 다시 시도하십시오.


### <a name="error-code--3611"></a>오류 코드: 3611

- **메시지**:`Azure function activity missing Method in JSON.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 '메서드'라는 속성이 있는지 확인하십시오.


### <a name="error-code--3612"></a>오류 코드: 3612

- **메시지**:`Azure function activity missing LinkedService definition in JSON.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의가 서비스 세부 정보를 연결했는지 확인하십시오.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>오류 코드: 4101

- **메시지**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **원인**: 잘못된 형식 또는 속성 '%propertyName;'의 정의가 누락되었습니다.

- **권장 사항**: 활동 '%activityName;'에 속성 '%propertyName;' 올바른 데이터로 정의된 것이 있는지 확인하십시오.


### <a name="error-code--4110"></a>오류 코드: 4110

- **메시지**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **원인**: AzureMLExecute파이프라인 활동 정의가 완전하지 않습니다.

- **권장 사항**: 입력 AzureMLExecutePipeline 활동 JSON 정의가 서비스 세부 정보를 연결했는지 확인하십시오.


### <a name="error-code--4111"></a>오류 코드: 4111

- **메시지**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **원인**: 잘못된 활동 정의.

- **권장 사항**: 입력 AzureMLExecutePipeline 활동 JSON 정의가 올바른 링크된 서비스 세부 정보가 있는지 확인하십시오.


### <a name="error-code--4112"></a>오류 코드: 4112

- **메시지**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **원인**: 잘못된 형식 또는 속성 '%propertyName;'의 정의가 누락되었습니다.

- **권장 사항**: 연결된 서비스에 올바른 데이터로 정의된 속성 '%propertyName;'가 있는지 확인하십시오.


### <a name="error-code--4121"></a>오류 코드: 4121

- **메시지**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure 기계 학습에 액세스하는 데 사용되는 자격 증명이 만료되었습니다.

- **권장 사항**: 자격 증명이 유효한지 확인하고 다시 시도하십시오.


### <a name="error-code--4122"></a>오류 코드: 4122

- **메시지**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure 기계 학습 연결된 서비스에서 제공된 자격 증명이 잘못되었거나 작업에 대한 권한이 없습니다.

- **권장 사항**: 연결된 서비스의 자격 증명이 유효하고 Azure 기계 학습에 액세스할 수 있는 권한이 있는지 확인하십시오.


### <a name="error-code--4123"></a>오류 코드: 4123

- **메시지**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: 파이프라인매개 변수와 같은 활동의 속성은 Azure ML 파이프라인에 대해 유효하지 않습니다.

- **권장 사항**: 연결된 서비스에 지정된 게시된 Azure ML 파이프라인의 예상 페이로드와 일치하도록 활동 속성의 값을 확인하십시오.


### <a name="error-code--4124"></a>오류 코드: 4124

- **메시지**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: 게시된 Azure ML 파이프라인 끝점이 없습니다.

- **권장 사항**: 연결된 서비스에 지정된 게시된 Azure 기계 학습 파이프라인 끝점이 Azure 기계 학습에 있는지 확인하십시오.


### <a name="error-code--4125"></a>오류 코드: 4125

- **메시지**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure 기계 학습의 서버 오류입니다.

- **권장 사항**: 나중에 다시 시도하십시오. 문제가 남아 있는 경우 Azure 기계 학습 팀에 문의하여 도움을 요청하십시오.


### <a name="error-code--4126"></a>오류 코드: 4126

- **메시지**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **원인**: Azure ML 파이프라인 실행이 실패했습니다.

- **권장 사항**: Azure 기계 학습에서 더 많은 오류 로그를 확인하고 ML 파이프라인을 수정하십시오.



## <a name="common"></a>일반

### <a name="error-code--2103"></a>오류 코드: 2103

- **메시지**:`Please provide value for the required property '%propertyName;'.`

- **원인**: 속성에 대한 값이 제공되지 않았지만 시나리오에서 필요합니다.

- **권장 사항**: 메시지의 값을 제공하고 다시 시도하십시오.


### <a name="error-code--2104"></a>오류 코드: 2104

- **메시지**:`The type of the property '%propertyName;' is incorrect.`

- **원인**: 제공된 속성의 형식이 예상대로 되지 않습니다.

- **권장 사항**: 속성의 유형을 수정하고 다시 시도하십시오.


### <a name="error-code--2105"></a>오류 코드: 2105

- **메시지**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **원인**: 속성 값이 잘못되었거나 예상 형식이 없습니다.

- **권장 사항**: 속성에 대한 설명서를 조회하고 제공된 값이 예상 형식 및 유형인지 확인하십시오.


### <a name="error-code--2106"></a>오류 코드: 2106

- **메시지**:`The storage connection string is invalid. %errorMessage;`

- **원인**: 저장소의 연결 문자열이 잘못되었거나 형식이 잘못되었습니다.

- **권장 사항**: Azure 포털로 이동하여 저장소를 찾고 연결 문자열을 복사하고 연결된 서비스에 붙여 넣은 다음 다시 시도하십시오.


### <a name="error-code--2108"></a>오류 코드: 2108

- **메시지**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 설정과 같은 근본적인 문제로 인해 요청이 실패했습니다.

- **권장 사항**: Fiddler/Postman을 사용하여 요청의 유효성을 검사합니다.


### <a name="error-code--2110"></a>오류 코드: 2110

- **메시지**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **원인**: 활동에 지정된 연결된 서비스가 잘못되었습니다.

- **권장 사항**: 연결된 서비스 유형이 활동에 대해 지원되는 유형 중 하나인지 확인하십시오. 예를 들어 HDI 활동의 경우 연결된 서비스 유형은 HDInsight 또는 HDInsightOnDemand일 수 있습니다.


### <a name="error-code--2111"></a>오류 코드: 2111

- **메시지**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **원인**: 제공된 속성의 형식이 예상대로 되지 않습니다.

- **권장 사항**: 속성의 유형을 수정하고 다시 시도하십시오.


### <a name="error-code--2112"></a>오류 코드: 2112

- **메시지**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **원인**: 클라우드 유형이 지원되지 않거나 EndpointSuffix에서 저장소에 대해 확인할 수 없습니다.

- **권장 사항**: 다른 클라우드의 저장소를 사용하고 다시 시도하십시오.


### <a name="error-code--2128"></a>오류 코드: 2128

- **메시지**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 시간.

- **권장 사항**: 누르려는 끝점이 요청에 응답하고 있는지 확인합니다. 당신은 바이올린 / 우체부와 같은 도구를 사용할 수 있습니다.



## <a name="custom"></a>사용자 지정

다음 표는 Azure 일괄 처리에 적용됩니다.
      
### <a name="error-code--2500"></a>오류 코드: 2500

- **메시지**:`Hit unexpected exception and execution failed.`

- **원인**:`Can't launch command, or the program returned an error code.`

- **권장 사항**: 실행 파일이 있는지 확인합니다. 프로그램이 시작된 경우 *stdout.txt* 및 *stderr.txt가* 저장소 계정에 업로드되었는지 확인하십시오. 디버깅을 위해 코드에서 풍부한 로그를 내하는 것이 좋습니다.


### <a name="error-code--2501"></a>오류 코드: 2501

- **메시지**:`Cannot access user batch account; please check batch account settings.`

- **원인**: 잘못된 일괄 처리 액세스 키 또는 풀 이름.

- **권장 사항**: 연결된 서비스에서 풀 이름과 일괄 처리 액세스 키를 확인합니다.


### <a name="error-code--2502"></a>오류 코드: 2502

- **메시지**:`Cannot access user storage account; please check storage account settings.`

- **원인**: 잘못된 저장소 계정 이름 또는 액세스 키.

- **권장 사항**: 연결된 서비스의 저장소 계정 이름과 액세스 키를 확인합니다.


### <a name="error-code--2504"></a>오류 코드: 2504

- **메시지**:`Operation returned an invalid status code 'BadRequest'.`

- **원인**: 폴더에 너무 많은 파일사용자 정의 활동의 경로. 리소스파일의 총 크기는 32,768자를 초과할 수 없습니다.

- **권장 사항**: 불필요한 파일을 제거합니다. 또는 압축을 압축하고 압축 해제 명령을 추가하여 추출합니다. 예를 들어,`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>오류 코드: 2505

- **메시지**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **원인**: 사용자 지정 활동은 액세스 키를 사용하는 저장소 계정만 지원합니다.

- **권장 사항**: 오류 설명을 참조하십시오.


### <a name="error-code--2507"></a>오류 코드: 2507

- **메시지**:`The folder path does not exist or is empty: ...`

- **원인**: 지정된 경로의 저장소 계정에 파일이 없습니다.

- **권장 사항:** 폴더 경로에는 실행하려는 실행 파일이 포함되어야 합니다.


### <a name="error-code--2508"></a>오류 코드: 2508

- **메시지**:`There are duplicate files in the resource folder.`

- **원인**: 같은 이름의 여러 파일이 folderPath의 다른 하위 폴더에 있습니다.

- **권장 사항**: 사용자 지정 활동은 folderPath 에서 폴더 구조를 평평하게 합니다.  폴더 구조를 유지해야 하는 경우 압축 해제 명령을 사용하여 파일을 압축하고 Azure Batch에서 추출합니다. 예를 들어,`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>오류 코드: 2509

- **메시지**:`Batch   url ... is invalid; it must be in Uri format.`

- **원인**: 일괄 URL은`https://mybatchaccount.eastus.batch.azure.com`

- **권장 사항**: 오류 설명을 참조하십시오.


### <a name="error-code--2510"></a>오류 코드: 2510

- **메시지**:`An   error occurred while sending the request.`

- **원인**: 일괄 처리 URL이 잘못되었습니다.

- **권장 사항**: 일괄 처리 URL을 확인합니다.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>오류 코드: 200

- **메시지**:`Unexpected error happened: '%error;'.`

- **원인**: 내부 서비스 문제가 있습니다.

- **권장 사항**: 추가 지원은 ADF 지원에 문의하십시오.


### <a name="error-code--201"></a>오류 코드: 201

- **메시지**:`JobType %jobType; is not found.`

- **원인**: ADF에서 지원하지 않는 새 작업 유형이 있습니다.

- **권장 사항**: ADF 지원 팀에 문의하여 추가 지원을 받으십시오.


### <a name="error-code--202"></a>오류 코드: 202

- **메시지**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 오류 메시지에 무엇이 잘못되었는지에 대한 세부 정보가 표시되어야 합니다.

- **권장 사항:** 오류 메시지는 문제를 해결하는 데 도움이 됩니다. 정보가 충분하지 않은 경우 ADF 지원에 문의하여 추가 도움을 받으십시오.


### <a name="error-code--203"></a>오류 코드: 203

- **메시지**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 오류 메시지에 무엇이 잘못되었는지에 대한 세부 정보가 표시되어야 합니다.

- **권장 사항:** 오류 메시지는 문제를 해결하는 데 도움이 됩니다. 정보가 충분하지 않은 경우 ADF 지원에 문의하여 추가 도움을 받으십시오.


### <a name="error-code--204"></a>오류 코드: 204

- **메시지**:`The resumption token is missing for runId '%runId;'.`

- **원인**: 내부 서비스 문제가 있습니다.

- **권장 사항**: 추가 지원은 ADF 지원에 문의하십시오.


### <a name="error-code--205"></a>오류 코드: 205

- **메시지**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **원인**: 주문형 HDI 클러스터를 만들 때 오류가 발생했습니다.

- **권장 사항**: 추가 지원은 ADF 지원에 문의하십시오.


### <a name="error-code--206"></a>오류 코드: 206

- **메시지**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **원인**: 이 문제를 일으킨 서비스에 내부 문제가 발생했습니다.

- **권장 사항**: 일시적인 문제일 수 있습니다. 작업을 다시 시도하고 문제가 지속되면 ADF 지원에 문의하여 추가 지원을 받으십시오.


### <a name="error-code--207"></a>오류 코드: 207

- **메시지**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **원인**: 기본 저장소 계정에서 지역을 확인 하 고 있는 동안 내부 오류가 발생 했습니다.

- **권장 사항**: 다른 저장소를 사용해 보십시오. 허용되는 솔루션이 아닌 경우 ADF 지원 팀에 문의하여 추가 지원을 받으하십시오.


### <a name="error-code--208"></a>오류 코드: 208

- **메시지**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **원인**: 서비스 주체를 읽거나 MSI 인증을 인스턴스화하는 동안 내부 오류가 발생했습니다.

- **권장 사항**: 제공된 구독에서 HDInsight 클러스터를 만들고 다시 시도할 수 있는 권한이 있는 서비스 주체를 제공하는 것이 좋습니다. 허용되는 솔루션이 아닌 경우 ADF 지원 팀에 문의하여 추가 지원을 받으려면.


### <a name="error-code--2300"></a>오류 코드: 2300

- **메시지**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **원인**: 오류 메시지에 '원격 이름을 확인할 수 없습니다.'와 유사한 메시지가 포함되어 있으면 제공된 클러스터 URI가 유효하지 않음을 의미할 수 있습니다.


- **권장 사항**: 클러스터가 삭제되지 않았는지, 제공된 URI가 올바른지 확인합니다. 브라우저에서 URI를 열면 Ambari UI가 표시됩니다. 클러스터가 가상 네트워크에 있는 경우 URI는 개인 URI여야 합니다. 열려면 동일한 가상 네트워크의 일부인 VM을 사용합니다. 자세한 내용은 [this](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)를 참조하세요.
                  

<br>

- **원인**: 오류 메시지에 '작업이 취소되었습니다.'와 유사한 메시지가 포함되어 있으면 작업 제출 시간이 시간 중지됩니다.

- **권장 사항**: 문제는 일반적인 HDInsight 연결 또는 네트워크 연결일 수 있습니다. 먼저 HDInsight 암바리 UI는 모든 브라우저에서 사용할 수 있는지 확인합니다. 자격 증명이 여전히 유효한지 확인합니다. 자체 호스팅 통합 런타임(IR)을 사용하는 경우 자체 호스팅 IR이 설치된 VM 또는 컴퓨터에서 이 작업을 수행해야 합니다. 그런 다음 데이터 팩터리에서 작업을 다시 제출해 봅을 시도합니다. 그래도 실패하면 데이터 팩터리 팀에 문의하여 지원을 요청하십시오.

<br>

- **원인**: 오류 메시지에 '사용자 관리자가 Ambari에서 잠겨 있습니다' 또는 '무단: Ambari 사용자 이름 또는 암호가 올바르지 않음'과 유사한 메시지가 포함된 경우 HDInsight의 자격 증명이 잘못되었거나 만료되었습니다.

- **권장 사항**: 자격 증명을 수정하고 연결된 서비스를 다시 배포합니다. 먼저 모든 브라우저에서 클러스터 URI를 열고 로그인을 시도하여 자격 증명이 HDInsight에서 작동하는지 확인합니다. 자격 증명이 작동하지 않으면 Azure 포털에서 자격 증명을 재설정할 수 있습니다.

<br>

- **원인**: 오류 메시지에 '502 - 웹 서버가 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 받은 경우 HDInsight 서비스에서 이 오류가 반환됩니다.


- **권장 사항**: Azure HDInsight 문제 해결 https://hdinsight.github.io/ambari/ambari-ui-502-error.html https://hdinsight.github.io/spark/spark-thriftserver-errors.html설명서(예: . . https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502
                  

<br>

- **원인**: 템플턴 서비스가 너무 많은 제출 작업 요청으로 바쁜 '템플턴 서비스가 너무 많은 작업 요청을 수행 할 수 없습니다'또는 '큐 root.joblauncher이미 500 응용 프로그램을 가지고, 응용 프로그램의 제출을 수락 할 수 없습니다'와 유사한 메시지가 포함되어 있는 경우, 이는 너무 많은 작업이 동시에 HDInsight에 제출되는 것을 의미한다.

- **권장 사항**: HDInsight에 제출된 동시 작업 수를 제한하는 것이 좋습니다. 동일한 활동으로 작업이 제출되는 경우 데이터 팩터리 활동 동시성을 참조하십시오. 동시 파이프라인 실행이 시간이 지남에 따라 분산되도록 트리거를 변경합니다. 오류에서 알 수 있듯이 TEMPLEton.parallellism.job.submit를 조정하려면 HDInsight 문서를 참조하십시오.


### <a name="error-code--2301"></a>오류 코드: 2301

- **메시지**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **원인**: HDInsight 클러스터 또는 서비스에 문제가 있습니다.


- **권장 사항**: 이 오류는 ADF가 실행 중인 작업의 상태를 얻으려고 시도할 때 HDInsight 클러스터에서 응답을 받지 못하는 경우에 발생합니다. 클러스터 자체에 문제가 발생하거나 HDInsight 서비스가 중단될 수 있습니다. 에서 HDInsight 문제 해결 https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide문서를 참조하거나 추가 지원을 받으려면 해당 지원팀에 문의하십시오.
                


### <a name="error-code--2302"></a>오류 코드: 2302

- **메시지**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **원인**: 작업이 HDI 클러스터에 제출되어 실패했습니다.

- **권장 사항**: 활동 실행 출력에서 원사 로그 링크를 따라 HDI 출력의 오류를 찾습니다. 필요한 경우 HDInsight 팀에 문의하여 지원을 요청하십시오.


### <a name="error-code--2303"></a>오류 코드: 2303

- **메시지**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **원인**: 작업이 HDI 클러스터에 제출되어 실패했습니다.

- **권장 사항**: 활동 실행 출력에서 원사 로그 링크를 따라 HDI 출력의 오류를 찾습니다. 다시 시도하거나 필요한 경우 HDInsight 팀에 문의하여 지원을 요청하십시오.


### <a name="error-code--2304"></a>오류 코드: 2304

- **메시지**:`MSI authentication is not supported on storages for HDI activities.`

- **원인**: HDI 링크 서비스 또는 HDI 활동에 사용되는 스토리지 링크 서비스는 지원되지 않는 MSI 인증으로 구성됩니다.

- **권장 사항**: HDI 연결 서비스 또는 HDI 활동에 사용되는 저장소 계정에 대한 전체 연결 문자열을 제공하십시오.


### <a name="error-code--2305"></a>오류 코드: 2305

- **메시지**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **원인**: HDI 클러스터의 연결 정보가 올바르지 않거나, 제공된 사용자에게 필요한 작업을 수행할 권한이 없거나, HDInsight 서비스가 ADF의 요청에 응답하는 데 문제가 있습니다.

- **권장 사항**: 사용자 정보가 올바른지 확인하십시오. 또한 자체 호스팅 IR의 경우 IR이 설치된 VM의 브라우저에서 HDI 클러스터의 Ambari UI를 열거나 Azure IR의 경우 모든 컴퓨터에서 열 수 있는지 확인합니다.


### <a name="error-code--2306"></a>오류 코드: 2306

- **메시지**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **원인**: 스크립트 작업에 대해 제공된 json이 잘못되었습니다.


- **권장 사항:** 오류 메시지가 문제를 식별하는 데 도움이 됩니다. json 구성을 수정하고 다시 시도하십시오. 자세한 https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service 내용은 확인하십시오.
                


### <a name="error-code--2310"></a>오류 코드: 2310

- **메시지**:`Failed to submit Spark job. Error: '%message;'`

- **원인**: ADF는 Livy API(livy/batch)를 사용하여 Spark 클러스터에서 일괄 처리를 만들려고 했지만 오류가 발생했습니다.

- **권장 사항**: 오류 메시지를 따라 문제를 해결하십시오. 해결할 정보가 충분하지 않은 경우 HDI 팀에 문의하여 ADF 모니터링 페이지에서 출력을 실행하는 활동에서 찾을 수 있는 일괄 ID 및 작업 ID를 제공합니다.


### <a name="error-code--2312"></a>오류 코드: 2312

- **메시지**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **원인**: HDInsight 스파크 클러스터에서 작업이 실패했습니다.

- **권장 사항**: 활동 실행의 링크를 따라 ADF 모니터링 페이지에서 HDInsight 스파크 클러스터에서 실행 문제를 해결하십시오. 자세한 내용은 HDInsight 지원 팀에 문의하십시오.


### <a name="error-code--2313"></a>오류 코드: 2313

- **메시지**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **원인**: HDInsight 스파크 클러스터에서 일괄 처리가 삭제되었습니다.

- 권장 사항 : HDInsight 스파크 클러스터의 일괄 처리 문제를 **해결합니다.** 추가 지원을 받으시면 HDInsight 지원에 문의하십시오. 


### <a name="error-code--2328"></a>오류 코드: 2328

- **메시지**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **원인**:`The error message should show the details of what went wrong.`

- **권장 사항:** 오류 메시지는 문제를 해결하는 데 도움이 됩니다.


### <a name="error-code--2329"></a>오류 코드: 2329

- **메시지**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 오류 메시지에 무엇이 잘못되었는지에 대한 세부 정보가 표시되어야 합니다.

- **권장 사항:** 오류 메시지는 문제를 해결하는 데 도움이 됩니다.


### <a name="error-code--2331"></a>오류 코드: 2331

- **메시지**:`The file path should not be null or empty.`

- **원인**: 제공된 파일 경로가 비어 있습니다.

- **권장 사항**: 존재하는 파일에 대한 경로를 제공하십시오.


### <a name="error-code--2340"></a>오류 코드: 2340

- **메시지**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **원인**: HDInsightOnDemand 연결 서비스는 셀프호스트 IR을 통한 실행을 지원하지 않습니다.

- **권장 사항**: Azure IR을 선택하고 다시 시도하십시오.


### <a name="error-code--2341"></a>오류 코드: 2341

- **메시지**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **원인**: 제공된 URL이 올바른 형식이 아닙니다.

- **권장 사항**: 클러스터 URL을 수정하고 다시 시도하십시오.


### <a name="error-code--2342"></a>오류 코드: 2342

- **메시지**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **원인**: 제공된 자격 증명이 클러스터에 잘못되었거나 네트워크 구성 또는 연결 문제가 있거나 IR에 클러스터 연결에 문제가 있는 경우

- **권장 사항**:  
      1. 브라우저에서 HDInsight 클러스터의 Ambari UI를 열어 자격 증명이 올바른지 확인합니다.
      2. 클러스터가 VNet에 있고 자체 호스팅 IR이 사용 중인 경우 HDI URL은 VNet의 개인 URL이어야 하며, 이는 클러스터 이름 옆에 '-int'가 있어야 한다는 것을 의미합니다. 예를 들어https://mycluster.azurehdinsight.net/" " "는 "로https://mycluster-int.azurehdinsight.net/변경되어야 합니다.
      2. 클러스터가 VNet에 있는 경우 자체 호스팅 IR이 사용되고 개인 URL이 사용되었으며 연결이 여전히 실패한 경우 IR이 설치된 VM에 HDI연결에 문제가 발생했습니다. IR이 설치된 VM에 연결하고 브라우저에서 Ambari UI를 엽니다. 클러스터에 대한 개인 URL을 사용합니다. 이 연결은 브라우저에서 작동합니다. 그렇지 않은 경우 HDInsight 지원 팀에 문의하여 추가 지원을 받으십시오.
      3. 자체 호스팅 IR을 사용하지 않는 경우 HDI 클러스터에 공개적으로 액세스할 수 있어야 합니다. 브라우저에서 Ambari UI를 열고 열리도록 합니다. 클러스터 또는 클러스터에 있는 서비스에 문제가 있는 경우 HDInsight 지원 팀에 문의하여 도움을 받으십시오.
      따라서 일반적으로 ADF 연결 서비스에 사용되는 HDI 클러스터 URL은 테스트 연결이 통과하기 위해 ADF IR(자체 호스팅 또는 Azure)에 액세스하고 실행이 작동하려면 액세스할 수 있어야 합니다. VM 또는 공용 컴퓨터에서 브라우저에서 해당 URL을 열어 쉽게 확인할 수 있습니다.
    


### <a name="error-code--2343"></a>오류 코드: 2343

- **메시지**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **원인**: 사용자 이름 또는 암호가 비어 있습니다.

- **권장 사항**: HDI에 연결하고 다시 시도할 수 있는 올바른 자격 증명을 제공합니다.


### <a name="error-code--2345"></a>오류 코드: 2345

- **메시지**:`Failed to read the content of the hive script. Error: '%message;'`

- **원인**: 스크립트 파일이 없거나 ADF가 스크립트 위치에 연결할 수 없습니다.

- **권장 사항**: 스크립트가 있는지 확인하고 연결된 연결된 서비스에 연결에 적합한 자격 증명이 있는지 확인하십시오.


### <a name="error-code--2346"></a>오류 코드: 2346

- **메시지**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **원인**: ADF가 HDI 클러스터에 대한 ODBC 연결을 설정하려고 했지만 오류가 발생했습니다.

- **권장 사항**: 오류 메시지 및 오류 코드는 ODBC 연결 오류 문제를 해결하는 데 도움이 됩니다. 문제를 해결하기에 충분하지 않은 경우 Azure HDInsight 팀에 문의하여 지원을 받으십시오.


### <a name="error-code--2347"></a>오류 코드: 2347

- **메시지**:`Hive execution through ODBC failed with error message '%message;'.`

- **원인**: ADF는 ODBC 연결을 통해 HDI 클러스터에 실행을 위해 하이브 스크립트를 제출하고 스크립트가 HDI에서 실패했습니다.

- **권장 사항**: HDI 클러스터에서 하이브 스크립트 실행이 실패하고 오류 메시지 및 오류 코드가 문제 해결에 도움이 됩니다. 문제를 해결하기에 충분하지 않은 경우 Azure HDInsight 팀에 문의하여 지원을 받으십시오.


### <a name="error-code--2348"></a>오류 코드: 2348

- **메시지**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **원인**: 저장소에 연결된 서비스 속성이 올바르게 설정되지 않았습니다.

- **권장 사항**: HDI 활동에 대한 기본 저장소 연결 서비스에서전체 연결 문자열만 지원됩니다. MSI 인증 또는 응용 프로그램을 사용하지 않는지 확인하십시오.


### <a name="error-code--2350"></a>오류 코드: 2350

- **메시지**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **원인**: 파일이 있어야 하는 저장소에 연결하기 위해 제공된 자격 증명이 올바르지 않거나 파일이 존재하지 않습니다.

- **권장 사항**: ADF가 HDI 활동에 대한 준비 단계를 수행할 때 이 오류가 발생합니다. 작업을 HDI에 제출하기 전에 파일을 기본 저장소에 복사하려고 합니다. 제공된 위치에 파일이 있는지 확인하여 저장소 연결이 올바른지 확인합니다. ADF HDI 활동은 HDI 활동과 관련된 저장소 계정에 대한 MSI 인증을 지원하지 않으므로 연결된 서비스에 전체 키가 있거나 Azure 키 볼트를 사용하고 있는지 확인합니다.


### <a name="error-code--2351"></a>오류 코드: 2351

- **메시지**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **원인**: 파일이 지정된 경로에 없습니다.

- **권장 사항**: 파일이 실제로 존재하는지 확인하고 이 파일을 가리키는 연결 정보가 있는 연결된 서비스에 올바른 자격 증명이 있는지 확인하십시오.


### <a name="error-code--2352"></a>오류 코드: 2352

- **메시지**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **원인**: 파일 저장소 에 연결된 서비스 속성이 올바르게 설정되지 않았습니다.

- **권장 사항**: 파일 저장소 링크 서비스의 속성이 제대로 구성되었는지 확인하십시오.


### <a name="error-code--2353"></a>오류 코드: 2353

- **메시지**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **원인**: 스크립트 저장소 연결 서비스 속성이 올바르게 설정되지 않았습니다.

- **권장 사항**: 스크립트 저장소 링크 서비스의 속성이 제대로 구성되었는지 확인하십시오.


### <a name="error-code--2354"></a>오류 코드: 2354

- **메시지**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **원인**: 저장소 연결 서비스 유형은 활동에서 지원되지 않습니다.

- **권장 사항**: 선택한 연결된 서비스에 활동에 대해 지원되는 유형 중 하나가 있는지 확인하십시오. HDI 활동은 AzureBlobStorage 및 AzureBlobFSStorage 연결 서비스를 지원합니다.


### <a name="error-code--2355"></a>오류 코드: 2355

- **메시지**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **원인**: 명령환경에 대해 제공된 환경이 올바르지 않습니다.

- **권장 사항**:  
      제공된 값이 \"비슷해야 합니다: commandEnvironment\": \"[ 변수Name=변수값]\" 그리고 각 변수는 목록에 한 번만 나타납니다.
    


### <a name="error-code--2356"></a>오류 코드: 2356

- **메시지**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **원인**: 변수가 명령 환경에서 두 번 제공되었습니다환경 .

- **권장 사항**:  
      제공된 값이 \"비슷해야 합니다: commandEnvironment\": \"[ 변수Name=변수값]\" 그리고 각 변수는 목록에 한 번만 나타납니다.
    


### <a name="error-code--2357"></a>오류 코드: 2357

- **메시지**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **원인**: 제공된 자격 증명이 올바르지 않습니다.

- **권장 사항**: ADLS Gen 1 연결 서비스의 연결 정보를 확인하고 테스트 연결이 성공했는지 확인하십시오.


### <a name="error-code--2358"></a>오류 코드: 2358

- **메시지**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **원인**: 필요한 속성 'TimeToLive'에 대해 제공된 값이 잘못된 형식입니다. 

- **권장 사항**: 값을 제안된 범위에 있도록 업데이트하고 다시 시도하십시오.


### <a name="error-code--2359"></a>오류 코드: 2359

- **메시지**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **원인**: 속성 '역할'에 대해 제공된 값이 잘못되었습니다.

- **권장 사항**: 값을 제안 중 하나로 업데이트하고 다시 시도하십시오.


### <a name="error-code--2360"></a>오류 코드: 2360

- **메시지**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **원인**: HCatalogLinkedService에 대해 제공된 연결 문자열이 잘못되었습니다.

- **권장 사항**: 값을 올바른 Azure SQL 연결 문자열로 업데이트하고 다시 시도하십시오.


### <a name="error-code--2361"></a>오류 코드: 2361

- **메시지**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **원인**: 클러스터 생성에 실패했으며 ADF에서 HDInsight 서비스에서 오류가 발생하지 않았습니다.

- **권장 사항**: Azure 포털을 열고 제공된 이름이 있는 HDI 리소스를 찾고 프로비저닝 상태를 확인합니다. 추가 지원은 HDInsight 지원 팀에 문의하십시오.


### <a name="error-code--2362"></a>오류 코드: 2362

- **메시지**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **원인**: 제공된 추가 저장소가 Azure Blob 저장소가 아닙니다.

- **권장 사항**: AZURE Blob 저장소 계정을 주문형 요청 시 연결 서비스에 대한 추가 저장소로 제공합니다.



## <a name="web-activity"></a>웹 작업

### <a name="error-code--2128"></a>오류 코드: 2128

- **메시지**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 시간.

- **권장 사항**: 누르려는 끝점이 요청에 응답하고 있는지 확인합니다. 당신은 바이올린 / 우체부와 같은 도구를 사용할 수 있습니다.


### <a name="error-code--2108"></a>오류 코드: 2108

- **메시지**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 설정과 같은 근본적인 문제로 인해 요청이 실패했습니다.

- **권장 사항**: Fiddler/Postman을 사용하여 요청의 유효성을 검사합니다.
<br>


#### <a name="more-details"></a>자세한 내용
Fiddler를 사용하여 모니터링되는 웹 응용 프로그램의 HTTP 세션을 만들려면 다음을 수행하십시오.

1. 다운로드, 설치 및 오픈 [Fiddler](https://www.telerik.com/download/fiddler).

1. 웹 응용 프로그램이 HTTPS를 사용하는 경우 **도구** > **바이올린 옵션** > **HTTPS로**이동합니다. HTTPS CONNECT 캡처 및 **HTTPS 트래픽 암호 해독을** **선택합니다.**

   ![바이올린 옵션](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 응용 프로그램에서 TLS/SSL 인증서를 사용하는 경우 장치에 Fiddler 인증서를 추가합니다. **도구** > **Fiddler 옵션** > **HTTPS** > **작업** > **바탕 화면에 루트 인증서를 내보내기로**이동합니다.

1. **파일** > **캡처 트래픽으로**이동하여 캡처를 끕니다. 또는 **F12를**누릅니다.

1. 캐시된 모든 항목이 제거되고 다시 다운로드해야 할 수 있도록 브라우저의 캐시를 지웁니까.

1. 요청 만들기:

   1. 작곡가 탭을 **선택합니다.**

   1. HTTP 메서드 및 URL을 설정합니다.
   
   1. 필요한 경우 헤더와 요청 본문을 추가합니다.

   1. **실행**을 선택합니다.

1. 트래픽 캡처를 다시 켜고 페이지에서 문제가 있는 트랜잭션을 완료합니다.

1.  > **모든 세션을****저장하는** **파일로** > 이동합니다.

자세한 내용은 [Fiddler 를 시작하기](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)를 참조하십시오.

## <a name="next-steps"></a>다음 단계

문제 해결에 대한 자세한 도움말을 보려면 다음 리소스를 사용해 보십시오.

*  [데이터 팩토리 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [데이터 팩터리 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [데이터 팩터리용 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [데이터 팩토리에 대한 트위터 정보](https://twitter.com/hashtag/DataFactory)


            
