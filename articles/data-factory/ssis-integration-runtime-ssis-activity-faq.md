---
title: SSIS integration runtime에서 패키지 실행 문제 해결 | Microsoft Docs
description: 이 문서에서는 SSIS integration runtime에서 SSIS 패키지 실행에 대 한 문제 해결 지침을 제공합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/15/2019
author: wenjiefu
ms.author: wenjiefu
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 05723a90725992e6b955524a2d35c82d3378ee3d
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621849"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>SSIS integration runtime에서 패키지 실행 문제 해결

이 문서에서는 SSIS integration runtime에서 SQL Server Integration Services (SSIS) 패키지를 실행 하는 경우 발생할 수 있는 가장 일반적인 오류를 포함 합니다. 이 오류를 해결 하기 위해 잠재적 원인과 작업 방법을 설명 합니다.

## <a name="where-to-find-logs-for-troubleshooting"></a>문제 해결에 대 한 로그를 찾을 수 있는 위치

Azure Data Factory 포털을 사용 하 여 SSIS 패키지 실행 작업의 출력을 확인 합니다. 출력은 포함 실행 결과, 오류 메시지 및 작업 id입니다. 자세한 내용은 참조 하세요 [파이프라인 모니터링](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)합니다.

SSIS 카탈로그 (SSISDB)를 사용 하 여 실행에 대 한 세부 정보 로그를 확인 합니다. 자세한 내용은 참조 하세요 [모니터가 실행 중인 패키지 및 기타 작업](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)합니다.

## <a name="common-errors-causes-and-solutions"></a>일반적인 오류, 원인 및 해결 방법

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>오류 메시지: "연결 제한 시간이 만료 되었습니다." 또는 "서비스 요청을 처리 하는 오류가 발생 했습니다. 다시 시도하세요."

잠재적 원인 및 권장된 조치 다음과 같습니다.
* 데이터 원본 또는 대상 오버 로드 됩니다. 데이터 원본 또는 대상에 로드를 확인 하 고 충분 한 용량이 있는지 확인 합니다. 예를 들어, Azure SQL Database를 사용한 경우에 데이터베이스 시간 초과 될 가능성이 강화 하는 것이 좋습니다.
* SSIS integration runtime 및 데이터 원본 또는 대상 간의 네트워크 연결 된 지역 간 또는 온-프레미스와 Azure 간에 하는 경우에 특히 안정적이 고 아닙니다. 다음 단계를 수행 하 여 SSIS 패키지에서 다시 시도 패턴을 적용 합니다.
  * SSIS 패키지 (예: 데이터 손실이 나 데이터 중복) 부작용 없이 실패 시 다시 실행할 수 있는지 확인 합니다.
  * 구성 **다시 시도** 및 **재시도 간격** 의 **SSIS 패키지 실행** 활동에는 **일반** 탭 합니다. ![일반 탭의 속성을 설정 합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * ADO.NET 및 OLE DB 원본 또는 대상 구성 요소에 대 한 설정 **ConnectRetryCount** 하 고 **ConnectRetryInterval** SSIS 작업을 SSIS 패키지에서 연결 관리자에서.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>오류 메시지: "ADO NET 원본에 '...' 연결을 설정 하지 못했습니다" 사용 하 여 "SQL Server에 연결을 설정 하는 동안 네트워크 관련 또는 인스턴스 관련 오류가 발생 합니다. 서버를 찾을 수 없거나 액세스할 수 없습니다. "

이 문제는 일반적으로 데이터 원본을 의미 없거나 대상 SSIS integration runtime에서 액세스할 수 있습니다. 이유 달라질 수 있습니다. 이러한 작업을 시도 합니다.
* 데이터 원본 또는 대상에 전달 하 고 있는지 확인 이름/i P 정확 합니다.
* 방화벽을 올바르게 설정 되어 있는지 확인 합니다.
* 가상 네트워크는 온-프레미스 데이터 원본 또는 대상 경우 올바르게 구성 되어 있는지 확인 합니다.
  * 동일한 가상 네트워크에 있는 Azure VM을 프로 비전 하 여 가상 네트워크 구성에서 문제 인지 여부를 확인할 수 있습니다. 데이터 원본 또는 대상 Azure VM에서 액세스할 수 있는지 여부를 확인 합니다.
  * SSIS integration runtime을 사용 하 여 가상 네트워크를 사용 하는 방법에 대 한 자세한 내용을 볼 수 있습니다 [가상 네트워크에 AZURE-SSIS 통합 런타임을 조인](join-azure-ssis-integration-runtime-virtual-network.md)합니다.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>오류 메시지: "ADO NET 원본에 '...' 연결을 설정 하지 못했습니다" 사용 하 여 "만들 수 없습니다 관리 되는 연결 관리자입니다."

잠재적 원인은 SSIS integration runtime에서 패키지에 사용 되는 ADO.NET 공급자 설치 되지 않습니다는입니다. 사용자 지정 설치를 사용 하 여 공급자를 설치할 수 있습니다. 사용자 지정 설치에 대 한 자세한 내용을 볼 수 있습니다 [AZURE-SSIS integration runtime에 대 한 설치를 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)합니다.

### <a name="error-message-the-connection--is-not-found"></a>오류 메시지: "연결 '...' 찾을 수 없습니다 "

이전 버전의 SQL Server Management Studio (SSMS)의 알려진된 문제에이 오류가 발생할 수 있습니다. SSMS 배포를 위해 여기서는 컴퓨터에 설치 되어 있지 않은 사용자 지정 구성 요소 (예를 들어, 파트너 또는 SSIS Azure Feature Pack 구성 요소)를 포함 하는 패키지, SSMS 구성 요소를 제거 되며 오류가 발생 합니다. 업그레이드 [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 고정 하는 문제가 있는 최신 버전으로 합니다.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>오류 메시지: "SSIS Executor 종료 코드:-1073741819."

* 잠재적 원인 및 권장된 작업:
  * 이 오류는 여러 Excel 원본 또는 대상에서 병렬 다중 스레드 실행 중일 때 Excel 원본 및 대상에 대 한 제한으로 인해 수 있습니다. 해결 방법으로이 제한을 변경 Excel 구성 요소를 순서 대로 실행 하거나 분리 하 여 서로 다른 패키지 및 "패키지 실행 태스크"를 통해 트리거 ExecuteOutOfProcess 속성을 True로 설정 하 여 할 수 있습니다.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>오류 메시지: "공간이 충분 한 디스크에"

이 오류는 SSIS integration runtime 노드가는 로컬 디스크를 의미 합니다. 에 패키지 또는 사용자 지정 설치 디스크 공간을 많이 사용 하는지 여부를 확인 합니다.
* 디스크에 패키지에서 사용 되는 경우 해당 인해 확보 될 패키지 실행이 완료 된 후입니다.
* 디스크에 사용자 지정 설치 프로그램에서 사용 되는 경우 필요한 SSIS integration runtime을 중지 하려면 스크립트를 수정 하 고 합니다 integration runtime을 다시 시작. 따라서 사용자 지정 설치 SSIS 통합 런타임 노드로 복사 됩니다에 대 한 지정 된 전체 Azure blob 컨테이너는 해당 컨테이너에서 불필요 한 내용을 인지 확인 합니다.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>오류 메시지: "마스터에서 리소스를 검색 하지 못했습니다. Microsoft.SqlServer.IntegrationServices.Scale.ScaleoutContract.Common.MasterResponseFailedException: 코드: 300004 합니다. 파일 설명: 로드 "*" 하지 못했습니다. "

* 잠재적 원인 및 권장된 작업:
  * SSIS 작업 패키지 (패키지 파일 또는 프로젝트 파일) 파일 시스템에서 실행 되는 프로젝트, 패키지 또는 구성 파일 SSIS 작업에서 제공한 패키지 액세스 자격 증명을 사용 하 여 액세스할 수 없는 경우이 오류가 발생 합니다.
    * Azure 파일을 사용 하는 경우
      * 파일 경로 시작 해야 \\ \\ \<저장소 계정 이름\>. file.core.windows.net\\\<파일 공유 경로\>
      * 도메인 "Azure" 해야 합니다.
      * 사용자 이름 이어야 합니다 \<저장소 계정 이름\>
      * 암호는 해야 \<저장소 액세스 키\>
    * 경우에 온-프레미스 파일을 사용 하 여 VNet, 패키지 액세스 자격 증명 및 권한을 AZURE-SSIS 통합 런타임을 온-프레미스 파일 공유에 액세스할 수 있도록 제대로 구성 하는 경우를 확인 하세요

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>오류 메시지: "파일 이름 '...' 에 지정 된 연결이 잘못 되었습니다. "

* 잠재적 원인 및 권장된 작업:
  * 잘못 된 파일 이름이 지정 된
  * 연결 관리자에서 짧은 시간 대신 FQDN (정규화 된 도메인 이름)을 사용 하 고 있는지 확인

### <a name="error-message-cannot-open-file-"></a>오류 메시지: "파일 열 수 없습니다..."

이 오류는 패키지 실행 SSIS integration runtime에서 로컬 디스크에 파일을 찾을 수 없을 때 발생 합니다. 이러한 작업을 시도 합니다.
* SSIS integration runtime에서 실행 되는 패키지의 절대 경로 사용 하지 마세요. 현재 실행 작업 디렉터리 (.) 또는 임시 폴더 (% TEMP %)를 사용 합니다. 대신 합니다.
* SSIS 통합 런타임 노드에서 일부 파일을 유지 해야 할 경우에 설명 된 대로 파일 준비 [설치를 사용자 지정](how-to-configure-azure-ssis-ir-custom-setup.md)합니다. 실행이 완료 되 면 작업 디렉터리의 모든 파일을 정리할 수 됩니다.
* SSIS integration runtime 노드가에 파일을 저장 하는 대신 Azure Files를 사용 합니다. 자세한 내용은 참조 하세요 [사용 하 여 Azure 파일 공유](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)합니다.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>오류 메시지: "'SSISDB' 데이터베이스 할당량에 도달한 크기"

잠재적 원인이 될 수는 SSIS integration runtime을 만들 때 Azure SQL database 또는 관리 되는 인스턴스 생성 SSISDB 데이터베이스 할당량에 도달 했습니다. 이러한 작업을 시도 합니다.
* 데이터베이스의 DTU를 늘려 보십시오. 세부 정보를 찾을 수 있습니다 [Azure SQL Database 서버에 대 한 SQL Database 리소스 제한](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)합니다.
* 패키지는 많은 로그를 생성 하는지 여부를 확인 합니다. 그렇다면 이러한 로그를 정리 하려면 탄력적 작업을 구성할 수 있습니다. 자세한 내용은 참조 하세요 [Azure Elastic Database 작업을 사용 하 여 SSISDB 로그 정리](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)합니다.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>오류 메시지: "데이터베이스에 대 한 요청 제한이 됩니다... 및에 도달 했습니다. "

많은 패키지를 SSIS integration runtime에서 병렬로 실행 하는 경우 SSISDB 요청 제한에 도달 하기 때문에이 오류가 발생할 수 있습니다. 이 문제를 해결 하려면 DTC의 SSISDB를 늘리는 것이 좋습니다. 세부 정보를 찾을 수 있습니다 [Azure SQL Database 서버에 대 한 SQL Database 리소스 제한](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)합니다.

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>오류 메시지: "SSIS 작업이 실패 했습니다. 예기치 않은 작업 상태:..."

일시적인 문제로 인해 대부분 오류가, 따라서 패키지 실행을 다시 실행 하려고 합니다. 다음 단계를 수행 하 여 SSIS 패키지에서 다시 시도 패턴을 적용 합니다.

* SSIS 패키지 (예: 데이터 손실이 나 데이터 중복) 부작용 없이 실패 시 다시 실행할 수 있는지 확인 합니다.
* 구성 **다시 시도** 및 **재시도 간격** 의 **SSIS 패키지 실행** 활동에는 **일반** 탭 합니다. ![일반 탭의 속성을 설정 합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* ADO.NET 및 OLE DB 원본 또는 대상 구성 요소에 대 한 설정 **ConnectRetryCount** 하 고 **ConnectRetryInterval** SSIS 작업을 SSIS 패키지에서 연결 관리자에서.

### <a name="error-message-there-is-no-active-worker"></a>오류 메시지: "이 활성 작업 자가 없습니다."

이 오류는 일반적으로 SSIS integration runtime에 비정상 상태를 의미 합니다. 상태 및 오류 세부 정보에 대 한 Azure portal을 확인 합니다. 자세한 내용은 [AZURE-SSIS 통합 런타임을](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)합니다.

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>오류 메시지: "Integration runtime 업그레이드할 수 없습니다 및 작업을 사용자 지정 설치에 대해 제공한 Azure Blob 컨테이너에 액세스할 수 없습니다 때문 결국 중지 됩니다."

SSIS 통합 런타임 사용자 지정 설치에 대 한 구성 저장소에 액세스할 수 없습니다.이 오류가 발생 합니다. 공유 액세스 서명 (SAS) URI 제공한 올바르고 만료 되지 않았는지 여부를 확인 합니다.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>오류 메시지: "Microsoft OLE DB Provider for Analysis Services입니다. ' Hresult: 0x80004005 설명:' COM 오류: COM 오류: mscorlib; 호출 대상이 예외를 throw 했습니다 "

한 가지 원인은 사용자 이름 또는 암호를 사용 하도록 설정 하는 Azure Multi-factor Authentication을 사용 하 여 Azure Analysis Services 인증에 대 한 구성 된 것입니다. 이 인증 SSIS integration runtime에서 지원 되지 않습니다. Azure Analysis Services 인증에 대 한 서비스 주체를 사용 하려고 합니다.
1. 에 설명 된 대로 서비스 주체를 준비 [서비스 주체를 사용 하 여 자동화](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)합니다.
2. 연결 관리자에서 구성할 **특정 사용자 이름 및 암호를 사용 하 여**: 설정 **AppID** 사용자 이름으로 및 **clientSecret** 암호로 합니다.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>오류 메시지: "ADONET 원본이 다음과 같은 오류 메시지가 {GUID} 연결을 설정 하지 못했습니다. 사용자 ' NT AUTHORITY\ANONYMOUS LOGON' 로그인 하지 못했습니다 "관리 되는 id를 사용 하는 경우

인증 방법으로 코드의 연결 관리자를 구성 하지 했는지 **Active Directory 암호 인증** 때 매개 변수 *ConnectUsingManagedIdentity* 는 **True** . 로 구성할 수 있습니다 **SQL 인증** 대신, 경우에 무시 됩니다 *ConnectUsingManagedIdentity* 설정 됩니다.

### <a name="package-execution-takes-too-long"></a>패키지 실행 시간이 너무 오래 걸리는 경우

잠재적 원인 및 권장된 조치 다음과 같습니다.
* SSIS integration runtime에서 너무 많은 패키지 실행 예약 되었습니다. 이러한 모든 실행 해당 설정에 대 한 큐에 대기 됩니다.
  * 이 수식을 사용 하 여 최대값을 결정 합니다. 
    
    최대 병렬 실행 개수 IR 노드 수 = * 노드당 최대 병렬 실행
  * 노드 수 및 노드당 최대 병렬 실행을 설정 하는 방법에 알아보려면 참조 [Azure Data Factory에서 AZURE-SSIS 통합 런타임을 만드는](create-azure-ssis-integration-runtime.md)합니다.
* SSIS 통합 런타임을 중지 되었거나 비정상 상태의 합니다. SSIS 통합 런타임 상태 및 오류를 확인 하는 방법에 알아보려면 참조 [AZURE-SSIS 통합 런타임을](monitor-integration-runtime.md#azure-ssis-integration-runtime)합니다.

제한 시간을 설정 하는 것이 좋습니다 합니다 **일반** 탭: ![일반 탭의 속성을 설정할](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)합니다.

### <a name="poor-performance-in-package-execution"></a>패키지 실행에서 성능 저하

이러한 작업을 시도 합니다.

* SSIS integration runtime 데이터 원본 및 대상으로 동일한 지역에 있는지 확인 합니다.

* 패키지 실행의 로깅 수준을 설정할 **성능** 실행의 각 구성 요소에 대 한 기간 정보를 수집 합니다. 자세한 내용은 참조 하세요 [Integration Services (SSIS) 로깅](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)합니다.

* Azure portal에서 IR 노드의 성능을 확인 합니다.
  * SSIS integration runtime을 모니터링 하는 방법에 대 한 정보를 참조 하세요 [AZURE-SSIS 통합 런타임을](monitor-integration-runtime.md#azure-ssis-integration-runtime)합니다.
  * Azure portal에서 data factory의 메트릭을 확인 하 여 SSIS 통합 런타임에 대 한 기록을 CPU/메모리를 찾을 수 있습니다.
    ![SSIS integration runtime의 메트릭 모니터링](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
