---
title: Azure 데이터 팩터리 문제 해결 | Microsoft Docs
description: Azure Data Factory를 해결합니다. 모든 외부, 제어 작업에 대 한 일반적인 문서입니다.
services: data-factory
author: abnarain
manager: craigg
ms.service: data-factory
ms.topic: troubleshoot
ms.subservice: troubleshoot
ms.date: 6/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 8d6ab565098e1ea40ede5c650f05e670a1edc7f6
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67452684"
---
# <a name="troubleshooting-azure-data-factory"></a>Azure 데이터 팩터리 문제 해결
이 문서에서는 일반적인 문제 해결 질문을 나열 합니다.

- [Azure Databricks (노트북, jar, python)](#azure-databricks)
- [Azure Data Lake Analytics (U-SQL)](#azure-data-lake-analytics-u-sql)
- [Azure Functions](#azure-functions)
- [사용자 지정 (Azure Batch)](#custom-azure-batch)
- [HDInsight (Spark, Hive, MapReduce, Pig, Hadoop 스트리밍)](#hdinsight-spark-hive-mapreduce-pig-hadoop-streaming)
- [웹 작업](#web-activity)



## <a name="azure-databricks"></a>Azure Databricks
| 오류 코드 | 오류 메시지                                          | 문제 설명                             | 가능한 수정 / 권장 작업                            |
| -------------- | ----------------------------------------------------- | --------------------------------------------------------------| :----------------------------------------------------------- |
| 3200           | 오류 403 표시                                                    | Databricks 액세스 토큰이 만료 되었습니다.                         | 기본적으로 Databricks 액세스 토큰은 90 일 동안 유효 합니다.  새 토큰을 만들고 연결 된 서비스를 업데이트 하세요. |
| 3201           | 필수 필드가 없음: settings.task.notebook_task.notebook_path | 잘못 된 작성 합니다. Notebook 경로 올바르게 지정 되지 않습니다. | Databricks 활동에서 notebook 경로 지정 하십시오. |
| 3201           | 클러스터는 중... 없는 경우                                 | 오류를 작성 합니다. Databricks 클러스터는 존재 하지 않거나 삭제 되었습니다. | Databricks 클러스터 있는지 확인 하십시오. |
| 3201           | 잘못 된 python 파일 URI:... 지원 되는 URI 체계에 대 한 Databricks 사용자 가이드를 참조 하세요. | 잘못 된 제작                                                | 작업 영역 주소 지정 구성표에 대 한 절대 경로 또는 DBFS에 저장 된 파일에 대 한 "dbfs:/folder/subfolder/foo.py"를 지정 합니다. |
| 3201           | {0}   LinkedService 있어야 도메인과 accessToken 필수 속성 | 잘못 된 제작                                                | 확인 하십시오 [연결 된 서비스 정의](compute-linked-services.md#azure-databricks-linked-service)합니다. |
| 3201           | {0}   기존 클러스터 Id 또는 새 클러스터 정보 만들기에 대 한 LinkedService 지정 해야 | 잘못 된 제작                                                | 확인 하십시오 [연결 된 서비스 정의](compute-linked-services.md#azure-databricks-linked-service)합니다. |
| 3201           | 노드 유형 Standard_D16S_v3 지원 되지 않습니다. 지원 되는 노드 형식:   Standard_ds3_v2(Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2 Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s Standard_F8s, Standard_F16s, 표준 h16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_ NC24s_v3 Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2 | 잘못 된 제작                                                | 오류 메시지를 참조 하세요.                                          |
| 3201           | 잘못 된 notebook_path:... 현재 절대 경로만 지원 됩니다. 경로 시작 해야 합니다 '/'. | 잘못 된 제작                                                | 오류 메시지를 참조 하세요.                                          |
| 3202           | 속도 제한을 초과 하는 지난 3, 600 초에서 생성 하는 작업이 1000 개 이미 했습니다.   3600 초 당 1000 작업 만들기입니다. | 너무 많은 Databricks는 한 시간에 실행 됩니다.                         | 작업 만들기 비율에 대 한이 Databricks 작업 영역을 사용 하는 모든 파이프라인을 확인 합니다.  파이프라인에서 너무 많은 Databricks 실행 집계에서를 시작한 경우 새 작업 영역에 몇 가지 파이프라인을 마이그레이션하십시오. |
| 3202           | 요청 개체를 구문 분석 하지 못했습니다. 예상된 'key' 및 'value' JSON 지도 필드 base_parameters에 대 한 설정 되었습니다 ' 키: "..." ' 사용합니다. | 오류를 작성 합니다. 매개 변수에 대해 제공 된 값         | 파이프라인 json를 검사 하 고 Notebook baseParameters의 모든 매개 변수에 비어 있지 않은 값이 지정 되어 있는지 확인 합니다. |
| 3202           | 사용자: SimpleUserContext {userId =..., 이름 =user@company.com, orgId =...} 클러스터에 액세스 권한이 없습니다. | 액세스 토큰을 생성 하는 사용자가 연결된 된 서비스에 지정 된 Databricks 클러스터에 액세스할 수 없습니다. | 작업 영역에서 사용자에 필요한 권한이 있는지 확인 합니다.   |
| 3203           | 클러스터가 종료 상태를 수신 작업에 사용할 수 없습니다. 클러스터를 수정 하거나 나중에 다시 시도 하세요. | 클러스터 종료 되었습니다.    대화형 클러스터에 대 한 경합 수 있습니다. | 이 문제를 방지 하는 가장 좋은 방법은 작업 클러스터를 사용 하는 것입니다.             |
| 3204           | 작업 실행이 실패 했습니다. 작업 관련 메시지에 예기치 않은 클러스터 상태에서 오류 메시지를 개수에 관계 없이 있을 수 있습니다.  가장 일반적인 오류 메시지를 전혀 않습니다. | N/A                                                          | N/A                                                          |



## <a name="azure-data-lake-analytics-u-sql"></a>Azure Data Lake Analytics (U-SQL)

| 오류 코드         | 오류 메시지                                                | 문제 설명                                          | 가능한 수정 / 권장 작업                             |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2709                 | 액세스 토큰이 잘못 된 테 넌 트에서                    | 잘못 된 AAD 테 넌 트                                         | 서비스 주체를 ADLA에 액세스 하는 데 다른 AAD 테 넌 트에 속합니다. ADLA 계정으로 동일한 테 넌 트에 새 서비스 주체를 만드세요. |
| 2711,   2705,   2704 | 사용할 수 없습니다. ACL 확인 하지 못했습니다. 리소스가 존재 하지 않는 또는 사용자가 요청한 작업을 수행할 수 있는 권한이 없습니다.<br/><br/>사용자가 data lake store에 액세스할 수 없습니다.  <br/><br/>사용자는 data lake analytics에 권한이 없습니다. | 서비스 주체 또는 제공 된 인증서 저장소에에서 있는 파일에 액세스할 수 없습니다. | 했는지 서비스 주체 또는 ADLA 작업에 대 한 제공 하는 인증서에 대 한 루트 폴더에서 ADLA 계정 및 기본 ADLS 저장소 모두에 대 한 액세스를가 합니다. |
| 2711                 | ' Azure Data Lake Store' 파일 또는 폴더를 찾을 수 없습니다.       | USQL 파일 경로가 잘못 된 또는 자격 증명 액세스 권한이 없는 연결된 된 서비스 | 경로 및 연결 된 서비스에 제공 된 자격 증명을 확인 하십시오 |
| 2707                 | AzureDataLakeAnalytics의 계정을 확인할 수 없습니다. 'AccountName' 및 'DataLakeAnalyticsUri' 확인 하세요. | 연결 된 서비스에서 ADLA 계정 잘못 되었습니다.                  | 올바른 계정을 제공 되도록를 확인 하세요.             |
| 2703                 | 오류 Id: E_CQO_SYSTEM_INTERNAL_ERROR 모든 오류에 시작 하는 "오류 Id:" | ADLA에서 오류 발생                                    | 이 예제에서는 작업 의미 처럼 보이는 오류 실패 한 ADLA 및 스크립트를 제출 되었습니다. ADLA에서 조사를 수행 되어야 합니다. 포털을 열고 ADLA 계정으로 이동 하는 경우 Id (없습니다 파이프라인 실행 Id)를 실행 ADF 활동을 사용 하 여 작업에 대 한 확인 합니다. 작업을는 오류에 대 한 자세한 내용은 있고 문제를 해결 하는 데 도움이 됩니다. 확인 되지 않는 경우 ADLA 지원 팀에 문의 후 계정 이름 및 작업 ID를 포함 하는 작업 URL을 제공 합니다 |
| 2709                 | 지금은 작업을 수락할 수 없습니다 했습니다. 계정에 대 한 큐에 대기 중인된 작업의 최대 수는 200 개입니다. | ADLA에서 제한                                           | ADLA를 ADF 트리거 및 작업에 따라 동시성 설정을 변경 하 여 제출 된 작업의 수를 줄이거나 ADLA에서 제한을 늘리려면 |
| 2709                 | 이 작업은 24 필요 하기 때문에 거부 된 Au 및이 계정에 작업을 5 개를 사용 하는 것을 방지 하는 관리자가 정의한 정책이 Au입니다. | ADLA에서 제한                                           | ADLA를 ADF 트리거 및 작업에 따라 동시성 설정을 변경 하 여 제출 된 작업의 수를 줄이거나 ADLA에서 제한을 늘리려면 |



## <a name="azure-functions"></a>Azure 기능

| 오류 코드 | 오류 메시지                           | 설명                                                  | 가능한 수정 / 권장 작업                           |
| ------------ | --------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 3600         | 응답 콘텐츠 유효한 JObject 아닙니다. | 즉, Azure를 호출한 함수로 얻은 응답에 JSON 페이로드를 반환 하지 않았습니다. ADF Azure 함수 활동 JSON 응답 콘텐츠를만 지원합니다. | 예를 들어 유효한 JSON 페이로드를 반환 하는 Azure 함수를 업데이트는 C# 함수는 새 (ActionResult)를 반환할 수 있습니다 < OkObjectResult ("{`\"Id\":\"123\"`}"); |
| 3600         | 잘못 된 HttpMethod: '... '입니다.               | 즉, Azure 함수 작업에서 작업 페이로드에 지정 된 Http 메서드가 지원 되지 않습니다. | 지원 되는 Http 메서드는 다음과 같습니다.  <br/>PUT, POST, 가져오기, 삭제, 옵션, HEAD, 추적 |



## <a name="custom-azure-batch"></a>사용자 지정 (Azure Batch)
| 오류 코드 | 오류 메시지                                                | 설명                                                  | 가능한 수정 / 권장 작업                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2500         | 예기치 않은 예외를 적중 횟수 및 실행에 실패 했습니다.             | 명령 또는 오류 코드를 반환 하는 프로그램을 시작할 수 없습니다. | 실행 파일이 있는지 확인 합니다. 프로그램 시작 하는 경우에 stdout.txt 및 stderr.txt 저장소 계정에 업로드를 확인 합니다. 디버깅에 대 한 코드에서 복사 로그를 내보내는 것이 좋습니다. |
| 2501         | 사용자 일괄 처리 계정에 액세스할 수 있습니다 일괄 처리 계정 설정을 확인 하세요. | 잘못 된 일괄 처리 액세스 키 또는 풀 이름이 제공 되었습니다.            | 풀 이름 및 연결된 된 서비스에서 일괄 처리 액세스 키를 확인 해야 합니다. |
| 2502         | 수 하지 액세스 사용자 저장소 계정, 하세요 저장소 계정 설정을 확인 하십시오. | 잘못 된 저장소 계정 이름 또는 액세스 키 제공 합니다.       | 연결 된 서비스에서 저장소 계정 이름과 액세스 키를 확인 해야 합니다. |
| 2504         | 작업 'BadRequest' 잘못 된 상태 코드를 반환 했습니다.     | FolderPath에 너무 많은 파일이 경우 사용자 지정 활동입니다.  (ResourceFiles의 총 크기 이상 32768 자 이어야 합니다.) | 불필요 한 파일을 제거 하거나 파일로 압축 하 고, 추출, 예를 들어 압축 풀기를 명령을 추가: powershell.exe-nologo-noprofile-명령은 "& {Add-type-를 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;  $folder\yourProgram.exe |
| 2505         | 계정 키 자격 증명을 사용 하지 않는 한 공유 액세스 서명을 만들 수 없습니다. | 사용자 지정 활동 액세스 키를 사용 하는 저장소 계정을 지원 합니다. | 설명을 참조 하세요.                                            |
| 2507         | 폴더 경로가 존재 하지 않거나 비어:...            | 지정된 된 경로에 저장소 계정에 파일이 없습니다.       | FolderPath는 실행 하려는 실행 파일을 포함 해야 합니다. |
| 2508         | 리소스 폴더에 있는 중복 파일이 있습니다.               | FolderPath의 다른 하위 폴더에 이름이 같은 여러 파일이 있습니다. | FolderPath에서 폴더 구조를 평면화 하는 사용자 지정 활동입니다.  파일 압축 및 압축을 풉니다 Azure Batch에는 압축 풀기 명령을 사용 하 여 예를 들어 폴더 구조를 유지 해야 하는 경우: powershell.exe-nologo-noprofile-명령은 "& {Add-type-를 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory ($zipFile, $folder); }" ;   $folder\yourProgram.exe |
| 2509         | 일괄 처리 url... 잘못 된 경우 해당 Uri 형식 이어야 합니다.         | 일괄 처리 Url 유사 해야 합니다. https://mybatchaccount.eastus.batch.azure.com | 설명을 참조 하세요.                                            |
| 2510         | 요청을 보내는 동안 오류가 발생 했습니다.               | 일괄 처리 URL 올바르지 않습니다.                                         | 일괄 처리 URL을 확인 합니다.                                            |

## <a name="hdinsight-spark-hive-mapreduce-pig-hadoop-streaming"></a>HDInsight (Spark, Hive, MapReduce, Pig, Hadoop 스트리밍)

| 오류 코드 | 오류 메시지                                                | 설명                                                  | 가능한 수정 / 권장 작업                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2300,   2310 | Hadoop 작업 제출 하지 못했습니다. 오류: 원격 이름을 확인할 수 없습니다. <br/><br/>클러스터를 찾을 수 없습니다. | 제공 된 클러스터 URI 올바르지 않습니다.                              | 클러스터 삭제 되지 않은, 하 고 제공 된 URI가 올바른지 확인 하십시오. 모든 브라우저에서 URI를 열고 Ambari UI를 표시 합니다. 클러스터가 vNet에 다음 URI는 개인 URI 있어야 하 고 동일한 vNet의 일부인 VM에서 여는 것이 좋습니다. 에 대 한 자세한 내용은 [가상 네트워크에 HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services)합니다. |
| 2,300         | Hadoop 작업 제출 하지 못했습니다. 작업:..., 클러스터:... /입니다. 오류: 작업이 취소 되었습니다. | 작업 제출 시간이 초과 되었습니다.                         | 일반적인 HDInsight 연결 문제 또는 네트워크 연결 문제가 수 있습니다. 먼저 HDInsight Ambari UI는 모든 브라우저를 통해 사용할 수 있습니다 하 고 자격 증명 여전히 유효한 지 확인 합니다. 이렇게 하려면 V m/컴퓨터에서 자체 호스팅된 IR.를 사용 하는 경우 자체 호스팅된 IR 설치 되어 있는 있는지 확인 ADF에서 작업을 다시 전송 시도 합니다. 그래도 실패할 경우 지원에 대 한 ADF 팀에 문의 합니다. |
| 2,300         | 권한 없음:   Ambari 사용자 이름 또는 암호가 올바르지 않습니다.  <br/><br/>권한 없음:   Ambari에서 사용자 관리를 잠그기   <br/><br/>403 -   Forbidden: 액세스가 거부 되었습니다. | 제공 된 자격 증명은 HDInsight에 대 한 잘못 된 또는 만료 됨 | 수정 하 고 연결 된 서비스를 다시 배포 하세요. 자격 증명을 올바르게 작동 하는지 HDInsight에서 먼저 모든 브라우저에서 클러스터 URI를 열고 로그인 하려고 합니다. 작동 하지 않는 경우 Azure Portal에서 다시 설정할 수 있습니다. |
| 2300,   2310 | 502-웹 서버가 게이트웨이 또는 프록시 서버를 작동 하는 동안 잘못 된 응답을 수신       <br/>잘못된 게이트웨이 | HDInsight에서 오류 발생                               | 이 오류는 HDInsight 클러스터에서 제공 됩니다. 참조할 [HDInsight 문제 해결사](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) 일반적인 오류가 발생 합니다.    <br/>Spark 클러스터에 대 한 것도 원인이로 인해 [이](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)합니다. <br/><br/>[추가 링크](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502) |
| 2,300         | Hadoop 작업 제출 하지 못했습니다. 작업:..., 클러스터:... 오류: {\"오류\":\"templeton 서비스 제출 작업 요청을 너무 많이 사용 중 이므로 그대로 제출 작업 요청을 처리할 수 없습니다. 작업을 다시 시도 하기 전에 잠시 기다리세요. 동시 요청을 구성 하려면 구성 templeton.parallellism.job.submit를 참조 하십시오. \  <br/><br/>Hadoop 작업 제출 하지 못했습니다. 작업: 161da5d4-6fa8-4ef4-a240-6b6428c5ae2f, 클러스터: https://abc-analytics-prod-hdi-hd-trax-prod01.azurehdinsight.net/ 합니다.   오류: {\"오류\":\"java.io.IOException: org.apache.hadoop.yarn.exceptions.YarnException: Application_1561147195099_3730 YARN에 제출 하지 못했습니다: org.apache.hadoop.security.AccessControlException: 큐 root.joblauncher 이미 500 응용 프로그램에 응용 프로그램의 전송을 허용할 수 없습니다: application_1561147195099_3730\ | 너무 많은 작업이 동시에 HDInsight에 전송 되 | HDI에 전송 되는 동시 작업 수를 제한 하는 것이 좋습니다. 동일한 활동에 의해 전송 되는 경우 ADF 작업 동시성을 참조 하십시오. 트리거를 변경 하 여 동시 파이프라인 실행 시간에 걸쳐 분산 됩니다. 또한 오류 알 수 있듯이 "templeton.parallellism.job.submit"를 조정 하기 위해 HDInsight 문서를 참조 하십시오. |
| 2303,   2347 | '5' 종료 코드로 Hadoop 작업이 실패 했습니다. 참조 'wasbs://adfjobs@adftrialrun.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' 대 한 자세한 내용은 합니다.  <br/><br/>오류 코드 'UserErrorHiveOdbcCommandExecutionFailure'를 사용 하 여 hive 실행 하지 못했습니다.   참조 'wasbs://adfjobs@eclsupplychainblobd.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' 대 한 자세한 내용은 | HDInsight에서에 작업이 제출 된 및 HDInsight에 실패 한 | 작업이는 성공적으로 제출 HDInsight를 되었습니다. 클러스터에서 실패 합니다. 하세요 HDInsight Ambari ui에서 작업을 엽니다 및, 로그 또는 오류 메시지 지적으로 저장소에서 파일을 엽니다. 오류의 세부 정보는 해당 파일에 있게 됩니다. |
| 2328         | 요청을 처리 하는 동안 내부 서버 오류가 발생 했습니다. 요청을 다시 시도 하거나 지원에 문의 하세요. | 주문형 HDInsight에서 발생합니다.                              | 이 오류는 HDInsight 프로 비전이 실패 하는 경우 HDInsight 서비스에서 제공 됩니다. HDInsight 팀에 문의 하 고에서 필요 시 클러스터 이름을 제공 하세요. |
| 2310         | java.lang.NullPointerException                               | Spark 클러스터에 작업을 전송 하는 동안 오류가 발생 했습니다.      | 이 예외 HDInsight에서 가져오고 실제 문제를 숨기면 됩니다.   HDInsight 팀 지원에 문의 하 고 클러스터 이름 및 시간 범위를 실행 하는 활동을 제공 하세요. |
|              | 다른 모든 오류                                             |                                                              | 참조 하십시오 [HDInsight 문제 해결사](../hdinsight/hdinsight-troubleshoot-guide.md) 고 [HDInsight FAQ](https://hdinsight.github.io/) |



## <a name="web-activity"></a>웹 작업

| 오류 코드 | 오류 메시지                                                | 설명                                                  | 가능한 수정 / 권장 작업                           |
| ------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| 2108         | 잘못 된 HttpMethod: '... '입니다.                                    | 즉, 작업 페이로드에 지정 된 Http 메서드는 웹 작업에서 지원 되지 않습니다. | 지원 되는 Http 메서드는 다음과 같습니다. <br/>PUT, POST, GET, 삭제 |
| 2108         | 잘못 된 서버 오류 500                                     | 끝점에서 내부 오류                               | URL (Fiddler/Postman) 기능을 확인 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 권한 없음된 401                                             | 요청 시 누락 된 유효한 인증                      | 유효한 인증 메서드를 제공 (토큰을 만료 되었을 수 있습니다.).   <br/><br/>URL (Fiddler/Postman) 기능을 확인 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 403 사용할 수 없음된                                                | 필요한 사용 권한이 없습니다.                                 | 액세스할된 리소스에 대 한 사용자 권한을 확인 합니다.   <br/><br/>URL (Fiddler/Postman) 기능을 확인 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 400 잘못 된 요청                                              | 잘못 된 Http 요청                                         | URL, 동사 및 요청 본문을 확인 합니다.   <br/><br/>요청의 유효성을 검사할 Fiddler/Postman을 사용 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 404 찾을 수 없음                                                | 리소스를 찾을 수 없습니다.                                       | 요청의 유효성을 검사할 Fiddler/Postman을 사용 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 서비스를 사용할 수 없음                                          | 서비스를 사용할 수 없음                                       | 요청의 유효성을 검사할 Fiddler/Postman을 사용 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 지원 되지 않는 미디어 유형                                       | 웹 작업 본문을 사용 하 여 일치 하지 않는 콘텐츠 형식           | 요청 유효성 검사를 사용 하 여 Fiddler/Postman 페이로드 형식을 일치 하는 올바른 콘텐츠 형식을 지정 합니다. [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 찾고 있는 리소스 제거 된에 대 한 이름이 변경, 또는 일시적으로 사용할 수 없습니다. | 리소스를 사용할 수 없습니다.                                | Fiddler/Postman을 사용 하 여 끝점을 확인 하려면: [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | 잘못 된 메서드 (HTTP 동사)을 사용 중 이므로 원하는 페이지를 표시할 수 없습니다. | 잘못 된 웹 작업 메서드는 요청에 지정 된   | Fiddler/Postman을 사용 하 여 끝점을 확인 하려면: [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |
| 2108         | invalid_payload                                              | 웹 작업의 본문은 올바르지 않습니다.                       | Fiddler/Postman을 사용 하 여 끝점을 확인 하려면: [Fiddler를 사용 하 여 HTTP 세션을 만드는 방법](#how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application) |

#### <a name="how-to-use-fiddler-to-create-an-http-session-of-the-monitored-web-application"></a>Fiddler를 사용 하 여 모니터링 된 웹 응용 프로그램의 HTTP 세션을 만드는 방법

1. [Fiddler](https://www.telerik.com/download/fiddler) 다운로드 및 설치

2. 웹 응용 프로그램이 HTTPS를 사용 합니다.

   1. 열기 Fiddler

   2. 로 이동 **도구 > Fiddler 옵션** 아래 스크린샷과 같이 선택 합니다. 

      ![fiddler-options](media/data-factory-troubleshoot-guide/fiddler-options.png)

3. 응용 프로그램에서 SSL 인증서를 사용 하는 경우 장치에 Fiddler 인증서도 추가 해야 합니다.

4. 장치에 Fiddler 인증서를 추가 하려면로 이동 **도구가** > **Fiddler 옵션** > **HTTPS**  >   **작업** > **데스크톱에 루트 인증서 내보내기** Fiddler 인증서를 가져와야 합니다.

5. 새 추적을 시작 하려면 브라우저의 캐시를 지울 수 있습니다 있도록 캡처를 해제 합니다.

6. 1. 로 이동 **파일** > **Capture Traffic** 누르거나 **F12**합니다.
   2. 모든 캐시 된 항목이 제거 되 고 다시 다운로드 해야 되도록 브라우저의 캐시를 지웁니다.

7. 요청을 만듭니다. 

8. 1. Composer 탭 클릭
   2. Http 메서드 및 URL을 설정 합니다.
   3. 헤더를 추가 하 고 필요한 경우 요청 본문
   4. 실행을 클릭

9. 트래픽을 다시 캡처를 시작 하 고 페이지에 문제가 있는 트랜잭션을 완료 합니다.

10. 완료 되 면 이동할 **파일** > **저장** > **모든 세션**합니다.

Fiddler에 자세한 내용은 [여기](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>다음 단계

문제 해결 방법을 찾는 데 도움이 필요한 경우 다음과 같은 리소스를 사용해 보세요.

*  [블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [스택 오버플로](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter](https://twitter.com/hashtag/DataFactory)



