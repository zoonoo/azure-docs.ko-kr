---
title: Azure Data Factory 문제 해결 | Microsoft Docs
description: Azure Data Factory에서 외부 제어 작업의 문제를 해결 하는 방법을 알아봅니다.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 1995ce2a91bfbc115f80c99687cc84b52ef614ec
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950103"
---
# <a name="troubleshoot-azure-data-factory"></a>문제 해결 Azure Data Factory

이 문서에서는 Azure Data Factory의 외부 제어 작업에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="azure-databricks"></a>Azure Databricks

| 오류 코드 | 오류 메시지                                          | 설명                             | 권장                             |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 오류 403.                                                    | Databricks 액세스 토큰이 만료 되었습니다.                         | 기본적으로 Databricks 액세스 토큰은 90 일 동안 유효 합니다.  새 토큰을 만들고 연결 된 서비스를 업데이트 합니다. |
| 3201           | 필수 필드가 없습니다. notebook_task. notebook_path | 잘못 된 제작: 전자 필기장 경로가 올바르게 지정 되지 않았습니다. | Databricks 활동에서 노트북 경로를 지정 합니다. |
| 3201           | 클러스터 ... 존재 하지 않습니다.                                 | 작성 오류: Databricks 클러스터가 없거나 삭제 되었습니다. | Databricks 클러스터가 있는지 확인 합니다. |
| 3201           | 잘못 된 Python 파일 URI .... 지원 되는 URI 체계는 Databricks 사용자 가이드를 참조 하세요. | 잘못 된 제작.                                                | 작업 영역 주소 지정 스키마 또는 `dbfs:/folder/subfolder/foo.py` Databricks 파일 시스템에 저장 된 파일에 대 한 절대 경로를 지정 합니다. |
| 3201           | {0}LinkedService에는 필수 속성으로 domain 및 accessToken가 있어야 합니다. | 잘못 된 제작.                                                | 연결 된 [서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인 합니다. |
| 3201           | {0}LinkedService는 만들 기존 클러스터 ID 또는 새 클러스터 정보를 지정 해야 합니다. | 잘못 된 제작.                                                | 연결 된 [서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인 합니다. |
| 3201           | 노드 유형 Standard_D16S_v3은 지원 되지 않습니다. 지원 되는 노드 유형:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2. | 잘못 된 제작.                                                | 오류 메시지를 참조 하십시오.                                          |
| 3201           | 잘못 된 notebook_path: ... 현재 절대 경로만 지원 됩니다. 경로는 '/'로 시작 해야 합니다. | 잘못 된 제작.                                                | 오류 메시지를 참조 하십시오.                                          |
| 3202           | 이전 3600 초에 생성 된 작업을 이미 1000, rate 제한을 초과 했습니다.   1000 초 3600 마다 작업을 생성 합니다. | 한 시간에 너무 많은 Databricks 실행 됩니다.                         | 이 Databricks 작업 영역을 사용 하는 모든 파이프라인에서 작업 만들기 빈도를 확인 합니다.  파이프라인이 너무 많은 Databricks를 시작한 경우에는 일부 파이프라인을 새 작업 영역으로 마이그레이션합니다. |
| 3202           | 요청 개체를 구문 분석할 수 없습니다. JSON 맵 필드 base_parameters에 대해 ' key ' 및 ' value '를 설정 해야 합니다. ' key: "..." '를 가져왔습니다. 사용합니다. | 작성 오류: 매개 변수에 대 한 값이 제공 되지 않았습니다.         | 파이프라인 JSON을 검사 하 고 baseParameters 노트북의 모든 매개 변수가 비어 있지 않은 값을 지정 하는지 확인 합니다. |
| 3202           | 사용자: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` 클러스터에 액세스할 수 있는 권한이 없습니다. | 액세스 토큰을 생성 한 사용자는 연결 된 서비스에 지정 된 Databricks 클러스터에 액세스할 수 없습니다. | 작업 영역에서 사용자에 게 필요한 권한이 있는지 확인 합니다.   |
| 3203           | 클러스터가 종료 된 상태 이며 작업을 수신 하는 데 사용할 수 없습니다. 클러스터를 수정 하거나 나중에 다시 시도 하세요. | 클러스터가 종료 되었습니다.    대화형 클러스터의 경우 경합 상태가 될 수 있습니다. | 이 오류를 방지 하는 가장 좋은 방법은 작업 클러스터를 사용 하는 것입니다.             |
| 3204           | 작업을 실행 하지 못했습니다.  | 오류 메시지는 예기치 않은 클러스터 상태 또는 특정 활동과 같은 다양 한 문제를 표시 합니다. 대부분 오류 메시지가 표시 되지 않는 경우가 많습니다.                                                          | 해당 사항 없음                                                          |



## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

다음 표는 U-SQL에 적용 됩니다.

| 오류 코드         | 오류 메시지                                                | 설명                                          | 권장                            |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | 액세스 토큰이 잘못 된 테 넌 트에 있습니다.                    | 잘못 된 Azure Active Directory (Azure AD) 테 넌 트입니다.                                         | Azure Data Lake Analytics 액세스 하는 데 사용 되는 서비스 주체는 다른 Azure AD 테 넌 트에 속합니다. Data Lake Analytics 계정과 동일한 테 넌 트에 새 서비스 주체를 만듭니다. |
| 2711,   2705,   2704 | 것. ACL 확인이 실패했습니다. 리소스가 없거나 사용자에 게 요청 된 작업을 수행할 수 있는 권한이 없습니다.<br/><br/>사용자가 Data Lake Store에 액세스할 수 없습니다.  <br/><br/>사용자에 게 Data Lake Analytics을 사용할 수 있는 권한이 없습니다. | 서비스 사용자 또는 인증서가 저장소에 있는 파일에 액세스할 수 없습니다. | 사용자가 Data Lake Analytics 작업을 위해 제공 하는 서비스 주체 또는 인증서가 루트 폴더에서 Data Lake Analytics 계정 및 기본 Data Lake Storage 인스턴스에 액세스할 수 있는지 확인 합니다. |
| 2711                 | ' Azure Data Lake Store ' 파일 또는 폴더를 찾을 수 없습니다.       | U-SQL 파일의 경로가 잘못 되었거나 연결 된 서비스 자격 증명에 액세스 권한이 없습니다. | 연결 된 서비스에서 제공 하는 경로 및 자격 증명을 확인 합니다. |
| 2707                 | AzureDataLakeAnalytics의 계정을 확인할 수 없습니다. ' AccountName ' 및 ' DataLakeAnalyticsUri '를 확인 하세요. | 연결 된 서비스의 Data Lake Analytics 계정이 잘못 되었습니다.                  | 올바른 계정이 제공 되는지 확인 합니다.             |
| 2703                 | 오류 Id: E_CQO_SYSTEM_INTERNAL_ERROR (또는 "오류 Id:"로 시작 하는 오류). | Data Lake Analytics에서 오류가 발생 했습니다.                                    | 예와 같은 오류는 작업이 Data Lake Analytics 전송 되었으며 스크립트가 실패 한 것을 의미 합니다. Data Lake Analytics를 조사 합니다. 포털에서 Data Lake Analytics 계정으로 이동 하 고 Data Factory 활동 실행 ID (파이프라인 실행 ID 아님)를 사용 하 여 작업을 찾습니다. 작업은 오류에 대 한 자세한 정보를 제공 하 고 문제를 해결 하는 데 도움이 됩니다. 해결 방법이 명확 하지 않은 경우 Data Lake Analytics 지원 팀에 문의 하 여 계정 이름 및 작업 ID를 포함 하는 작업 URL을 제공 합니다. |
| 2709                 | 지금은 작업을 수락할 수 없습니다. 계정에 대 한 최대 대기 작업 수는 200입니다. | 이 오류는 Data Lake Analytics에 대 한 제한으로 인해 발생 합니다.                                           | 작업에 대 한 Data Factory 트리거 및 동시성 설정을 변경 하 여 Data Lake Analytics 전송 된 작업의 수를 줄입니다. 또는 Data Lake Analytics에 대 한 제한을 늘립니다. |
| 2709                 | 이 작업은 24 au가 필요 하기 때문에 거부 되었습니다. 이 계정의 관리자 정의 정책에 따라 작업에서 au를 5 개 이상 사용할 수 없습니다. | 이 오류는 Data Lake Analytics에 대 한 제한으로 인해 발생 합니다.                                           | 작업에 대 한 Data Factory 트리거 및 동시성 설정을 변경 하 여 Data Lake Analytics 전송 된 작업의 수를 줄입니다. 또는 Data Lake Analytics에 대 한 제한을 늘립니다. |



## <a name="azure-functions"></a>Azure Functions

| 오류 코드 | 오류 메시지                           | Description                                                  | 권장                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | 응답 콘텐츠가 올바른 JObject가 아닙니다. | 호출 된 Azure 함수에서 응답에 JSON 페이로드를 반환 하지 않았습니다. Data Factory의 Azure function 활동은 JSON 응답 콘텐츠만 지원 합니다. | 유효한 JSON 페이로드를 반환 하도록 Azure 함수를 업데이트 합니다. 예를 들어 함수 C# 는 Id\": `(ActionResult)new<OkObjectResult("{` \"\"123를반환할수`}");`있습니다.\" |
| 3600         | 잘못 된 HttpMethod: ' ... '.               | 활동 페이로드에 지정 된 HTTP 메서드는 Azure function 활동에서 지원 되지 않습니다. | PUT, POST, GET, DELETE, OPTIONS, HEAD 또는 TRACE와 같은 지원 되는 HTTP 메서드를 사용 합니다. |



## <a name="custom"></a>사용자 지정

다음 표는 Azure Batch에 적용 됩니다.

| 오류 코드 | 오류 메시지                                                | Description                                                  | 권장                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | 예기치 않은 예외에 도달 하 여 실행 하지 못했습니다.             | 명령을 실행할 수 없거나 프로그램에서 오류 코드를 반환 했습니다. | 실행 파일이 있는지 확인 하십시오. 프로그램이 시작 되 면 *stdout* 및 *stderr* 이 저장소 계정에 업로드 되었는지 확인 합니다. 디버깅을 위해 코드에서 copious 로그를 내보내는 것이 좋습니다. |
| 2501         | 사용자 배치 계정에 액세스할 수 없습니다. batch 계정 설정을 확인 하세요. | 일괄 처리 액세스 키 또는 풀 이름이 잘못 되었습니다.            | 연결 된 서비스의 풀 이름 및 Batch 액세스 키를 확인 합니다. |
| 2502         | 사용자 저장소 계정에 액세스할 수 없습니다. 저장소 계정 설정을 확인 하세요. | 저장소 계정 이름 또는 액세스 키가 잘못 되었습니다.       | 연결 된 서비스에서 저장소 계정 이름 및 액세스 키를 확인 합니다. |
| 2504         | 작업에서 잘못 된 상태 코드 ' BadRequest '를 반환 했습니다.     | 사용자 지정 활동의 folderPath에 파일이 너무 많습니다. ResourceFiles의 총 크기는 32768 자를 초과할 수 없습니다. | 불필요 한 파일을 제거 합니다. 또는 압축을 풀고 압축 풀기 명령을 추가 하 여 압축을 풉니다. 예를 들어 다음을 사용 합니다.`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe` |
| 2505         | 계정 키 자격 증명이 사용 되지 않으면 공유 액세스 서명을 만들 수 없습니다. | 사용자 지정 작업은 액세스 키를 사용 하는 저장소 계정만 지원 합니다. | 오류 설명을 참조 하십시오.                                            |
| 2507         | 폴더 경로가 없거나 비어 있습니다 ....            | 지정 된 경로의 저장소 계정에 파일이 없습니다.       | 폴더 경로는 실행 하려는 실행 파일을 포함 해야 합니다. |
| 2508         | 리소스 폴더에 중복 된 파일이 있습니다.               | 동일한 이름의 여러 파일은 folderPath의 다른 하위 폴더에 있습니다. | 사용자 지정 작업은 folderPath에서 폴더 구조를 평면화 합니다.  폴더 구조를 유지 해야 하는 경우 파일을 압축 하 고 압축 풀기 명령을 사용 하 여 Azure Batch에서 압축을 풉니다. 예를 들어 다음을 사용 합니다.`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe` |
| 2509         | 일괄 처리 url ... 잘못 되었습니다. Uri 형식 이어야 합니다.         | 일괄 처리 Url은 다음과 같아야 합니다.`https://mybatchaccount.eastus.batch.azure.com` | 오류 설명을 참조 하십시오.                                            |
| 2510         | 요청을 보내는 동안 오류가 발생 했습니다.               | 일괄 처리 URL이 잘못 되었습니다.                                         | 일괄 처리 URL을 확인 합니다.                                            |

## <a name="hdinsight"></a>HDInsight

다음 표는 Spark, Hive, MapReduce, Pig 및 Hadoop 스트리밍에 적용 됩니다.

| 오류 코드 | 오류 메시지                                                | Description                                                  | 권장                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Hadoop 작업을 제출 하지 못했습니다. 오류: 원격 이름을 확인할 수 없습니다. <br/><br/>클러스터를 찾을 수 없습니다. | 제공 된 클러스터 URI가 잘못 되었습니다.                              | 클러스터가 삭제 되지 않았고 제공 된 URI가 올바른지 확인 합니다. 브라우저에서 URI를 열 때 Ambari UI가 표시 되어야 합니다. 클러스터가 가상 네트워크에 있는 경우 URI는 개인 URI 여야 합니다. 이를 열려면 동일한 가상 네트워크의 일부인 VM을 사용 합니다. 자세한 내용은 [Apache Hadoop 서비스에 직접 연결](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)을 참조 하세요. |
| 2,300         | Hadoop 작업을 제출 하지 못했습니다. 작업: ..., 클러스터: .../. 오류: 태스크가 취소 되었습니다. | 작업 제출 시간이 초과 되었습니다.                         | 문제는 일반적인 HDInsight 연결 또는 네트워크 연결 중 하나일 수 있습니다. 먼저 모든 브라우저에서 HDInsight Ambari UI를 사용할 수 있는지 확인 합니다. 자격 증명이 여전히 유효한 지 확인 합니다. 자체 호스팅 IR (통합 런타임)을 사용 하는 경우 자체 호스팅 IR이 설치 된 VM 또는 컴퓨터에서이 작업을 수행 해야 합니다. 그런 다음 Data Factory에서 작업을 다시 제출 하십시오. 그래도 오류가 계속 발생 하면 Data Factory 팀에 지원을 요청 하세요. |
| 2,300         | 상태로   Ambari 사용자 이름 또는 암호가 잘못 되었습니다.  <br/><br/>상태로   사용자 관리자가 Ambari에서 잠겼습니다.   <br/><br/>403-사용할 수 없음: 액세스가 거부되었습니다. | HDInsight에 대 한 자격 증명이 잘못 되었거나 만료 되었습니다. | 자격 증명을 수정 하 고 연결 된 서비스를 다시 배포 합니다. 먼저 브라우저에서 클러스터 URI를 열고 로그인을 시도 하 여 HDInsight에서 자격 증명이 작동 하는지 확인 합니다. 자격 증명이 작동 하지 않는 경우 Azure Portal에서 자격 증명을 다시 설정할 수 있습니다. |
| 2300,   2310 | 502 - 웹 서버에서 게이트웨이 또는 프록시 서버 역할을 하는 동안 잘못된 응답을 수신했습니다.       <br/>잘못 된 게이트웨이입니다. | 이 오류는 HDInsight에서 발생 한 것입니다.                               | 이 오류는 HDInsight 클러스터에서 발생 한 것입니다. 자세한 내용은 [AMBARI UI 502 오류](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 Errors to spark Thrift server](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Thrift server에 연결 하는 502 오류](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [Application Gateway에서 잘못 된 게이트웨이 오류 문제 해결](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502)을 참조 하세요. |
| 2,300         | Hadoop 작업을 제출 하지 못했습니다. 작업: ..., 클러스터: ... 오류: {\"error\":\"templeton 서비스가 너무 많은 전송 작업 요청을 사용 중 이므로 제출 작업 요청에 서비스를 수행할 수 없습니다. 잠시 기다린 후 작업을 다시 시도 하세요. Templeton 구성 parallellism을 참조 하 여 동시 요청을 구성 하십시오.  <br/><br/>Hadoop 작업을 제출 하지 못했습니다. 작업: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, Cluster: `https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/`.   오류: {\"오류\":\"yarn. YarnException...:. Application_1561147195099_3730을 YARN에 제출 하지 못했습니다:: Queue root. joblauncher는 이미 500 응용 프로그램을 포함 하 고 있으며, 응용 프로그램 제출을 허용할 수 없습니다. | 동시에 너무 많은 작업을 HDInsight에 전송 하 고 있습니다. | HDInsight에 제출 되는 동시 작업 수를 제한 하는 것이 좋습니다. 동일한 작업을 통해 작업을 전송 하는 경우 Data Factory 작업 동시성을 참조 하세요. 동시 파이프라인 실행이 시간이 지남에 따라 분산 되도록 트리거를 변경 합니다. 오류가 제안 된 대로 조정 `templeton.parallellism.job.submit` 하려면 HDInsight 설명서를 참조 하세요. |
| 2303,   2347 | Hadoop 작업이 실패 했습니다 (종료 코드 ' 5 '). 자세한 내용은wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' '을 참조 하세요.  <br/><br/>하이브 실행이 실패 했습니다 (오류 코드 ' UserErrorHiveOdbcCommandExecutionFailure ').   자세한 내용은wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' '을 참조 하세요. | 작업이 HDInsight에 제출 되었으며 HDInsight에서 실패 했습니다. | 작업이 HDInsight에 성공적으로 제출 되었습니다. 클러스터에서 실패 했습니다. HDInsight Ambari UI에서 작업 및 로그를 열거나 오류 메시지에서 제안 하는 대로 저장소에서 파일을 엽니다. 파일에 오류 정보가 표시 됩니다. |
| 2328         | 요청을 처리 하는 동안 내부 서버 오류가 발생 했습니다. 요청을 다시 시도 하거나 고객 지원에 문의 하세요. | 이 오류는 HDInsight 주문형에서 발생 합니다.                              | 이 오류는 HDInsight 프로 비전이 실패할 때 HDInsight 서비스에서 제공 됩니다. HDInsight 팀에 문의 하 고 주문형 클러스터 이름을 제공 합니다. |
| 2310         | java.lang.NullPointerException                               | 이 오류는 작업이 Spark 클러스터에 제출 될 때 발생 합니다.      | 이 예외는 HDInsight에서 제공 됩니다. 실제 문제를 숨깁니다. HDInsight 팀에 지원을 요청 하세요. 클러스터 이름 및 작업 실행 시간 범위를 제공 합니다. |
|              | 다른 모든 오류                                             |                                                              | HDInsight 및 Hdinsight를 [사용 하 여 문제 해결](../hdinsight/hdinsight-troubleshoot-guide.md) [FAQ](https://hdinsight.github.io/)를 참조 하세요. |



## <a name="web-activity"></a>웹 작업

| 오류 코드 | 오류 메시지                                                | Description                                                  | 권장                          |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | 잘못 된 HttpMethod: ' ... '.                                    | 웹 작업은 활동 페이로드에 지정 된 HTTP 메서드를 지원 하지 않습니다. | 지원 되는 HTTP 메서드는 PUT, POST, GET 및 DELETE입니다. |
| 2108         | 잘못 된 서버 오류 500입니다.                                     | 끝점에서 내부 오류가 발생 했습니다.                               | Fiddler 또는 Postman을 사용 하 여 URL의 기능을 확인 합니다. |
| 2108         | 권한 없음 401.                                             | 요청에 대 한 올바른 인증이 없습니다.                      | 토큰이 만료 되었을 수 있습니다. 유효한 인증 방법을 제공 합니다. Fiddler 또는 Postman을 사용 하 여 URL의 기능을 확인 합니다. |
| 2108         | 금지 403.                                                | 필요한 사용 권한이 없습니다.                                 | 액세스 한 리소스에 대 한 사용자 권한을 확인 합니다. Fiddler 또는 Postman을 사용 하 여 URL의 기능을 확인 합니다.  |
| 2108         | 잘못 된 요청 400.                                              | HTTP 요청이 잘못 되었습니다.                                         | 요청의 URL, 동사 및 본문을 확인 합니다. Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.  |
| 2108         | 404를 찾을 수 없습니다.                                                | 리소스를 찾을 수 없습니다.                                       | Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.  |
| 2108         | 서비스를 사용할 수 없습니다.                                          | 서비스를 사용할 수 없습니다.                                       | Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다.  |
| 2108         | 지원 되지 않는 미디어 유형입니다.                                       | 콘텐츠 형식이 웹 활동 본문과 일치 하지 않습니다.           | 페이로드 형식과 일치 하는 내용 유형을 지정 합니다. Fiddler 또는 Postman을 사용 하 여 요청의 유효성을 검사 합니다. |
| 2108         | 찾고 있는 리소스가 제거 되었거나 이름이 변경 되었거나 일시적으로 사용할 수 없습니다. | 리소스를 사용할 수 없습니다.                                | Fiddler 또는 Postman을 사용 하 여 끝점을 확인 합니다. |
| 2108         | 잘못 된 메서드 (HTTP 동사)가 사용 중 이므로 찾고 있는 페이지를 표시할 수 없습니다. | 요청에 잘못 된 웹 작업 메서드가 지정 되었습니다.   | Fiddler 또는 Postman을 사용 하 여 끝점을 확인 합니다. |
| 2108         | invalid_payload                                              | 웹 활동 본문이 잘못 되었습니다.                       | Fiddler 또는 Postman을 사용 하 여 끝점을 확인 합니다. |

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



