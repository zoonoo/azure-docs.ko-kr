---
title: Azure Data Factory 문제 해결 | Microsoft Docs
description: Azure Data Factory에서 외부 제어 작업의 문제를 해결 하는 방법을 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 45aa1354f6009d5eccd48f85f993bae8949139e3
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058966"
---
# <a name="troubleshoot-azure-data-factory"></a>문제 해결 Azure Data Factory

이 문서에서는 Azure Data Factory의 외부 제어 작업에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="connector-and-copy-activity"></a>커넥터 및 복사 작업

커넥터 문제 (예: 복사 작업을 사용 하 여 오류 발생)의 경우 [커넥터 Azure Data Factory 문제 해결](connector-troubleshoot-guide.md)을 참조 하세요.

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>오류 코드:  3200

- **메시지**: 오류 403.

- **원인**:`The Databricks access token has expired.`

- **권장 사항**: 기본적으로 Azure Databricks 액세스 토큰은 90 일 동안 유효 합니다. 새 토큰을 만들고 연결 된 서비스를 업데이트 합니다.


### <a name="error-code--3201"></a>오류 코드:  3201

- **메시지**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **원인**:`Bad authoring: Notebook path not specified correctly.`

- **권장 사항**: Databricks 활동에서 노트북 경로를 지정 합니다.

<br/>

- **메시지**:`Cluster   ... does not exist.`

- **원인**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **권장 사항**: Databricks 클러스터가 있는지 확인 합니다.

<br/>

- **메시지**:`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **원인**:`Bad authoring.`

- **권장 사항**: 작업 영역 주소 지정 스키마 또는 `dbfs:/folder/subfolder/foo.py` Databricks 파일 시스템에 저장 된 파일에 대 한 절대 경로를 지정 합니다.

<br/>

- **메시지**:`{0} LinkedService should have domain and accessToken as required properties.`

- **원인**:`Bad authoring.`

- **권장 사항**: 연결 된 [서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인 합니다.

<br/>

- **메시지**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **원인**:`Bad authoring.`

- **권장 사항**: 연결 된 [서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인 합니다.

<br/>

- **메시지**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **원인**:`Bad authoring.`

- **권장 사항**: 오류 메시지를 참조 하십시오. 

<br/>

### <a name="error-code--3202"></a>오류 코드:  3202

- **메시지**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **원인**:`Too many Databricks runs in an hour.`

- **권장 사항**: 이 Databricks 작업 영역을 사용 하는 모든 파이프라인에서 작업 만들기 빈도를 확인 합니다.  파이프라인이 너무 많은 Databricks를 시작한 경우에는 일부 파이프라인을 새 작업 영역으로 마이그레이션합니다.

<br/>

- **메시지**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **원인**:`Authoring error: No value provided for the parameter.`

- **권장 사항**: 파이프라인 JSON을 검사 하 고 baseParameters 노트북의 모든 매개 변수가 비어 있지 않은 값을 지정 하는지 확인 합니다.

<br/>

- **메시지**: `User: `SimpleUserContext {userId = ..., name =user@company.com, orgId = ...}` is not   authorized to access cluster.`

- **원인**: 액세스 토큰을 생성 한 사용자는 연결 된 서비스에 지정 된 Databricks 클러스터에 액세스할 수 없습니다.

- **권장 사항**: 작업 영역에서 사용자에 게 필요한 권한이 있는지 확인 합니다.


### <a name="error-code--3203"></a>오류 코드:  3203

- **메시지**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **원인**: 클러스터가 종료 되었습니다. 대화형 클러스터의 경우 경합 상태가 될 수 있습니다.

- **권장 사항**: 이 오류를 방지 하는 가장 좋은 방법은 작업 클러스터를 사용 하는 것입니다.


### <a name="error-code--3204"></a>오류 코드:  3204

- **메시지**:`Job execution failed.`

- **원인**:  오류 메시지는 예기치 않은 클러스터 상태 또는 특정 활동과 같은 다양 한 문제를 표시 합니다. 대부분 오류 메시지가 표시 되지 않는 경우가 많습니다. 

- **권장 사항**: 해당 사항 없음


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

다음 표는 U-SQL에 적용 됩니다.


### <a name="error-code--2709"></a>오류 코드:  2709

- **메시지**:`The access token is from the wrong tenant.`

- **원인**:  잘못 된 Azure Active Directory (Azure AD) 테 넌 트입니다.

- **권장 사항**: 잘못 된 Azure Active Directory (Azure AD) 테 넌 트입니다.

<br/>

- **메시지**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **원인**:  이 오류는 Data Lake Analytics에 대 한 제한으로 인해 발생 합니다.

- **권장 사항**: 작업에 대 한 Data Factory 트리거 및 동시성 설정을 변경 하 여 Data Lake Analytics 전송 된 작업의 수를 줄입니다. 또는 Data Lake Analytics에 대 한 제한을 늘립니다.

<br/>

- **메시지**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **원인**:  이 오류는 Data Lake Analytics에 대 한 제한으로 인해 발생 합니다. 

- **권장 사항**: 작업에 대 한 Data Factory 트리거 및 동시성 설정을 변경 하 여 Data Lake Analytics 전송 된 작업의 수를 줄입니다. 또는 Data Lake Analytics에 대 한 제한을 늘립니다.


### <a name="error-code--2705"></a>오류 코드:  2705

- **메시지**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **원인**:  서비스 사용자 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다.

- **권장 사항**: 사용자가 Data Lake Analytics 작업을 위해 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다.


### <a name="error-code--2711"></a>오류 코드:  2711

- **메시지**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **원인**:  서비스 사용자 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다.

- **권장 사항**: 사용자가 Data Lake Analytics 작업을 위해 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다.

<br/>

- **메시지**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **원인**:  U-SQL 파일의 경로가 잘못 되었거나 연결 된 서비스 자격 증명에 액세스 권한이 없습니다.

- **권장 사항**: 연결 된 서비스에서 제공 하는 경로 및 자격 증명을 확인 합니다.


### <a name="error-code--2704"></a>오류 코드:  2704

- **메시지**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **원인**:  서비스 사용자 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다.

- **권장 사항**: 사용자가 Data Lake Analytics 작업을 위해 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다.


### <a name="error-code--2707"></a>오류 코드:  2707

- **메시지**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **원인**:  연결 된 서비스의 Data Lake Analytics 계정이 잘못 되었습니다.

- **권장 사항**: 올바른 계정이 제공 되는지 확인 합니다.


### <a name="error-code--2703"></a>오류 코드:  2703

- **메시지**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **원인**:  Data Lake Analytics에서 오류가 발생 했습니다. 

- **권장 사항**: 예와 같은 오류는 작업이 Data Lake Analytics 전송 되었으며 스크립트가 실패 한 것을 의미 합니다. Data Lake Analytics를 조사 합니다. 포털에서 Data Lake Analytics 계정으로 이동 하 고 Data Factory 활동 실행 ID (파이프라인 실행 ID 아님)를 사용 하 여 작업을 찾습니다. 작업은 오류에 대 한 자세한 정보를 제공 하 고 문제를 해결 하는 데 도움이 됩니다. 해결 방법이 명확 하지 않은 경우 Data Lake Analytics 지원 팀에 문의 하 여 계정 이름 및 작업 ID를 포함 하는 작업 URL을 제공 합니다.



## <a name="azure-functions"></a>Azure Functions

### <a name="error-code--3602"></a>오류 코드:  3602

- **메시지**:`Invalid HttpMethod: {method}.`

- **원인**: 활동 페이로드에 지정 된 Http 메서드는 Azure Function 활동에서 지원 되지 않습니다. 

- **권장 사항**: 지원 되는 Http 메서드는 PUT, POST, GET, DELETE, OPTIONS, HEAD 및 TRACE입니다.


### <a name="error-code--3603"></a>오류 코드:  3603

- **메시지**:`Response content is not a valid JObject.`

- **원인**: 호출 된 Azure 함수에서 응답에 JSON 페이로드를 반환 하지 않았습니다. Data Factory의 Azure function 활동은 JSON 응답 콘텐츠만 지원 합니다. 

- **권장 사항**: 유효한 JSON 페이로드를 반환 하도록 Azure 함수를 업데이트 합니다. 예를 들어 함수 C# 는 Id\": `(ActionResult)new<OkObjectResult("{` \"\"123를반환할수`}");`있습니다.\"


### <a name="error-code--3606"></a>오류 코드:  3606

- **메시지**:`Azure function activity missing function key.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다. 

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 이름이 ' functionKey ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3607"></a>오류 코드:  3607

- **메시지**:`Azure function activity missing function name.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다. 

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 이름이 ' functionName ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3608"></a>오류 코드:  3608

- **메시지**:`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **원인**: 활동 정의의 Azure 함수 정보가 잘못 되었을 수 있습니다. 

- **권장 사항**: Azure 함수 정보를 수정 하 고 다시 시도 하세요.


### <a name="error-code--3609"></a>오류 코드:  3609

- **메시지**:`Azure function activity missing functionAppUrl.` 

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다. 

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 이름이 ' functionAppUrl ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3610"></a>오류 코드:  3610

- **메시지**:`There was an error while calling endpoint.`

- **원인**: 함수 URL이 잘못 되었을 수 있습니다.

- **권장 사항**: 작업 JSON에서 ' functionAppUrl '의 값이 올바른지 확인 하 고 다시 시도 하세요.


### <a name="error-code--3611"></a>오류 코드:  3611

- **메시지**:`Azure function activity missing Method in JSON.` 

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 이름이 ' method ' 인 속성이 있는지 확인 하세요.


### <a name="error-code--3612"></a>오류 코드:  3612

- **메시지**:`Azure function activity missing LinkedService definition in JSON.`

- **원인**: Azure 함수 활동 정의가 완료 되지 않았습니다.

- **권장 사항**: 입력 AzureFunction 활동 JSON 정의에 연결 된 서비스 정보가 있는지 확인 하세요.



## <a name="custom"></a>사용자 지정

다음 표는 Azure Batch에 적용 됩니다.


### <a name="error-code--2500"></a>오류 코드:  2500

- **메시지**:`Hit unexpected exception and execution failed.`

- **원인**:`Can't launch command, or the program returned an error code.`

- **권장 사항**:  실행 파일이 있는지 확인 하십시오. 프로그램이 시작 되 면 *stdout* 및 *stderr* 이 저장소 계정에 업로드 되었는지 확인 합니다. 디버깅을 위해 코드에서 copious 로그를 내보내는 것이 좋습니다.


### <a name="error-code--2501"></a>오류 코드:  2501

- **메시지**:`Cannot access user batch account; please check batch account settings.`

- **원인**: 일괄 처리 액세스 키 또는 풀 이름이 잘못 되었습니다.

- **권장 사항**: 연결 된 서비스의 풀 이름 및 Batch 액세스 키를 확인 합니다.


### <a name="error-code--2504"></a>오류 코드:  2504

- **메시지**:`Operation returned an invalid status code 'BadRequest'.` 

- **원인**: 사용자 지정 활동의 folderPath에 파일이 너무 많습니다. ResourceFiles의 총 크기는 32768 자를 초과할 수 없습니다.

- **권장 사항**: 불필요 한 파일을 제거 합니다. 또는 압축을 풀고 압축 풀기 명령을 추가 하 여 압축을 풉니다. 예를 들어 다음을 사용 합니다.`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>오류 코드:  2505

- **메시지**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **원인**: 사용자 지정 작업은 액세스 키를 사용 하는 저장소 계정만 지원 합니다.

- **권장 사항**: 오류 설명을 참조 하십시오.


### <a name="error-code--2507"></a>오류 코드:  2507

- **메시지**:`The folder path does not exist or is empty: ....`

- **원인**: 지정 된 경로의 저장소 계정에 파일이 없습니다.

- **권장 사항**: 폴더 경로는 실행 하려는 실행 파일을 포함 해야 합니다.


### <a name="error-code--2508"></a>오류 코드:  2508

- **메시지**:`There are duplicate files in the resource folder.`

- **원인**: 동일한 이름의 여러 파일은 folderPath의 다른 하위 폴더에 있습니다.

- **권장 사항**: 사용자 지정 작업은 folderPath에서 폴더 구조를 평면화 합니다.  폴더 구조를 유지 해야 하는 경우 파일을 압축 하 고 압축 풀기 명령을 사용 하 여 Azure Batch에서 압축을 풉니다. 예를 들어 다음을 사용 합니다.`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>오류 코드:  2509

- **메시지**:`Batch   url ... is invalid; it must be in Uri format.` 

- **원인**: 일괄 처리 Url은 다음과 같아야 합니다.`https://mybatchaccount.eastus.batch.azure.com`

- **권장 사항**: 오류 설명을 참조 하십시오.


### <a name="error-code--2510"></a>오류 코드:  2510

- **메시지**:`An   error occurred while sending the request.`

- **원인**: 일괄 처리 URL이 잘못 되었습니다. 

- **권장 사항**: 일괄 처리 URL을 확인 합니다.


## <a name="hdinsight"></a>HDInsight

다음 표는 Spark, Hive, MapReduce, Pig 및 Hadoop 스트리밍에 적용 됩니다.


### <a name="error-code--2300"></a>오류 코드:  2,300

- **메시지**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **원인**: 제공 된 클러스터 URI가 잘못 되었습니다. 

- **권장 사항**:  클러스터가 삭제 되지 않았고 제공 된 URI가 올바른지 확인 합니다. 브라우저에서 URI를 열 때 Ambari UI가 표시 되어야 합니다. 클러스터가 가상 네트워크에 있는 경우 URI는 개인 URI 여야 합니다. 이를 열려면 동일한 가상 네트워크의 일부인 VM을 사용 합니다. 자세한 내용은 [Apache Hadoop 서비스에 직접 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)을 참조 하세요.

<br/>

- **메시지**:`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **원인**: 작업 제출 시간이 초과 되었습니다. 

- **권장 사항**: 문제는 일반적인 HDInsight 연결 또는 네트워크 연결 중 하나일 수 있습니다. 먼저 모든 브라우저에서 HDInsight Ambari UI를 사용할 수 있는지 확인 합니다. 자격 증명이 여전히 유효한 지 확인 합니다. 자체 호스팅 IR (통합 런타임)을 사용 하는 경우 자체 호스팅 IR이 설치 된 VM 또는 컴퓨터에서이 작업을 수행 해야 합니다. 그런 다음 Data Factory에서 작업을 다시 제출 하십시오. 그래도 오류가 계속 발생 하면 Data Factory 팀에 지원을 요청 하세요.


- **메시지**:`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **원인**: HDInsight에 대 한 자격 증명이 잘못 되었거나 만료 되었습니다.

- **권장 사항**: 자격 증명을 수정 하 고 연결 된 서비스를 다시 배포 합니다. 먼저 브라우저에서 클러스터 URI를 열고 로그인을 시도 하 여 HDInsight에서 자격 증명이 작동 하는지 확인 합니다. 자격 증명이 작동 하지 않는 경우 Azure Portal에서 자격 증명을 다시 설정할 수 있습니다.

<br/>

- **메시지**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **원인**: 이 오류는 HDInsight에서 발생 한 것입니다.

- **권장 사항**: 이 오류는 HDInsight 클러스터에서 발생 한 것입니다. 자세한 내용은 [AMBARI UI 502 오류](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors to spark Thrift server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Thrift server에 연결 하는 502 오류](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Application Gateway에서 잘못 된 게이트웨이 오류 문제 해결](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)을 참조 하세요.

<br/>

- **메시지**:`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **원인**: 동시에 너무 많은 작업을 HDInsight에 전송 하 고 있습니다.

- **권장 사항**: HDInsight에 제출 되는 동시 작업 수를 제한 하는 것이 좋습니다. 동일한 작업을 통해 작업을 전송 하는 경우 Data Factory 작업 동시성을 참조 하세요. 동시 파이프라인 실행이 시간이 지남에 따라 분산 되도록 트리거를 변경 합니다. 오류가 제안 된 대로 조정 `templeton.parallellism.job.submit` 하려면 HDInsight 설명서를 참조 하세요.


### <a name="error-code--2303"></a>오류 코드:  2303

- **메시지**:`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **원인**: 작업이 HDInsight에 제출 되었으며 HDInsight에서 실패 했습니다.

- **권장 사항**: 작업이 HDInsight에 성공적으로 제출 되었습니다. 클러스터에서 실패 했습니다. HDInsight Ambari UI에서 작업 및 로그를 열거나 오류 메시지에서 제안 하는 대로 저장소에서 파일을 엽니다. 파일에 오류 정보가 표시 됩니다.


### <a name="error-code--2310"></a>오류 코드:  2310

- **메시지**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **원인**: 제공 된 클러스터 URI가 잘못 되었습니다. 

- **권장 사항**:  클러스터가 삭제 되지 않았고 제공 된 URI가 올바른지 확인 합니다. 브라우저에서 URI를 열 때 Ambari UI가 표시 되어야 합니다. 클러스터가 가상 네트워크에 있는 경우 URI는 개인 URI 여야 합니다. 이를 열려면 동일한 가상 네트워크의 일부인 VM을 사용 합니다. 자세한 내용은 [Apache Hadoop 서비스에 직접 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)을 참조 하세요.

<br/>

- **메시지**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **원인**: 이 오류는 HDInsight에서 발생 한 것입니다.

- **권장 사항**: 이 오류는 HDInsight 클러스터에서 발생 한 것입니다. 자세한 내용은 [AMBARI UI 502 오류](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors to spark Thrift server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Thrift server에 연결 하는 502 오류](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Application Gateway에서 잘못 된 게이트웨이 오류 문제 해결](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)을 참조 하세요.

<br/>

- **메시지**:`java.lang.NullPointerException`

- **원인**: 이 오류는 작업이 Spark 클러스터에 제출 될 때 발생 합니다. 

- **권장 사항**: 이 예외는 HDInsight에서 제공 됩니다. 실제 문제를 숨깁니다. HDInsight 팀에 지원을 요청 하세요. 클러스터 이름 및 작업 실행 시간 범위를 제공 합니다.


### <a name="error-code--2347"></a>오류 코드:  2347

- **메시지**:`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **원인**: 작업이 HDInsight에 제출 되었으며 HDInsight에서 실패 했습니다.

- **권장 사항**: 작업이 HDInsight에 성공적으로 제출 되었습니다. 클러스터에서 실패 했습니다. HDInsight Ambari UI에서 작업 및 로그를 열거나 오류 메시지에서 제안 하는 대로 저장소에서 파일을 엽니다. 파일에 오류 정보가 표시 됩니다.


### <a name="error-code--2328"></a>오류 코드:  2328

- **메시지**:`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **원인**: 이 오류는 HDInsight 주문형에서 발생 합니다.

- **권장 사항**: 이 오류는 HDInsight 프로 비전이 실패할 때 HDInsight 서비스에서 제공 됩니다. HDInsight 팀에 문의 하 고 주문형 클러스터 이름을 제공 합니다.



## <a name="web-activity"></a>웹 작업

### <a name="error-code--2310"></a>오류 코드:  2310

- **메시지**:`Invalid HttpMethod: '...'.`

- **원인**: 웹 작업은 활동 페이로드에 지정 된 HTTP 메서드를 지원 하지 않습니다.

- **권장 사항**:  지원 되는 HTTP 메서드는 PUT, POST, GET 및 DELETE입니다.

<br/>

- **메시지**:`Invalid Server Error 500.`

- **원인**: 끝점에서 내부 오류가 발생 했습니다.

- **권장 사항**:  Fiddler 또는 Postman을 사용 하 여 URL의 기능을 확인 합니다.

<br/>

- **메시지**:`Unauthorized 401.`

- **원인**: 요청에 대 한 올바른 인증이 없습니다.

- **권장 사항**:  토큰이 만료 되었을 수 있습니다. 유효한 인증 방법을 제공 합니다. Fiddler 또는 Postman을 사용 하 여 URL의 기능을 확인 합니다.

<br/>

- **메시지**:`Forbidden 403.`

- **원인**: 필요한 사용 권한이 없습니다.

- **권장 사항**:  액세스 한 리소스에 대 한 사용자 권한을 확인 합니다. Fiddler 또는 Postman을 사용 하 여 URL의 기능을 확인 합니다.

<br/>

- **메시지**:`Bad Request 400.`

- **원인**: HTTP 요청이 잘못 되었습니다.

- **권장 사항**:   요청의 URL, 동사 및 본문을 확인 합니다. Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.

<br/>

- **메시지**:`Not found 404.` 

- **원인**: 리소스를 찾을 수 없습니다.   

- **권장 사항**:  Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.

<br/>

- **메시지**:`Service unavailable.`

- **원인**: 서비스를 사용할 수 없습니다.

- **권장 사항**:  Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.

<br/>

- **메시지**:`Unsupported Media Type.`

- **원인**: 콘텐츠 형식이 웹 활동 본문과 일치 하지 않습니다.

- **권장 사항**:  페이로드 형식과 일치 하는 내용 유형을 지정 합니다. Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.

<br/>

- **메시지**:`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **원인**: 리소스를 사용할 수 없습니다. 

- **권장 사항**:  Fiddler 또는 Postman을 사용 하 여 끝점을 확인 합니다.

<br/>

- **메시지**:`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **원인**: 요청에 잘못 된 웹 작업 메서드가 지정 되었습니다.

- **권장 사항**:  Fiddler 또는 Postman을 사용 하 여 끝점을 확인 합니다.

<br/>

- **메시지**:`invalid_payload`

- **원인**: 웹 활동 본문이 잘못 되었습니다.

- **권장 사항**:  Fiddler 또는 Postman을 사용 하 여 끝점을 확인 합니다.

Fiddler를 사용 하 여 모니터링 되는 웹 응용 프로그램의 HTTP 세션을 만들려면 다음을 수행 합니다.

1. [Fiddler](https://www.telerik.com/download/fiddler)를 다운로드, 설치 및 엽니다.

1. 웹 응용 프로그램에서 https를 사용 하는 경우 **Tools** > **Fiddler Options** > **HTTPS**로 이동 합니다. Https **연결 캡처** 및 **https 트래픽 해독**을 선택 합니다. 
   
   ![Fiddler 옵션](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. 응용 프로그램에서 SSL 인증서를 사용 하는 경우 Fiddler 인증서를 장치에 추가 합니다. **Tools**FiddlerOptions > HTTPS 작업루트인증서를데스크톱으로내보내기로이동합니다 > . >  > 

1. **파일** > **캡처 트래픽으로**이동 하 여 캡처를 해제 합니다. 또는 **F12**키를 누릅니다.

1. 모든 캐시 된 항목을 제거 하 고 다시 다운로드 해야 하도록 브라우저의 캐시를 지웁니다.

1. 요청을 만듭니다. 

   a. **작성기** 탭을 선택 합니다.

   b. HTTP 메서드 및 URL을 설정 합니다.

   c. 필요한 경우 헤더 및 요청 본문을 추가 합니다.

   d. **실행**을 선택합니다.

9. 트래픽 캡처를 다시 켜고 페이지에서 문제가 되는 트랜잭션을 완료 합니다.

10. **파일** > **모든 세션**저장 > 으로 이동 합니다.

자세한 내용은 [Fiddler 시작](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대 한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대 한 Twitter 정보](https://twitter.com/hashtag/DataFactory)



