---
title: SSIS 통합 런타임에서 패키지 실행 문제 해결
description: 이 문서에서는 SSIS 통합 런타임에서 SSIS 패키지 실행을 위한 문제 해결 지침을 제공합니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: wenjiefu
author: wenjiefu
ms.reviewer: sawinark
manager: shwang
ms.custom: seo-lt-2019
ms.date: 04/15/2019
ms.openlocfilehash: 1c2db107302e4851641ef430db61ec9b29ee151f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187472"
---
# <a name="troubleshoot-package-execution-in-the-ssis-integration-runtime"></a>SSIS 통합 런타임에서 패키지 실행 문제 해결

이 문서에는 SSIS 통합 런타임에서 SSIS(SQL Server 통합 서비스) 패키지를 실행할 때 찾을 수 있는 가장 일반적인 오류가 포함되어 있습니다. 오류를 해결하기 위한 잠재적인 원인과 작업을 설명합니다.

## <a name="where-to-find-logs-for-troubleshooting"></a>문제 해결을 위한 로그를 찾을 수 있는 위치

Azure Data Factory 포털을 사용하여 SSIS 패키지 실행 활동의 출력을 확인합니다. 출력에는 실행 결과, 오류 메시지 및 작업 ID가 포함됩니다. 자세한 내용은 [파이프라인 모니터](how-to-invoke-ssis-package-ssis-activity.md#monitor-the-pipeline)를 참조하십시오.

SSIS 카탈로그(SSISDB)를 사용하여 실행에 대한 세부 정보 로그를 확인합니다. 자세한 내용은 [실행 중인 패키지 및 기타 작업 모니터를](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017)참조하십시오.

## <a name="common-errors-causes-and-solutions"></a>일반적인 오류, 원인 및 해결 방법

### <a name="error-message-connection-timeout-expired-or-the-service-has-encountered-an-error-processing-your-request-please-try-again"></a>오류 메시지: "연결 시간 만료가 만료되었습니다" 또는 "서비스가 요청을 처리하는 오류가 발생했습니다. 다시 시도하세요”와 같은 오류로 인해 실패합니다.

잠재적인 원인과 권장 조치는 다음과 같습니다.
* 데이터 원본 또는 대상이 오버로드됩니다. 데이터 원본 또는 대상의 부하를 확인하고 용량이 충분한지 확인합니다. 예를 들어 Azure SQL Database를 사용한 경우 데이터베이스가 시간 중지될 가능성이 있는 경우 확장하는 것이 좋습니다.
* SSIS 통합 런타임과 데이터 원본 또는 대상 간의 네트워크는 특히 연결이 지역 간 또는 온-프레미스와 Azure 간에 있는 경우 불안정합니다. 다음 단계를 수행하여 SSIS 패키지에 다시 시도 패턴을 적용합니다.
  * SSIS 패키지가 부작용 없이 오류(예: 데이터 손실 또는 데이터 중복)를 다시 실행할 수 있는지 확인합니다.
  * **일반** 탭에서 **SSIS 패키지 실행** 활동의 **재시도** 및 ![ **재시도 간격** 을 구성합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
  * ADO.NET 및 OLE DB 소스 또는 대상 구성 요소의 경우 SSIS 패키지 또는 SSIS 활동의 연결 관리자에서 **ConnectRetryCount** 및 **ConnectRetryInterval을** 설정합니다.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-sql-server-the-server-was-not-found-or-was-not-accessible"></a>오류 메시지: "ADO NET Source에서 연결을 획득하지 못했습니다....'" "SQL Server에 대한 연결을 설정하는 동안 네트워크 관련 또는 인스턴스별 오류가 발생했습니다. 서버를 찾을 수 없거나 액세스할 수 없습니다."

이 이슈는 일반적으로 SSIS 통합 런타임에서 데이터 원본 또는 대상에 액세스할 수 없음을 의미합니다. 원인은 다양합니다. 다음과 같이 작업해 보세요.
* 데이터 원본 또는 대상 이름/IP를 올바르게 전달하고 있는지 확인합니다.
* 방화벽이 제대로 설정되어 있는지 확인합니다.
* 데이터 원본 또는 대상이 온-프레미스인 경우 가상 네트워크가 제대로 구성되었는지 확인합니다.
  * 동일한 가상 네트워크에서 Azure VM을 프로비전하여 가상 네트워크 구성에서 문제가 발생했는지 확인할 수 있습니다. 그런 다음 Azure VM에서 데이터 원본 또는 대상에 액세스할 수 있는지 확인합니다.
  * [Azure-SSIS 통합 런타임에](join-azure-ssis-integration-runtime-virtual-network.md)가상 네트워크 가입에서 SSIS 통합 런타임이 있는 가상 네트워크 사용에 대한 자세한 내용을 찾을 수 있습니다.

### <a name="error-message-ado-net-source-has-failed-to-acquire-the-connection--with-could-not-create-a-managed-connection-manager"></a>오류 메시지: "ADO NET Source에서 연결을 획득하지 못했습니다....'" "관리되는 연결 관리자를 만들 수 없습니다."

잠재적인 원인은 패키지에 사용된 ADO.NET 공급자가 SSIS 통합 런타임에 설치되지 않기 때문에 발생할 수 있습니다. 사용자 지정 설정을 사용하여 공급자를 설치할 수 있습니다. [Azure-SSIS 통합 런타임에 대한 사용자 지정 설정에서](how-to-configure-azure-ssis-ir-custom-setup.md)사용자 지정 설정에 대한 자세한 내용을 찾을 수 있습니다.

### <a name="error-message-the-connection--is-not-found"></a>오류 메시지: "연결 '...' 찾을 수 없습니다."

이 오류는 이전 버전의 SSMS(SQL Server Management Studio)에서 알려진 이슈로 인해 발생할 수 있습니다. 패키지에 SSMS를 사용하여 배포를 수행하는 컴퓨터에 설치되지 않은 사용자 지정 구성 요소(예: SSIS Azure Feature Pack 또는 파트너 구성 요소)가 포함된 경우 SSMS는 구성 요소를 제거하고 오류를 발생시킵니다. 문제가 해결된 최신 버전으로 [SSMS를](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 업그레이드합니다.

### <a name="error-messagessis-executor-exit-code--1073741819"></a>오류 메시지:"SSIS 실행기 종료 코드: -1073741819."

* 잠재적인 원인 & 권장 조치:
  * 이 오류는 여러 Excel 원본 또는 대상이 다중 스레드에서 병렬로 실행되는 경우 Excel 원본 및 대상에 대한 제한 때문일 수 있습니다. Excel 구성 요소를 순서대로 실행하도록 변경하거나 ExecuteOutOfProcess 속성을 True로 설정한 "패키지 작업 실행"을 통해 다른 패키지로 분리하고 트리거하여 이 제한을 해결할 수 있습니다.

### <a name="error-message-there-is-not-enough-space-on-the-disk"></a>오류 메시지: "디스크에 공간이 부족합니다."

이 오류는 로컬 디스크가 SSIS 통합 런타임 노드에서 사용된임을 의미합니다. 패키지 또는 사용자 지정 설정에 많은 디스크 공간이 소모되는지 확인합니다.
* 패키지에서 디스크를 사용하는 경우 패키지 실행이 완료되면 디스크가 해제됩니다.
* 사용자 지정 설정에서 디스크를 사용하는 경우 SSIS 통합 런타임을 중지하고 스크립트를 수정한 다음 통합 런타임을 다시 시작해야 합니다. 사용자 지정 설정에 대해 지정한 전체 Azure Blob 컨테이너가 SSIS 통합 런타임 노드로 복사되므로 해당 컨테이너 아래에 불필요한 콘텐츠가 있는지 확인합니다.

### <a name="error-message-failed-to-retrieve-resource-from-master-microsoftsqlserverintegrationservicesscalescaleoutcontractcommonmasterresponsefailedexception-code300004-descriptionload-file--failed"></a>오류 메시지: "마스터에서 리소스를 검색하지 못했습니다. Microsoft.SqlServer.통합 서비스.Scale.Scaleout계약.일반.마스터응답 실패 예외: 코드:300004. 설명/ 컨트롤: 로드 파일 "***"에 실패했습니다."

* 잠재적인 원인 & 권장 조치:
  * SSIS 활동이 파일 시스템(패키지 파일 또는 프로젝트 파일)에서 패키지를 실행하는 경우 SSIS 활동에 제공한 패키지 액세스 자격 증명으로 프로젝트, 패키지 또는 구성 파일에 액세스할 수 없는 경우 이 오류가 발생합니다.
    * Azure 파일을 사용하는 경우:
      * 파일 경로저장소 계정 \\ \\ \<이름\>.file.core.windows.net\\\<파일 공유 경로로 시작해야 합니다.\>
      * 도메인은 "Azure"여야 합니다.
      * 사용자 이름은 저장소 \<계정 이름이어야 합니다.\>
      * 암호는 저장소 \<액세스 키여야 합니다.\>
    * 온-프레미스 파일을 사용하는 경우 Azure-SSIS 통합 런타임이 온-프레미스 파일 공유에 액세스할 수 있도록 VNet, 패키지 액세스 자격 증명 및 사용 권한이 제대로 구성되었는지 확인하십시오.

### <a name="error-message-the-file-name--specified-in-the-connection-was-not-valid"></a>오류 메시지: "파일 이름 '...' 연결에 지정되어 있지 않습니다."

* 잠재적인 원인 & 권장 조치:
  * 잘못된 파일 이름이 지정되었습니다.
  * 연결 관리자에서 짧은 시간 대신 FQDN(정규화된 도메인 이름)을 사용하고 있는지 확인합니다.

### <a name="error-message-cannot-open-file-"></a>오류 메시지: "파일을 열 수 없습니다 '...'"

이 오류는 패키지 실행이 SSIS 통합 런타임의 로컬 디스크에서 파일을 찾을 수 없을 때 발생합니다. 다음과 같이 작업해 보세요.
* SSIS 통합 런타임에서 실행되는 패키지의 절대 경로를 사용하지 마십시오. 현재 실행 작업 디렉토리(.) 또는 임시 폴더(%TEMP%)를 사용합니다. 대신.
* SSIS 통합 런타임 노드에서 일부 파일을 유지해야 하는 경우 [사용자 지정 설정에](how-to-configure-azure-ssis-ir-custom-setup.md)설명된 대로 파일을 준비합니다. 실행이 완료되면 작업 디렉토리의 모든 파일이 정리됩니다.
* SSIS 통합 런타임 노드에 파일을 저장하는 대신 Azure 파일을 사용합니다. 자세한 내용은 [Azure 파일 공유 사용을](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-files-file-shares?view=sql-server-2017#use-azure-file-shares)참조하십시오.

### <a name="error-message-the-database-ssisdb-has-reached-its-size-quota"></a>오류 메시지: "데이터베이스 'SSISDB'가 크기 할당량에 도달했습니다."

Azure SQL 데이터베이스에 만든 SSISDB 데이터베이스 또는 SSIS 통합 런타임을 만들 때 관리되는 인스턴스가 할당량에 도달했기 때문일 수 있습니다. 다음과 같이 작업해 보세요.
* 데이터베이스의 DTU를 늘리는 것이 좋습니다. 자세한 내용은 [Azure SQL Database 서버의 SQL Database 리소스 한도](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)를 참조하세요.
* 패키지에서 많은 로그를 생성할지 여부를 확인합니다. 많은 로그를 생성할 경우 이러한 로그를 정리하도록 탄력적 작업을 구성할 수 있습니다. 자세한 내용은 [Azure Elastic Database 작업을 사용하여 SSISDB 로그 정리](how-to-clean-up-ssisdb-logs-with-elastic-jobs.md)를 참조하세요.

### <a name="error-message-the-request-limit-for-the-database-is--and-has-been-reached"></a>오류 메시지: "데이터베이스에 대한 요청 제한은 ... 도달했습니다."

SSIS 통합 런타임에서 많은 패키지가 병렬로 실행되는 경우 SSISDB가 요청 제한에 도달했기 때문에 이 오류가 발생할 수 있습니다. 이 문제를 해결 하려면 SSISDB의 DTC를 늘리는 것이 좋습니다. 자세한 내용은 [Azure SQL Database 서버의 SQL Database 리소스 한도](https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-logical-server)를 참조하세요.

### <a name="error-message-ssis-operation-failed-with-unexpected-operation-status-"></a>오류 메시지: "SSIS 작업이 예기치 않은 작업 상태로 실패했습니다.

이 오류는 주로 일시적인 문제로 인해 발생하므로 패키지 실행을 다시 실행해 보십시오. 다음 단계를 수행하여 SSIS 패키지에 다시 시도 패턴을 적용합니다.

* SSIS 패키지가 부작용 없이 오류(예: 데이터 손실 또는 데이터 중복)를 다시 실행할 수 있는지 확인합니다.
* **일반** 탭에서 **SSIS 패키지 실행** 활동의 **재시도** 및 ![ **재시도 간격** 을 구성합니다.](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)
* ADO.NET 및 OLE DB 소스 또는 대상 구성 요소의 경우 SSIS 패키지 또는 SSIS 활동의 연결 관리자에서 **ConnectRetryCount** 및 **ConnectRetryInterval을** 설정합니다.

### <a name="error-message-there-is-no-active-worker"></a>오류 메시지: "활성 작업자가 없습니다."

이 오류는 일반적으로 SSIS 통합 런타임에 비정상 상태가 됨을 의미합니다. Azure 포털에서 상태 및 자세한 오류를 확인합니다. 자세한 내용은 [Azure-SSIS 통합 런타임을](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)참조하십시오.

### <a name="error-message-your-integration-runtime-cannot-be-upgraded-and-will-eventually-stop-working-since-we-cannot-access-the-azure-blob-container-you-provided-for-custom-setup"></a>오류 메시지: "사용자 지정 설정에 대해 제공한 Azure Blob 컨테이너에 액세스할 수 없으므로 통합 런타임을 업그레이드할 수 없으며 결국 작동이 중지됩니다."

이 오류는 SSIS 통합 런타임이 사용자 지정 설정에 대해 구성된 저장소에 액세스할 수 없는 경우에 발생합니다. 제공한 SAS(공유 액세스 서명) URI가 유효하고 만료되지 않았는지 확인합니다.

### <a name="error-message-microsoft-ole-db-provider-for-analysis-services-hresult-0x80004005-description-com-error-com-error-mscorlib-exception-has-been-thrown-by-the-target-of-an-invocation"></a>오류 메시지: "분석 서비스를 위한 Microsoft OLE DB 공급자입니다. 'Hresult: 0x80004005 설명:' COM 오류: COM 오류: mscorlib; 호출 대상에서 예외를 throw했습니다."

한 가지 잠재적인 원인은 Azure 다단계 인증을 사용하도록 설정한 사용자 이름 또는 암호가 Azure 분석 서비스 인증에 대해 구성되기 때문에 가능합니다. 이 인증은 SSIS 통합 런타임에서 지원되지 않습니다. Azure 분석 서비스 인증에 서비스 주체를 사용해 보십시오.

1. 서비스 주체와 함께 자동화에 설명된 대로 [서비스 주체를 준비합니다.](https://docs.microsoft.com/azure/analysis-services/analysis-services-service-principal)
2. 연결 관리자에서 **구성 특정 사용자 이름과 암호 사용**: **AppID를** 사용자 이름으로 설정하고 **clientSecret를** 암호로 설정합니다.

### <a name="error-message-adonet-source-has-failed-to-acquire-the-connection-guid-with-the-following-error-message-login-failed-for-user-nt-authorityanonymous-logon-when-using-a-managed-identity"></a>오류 메시지: "ADONET 소스가 다음 오류 메시지와 함께 {GUID}연결을 획득하지 못했습니다: 관리되는 ID를 사용할 때 사용자 'NT AUTHORITY\익명 LOGON'에 대한 로그인실패"

매개 변수 *ConnectUsingManagedId가* **true인**경우 연결 관리자의 인증 방법을 **활성 디렉터리 암호 인증으로** 구성하지 않았는지 확인합니다. 대신 **SQL 인증으로** 구성할 수 있으며, *ConnectUsingManagedIdentity가* 설정된 경우 무시됩니다.

### <a name="error-message-0xc020801f-at--odata-source--cannot-acquire-a-managed-connection-from-the-run-time-connection-manager"></a>오류 메시지: "0xC020801F 에서..., OData 소스 [...]: 런타임 연결 관리자에서 관리 되는 연결을 수집할 수 없습니다."

한 가지 잠재적인 원인은 OData 원본에 필요한 SSIS 통합 런타임에서 TLS(전송 계층 보안)를 사용할 수 없기 때문에 발생할 수 있습니다. 사용자 지정 설정을 사용하여 SSIS 통합 런타임에서 TLS를 활성화할 수 있습니다. 자세한 내용은 [SSIS에서 프로젝트 온라인 Odata를 연결할 수 없고](https://docs.microsoft.com/office365/troubleshoot/cant-connect-project-online-odata-from-ssis) [Azure-SSIS 통합 런타임에 대한 설정을 사용자 지정할](how-to-configure-azure-ssis-ir-custom-setup.md)수 없습니다.

### <a name="error-message-request-staging-task-with-operation-guid--fail-since-error-failed-to-dispatch-staging-operation-with-error-message-microsoftsqlserverintegrationservicesaisagentcoreaisagentexception-failed-to-load-data-proxy"></a>오류 메시지: "작업 guid를 사용 하 고 준비 작업을 요청 ... 오류 이후 실패: 오류 메시지와 함께 준비 작업을 디스패치하는 데 실패했습니다: Microsoft.SqlServer.IntegrationServices.AisAgentCore.AisAgent예외: 데이터 프록시를 로드하지 못했습니다."

Azure-SSIS 통합 런타임이 자체 호스팅 통합 런타임으로 구성되어 있는지 확인합니다. 자세한 내용은 [ADF의 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성에서](self-hosted-integration-runtime-proxy-ssis.md)찾을 수 있습니다.

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2010-errormessage-the-self-hosted-integration-runtime--is-offline"></a>오류 메시지: "스테이징 작업 상태: 실패했습니다. 스테이징 작업 오류: ErrorCode: 2010, ErrorMessage: 자체 호스팅 통합 런타임... 오프라인 상태입니다."

자체 호스팅 통합 런타임이 설치되고 시작되었는지 확인합니다. 자세한 내용은 자체 [호스팅 통합 런타임 만들기 및 구성에서](create-self-hosted-integration-runtime.md) 찾을 수 있습니다.

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-the-requested-ole-db-provider--is-not-registered-if-the-64-bit-driver-is-not-installed-run-the-package-in-32-bit-mode"></a>오류 메시지: "스테이징 작업 오류: 오류 코드: 2906, ErrorMessage: 패키지 실행 실패., 출력: {"OperationErrorMessages": "오류: 요청된 OLE DB 공급자... 등록되지 않았습니다. 64비트 드라이버가 설치되어 있지 않으면 32비트 모드에서 패키지를 실행합니다..."

패키지의 OLE DB 커넥터에서 사용하는 해당 공급자가 자체 호스팅 통합 런타임 컴퓨터에 제대로 설치되어 있는지 확인합니다. 자세한 내용은 [ADF의 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성에서](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir) 찾을 수 있습니다.

### <a name="error-message-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-error-systemiofileloadexception-could-not-load-file-or-assembly-microsoftwindowsazurestorage-version-cultureneutral-publickeytoken31bf3856ad364e35-or-one-of-its-dependencies-the-located-assemblys-manifest-definition-does-not-match-the-assembly-reference"></a>오류 메시지: "스테이징 작업 오류: 오류 코드: 2906, ErrorMessage: 패키지 실행 실패., 출력: {"OperationErrorMessages": "오류: System.IO.FileLoadException: 파일 또는 어셈블리를 로드할 수 없습니다 ' Microsoft.WindowsAzure.Storage, 버전=..., 문화문화=중립, PublicKeyToken=31bf3856ad364e35' 또는 해당 종속성 중 하나. 위치 된 어셈블리의 매니페스트 정의 어셈블리 참조와 일치하지 않습니다.' ..."

한 가지 잠재적인 원인은 자체 호스팅 통합 런타임이 제대로 설치되거나 업그레이드되지 않았기 입니다. 최신 자체 호스팅 통합 런타임을 다운로드하고 다시 설치하는 것이 좋습니다. 자세한 내용은 자체 [호스팅 통합 런타임 만들기 및 구성에서](create-self-hosted-integration-runtime.md#installation-best-practices) 찾을 수 있습니다.

### <a name="error-message-a-connection-is-required-when-requesting-metadata-if-you-are-working-offline-uncheck-work-offline-on-the-ssis-menu-to-enable-the-connection"></a>오류 메시지: "메타데이터를 요청할 때 연결이 필요합니다. 오프라인으로 작업하는 경우 SSIS 메뉴에서 오프라인 작업 선택을 취소하여 연결을 활성화합니다."

* 잠재적인 원인 & 권장 조치:
  * 실행 로그에 "구성 요소가 ConnectByProxy 값 설정 true를 사용하여 연결 관리자를 지원하지 않습니다"라는 경고 메시지가 있는 경우 이는 아직 "ConnectByProxy"를 지원하지 않은 구성 요소에 연결 관리자가 사용된임을 의미합니다. 지원되는 구성 요소는 [ADF의 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성에서](self-hosted-integration-runtime-proxy-ssis.md#enable-ssis-packages-to-connect-by-proxy) 찾을 수 있습니다.
  * 실행 로그는 [SSMS 보고서 또는 SSIS](https://docs.microsoft.com/sql/integration-services/performance/monitor-running-packages-and-other-operations?view=sql-server-2017#reports) 패키지 실행 활동에 지정한 로그 폴더에서 찾을 수 있습니다.
  * vNet을 사용하여 온-프레미스 데이터에 액세스할 수도 있습니다. 자세한 내용은 [Azure-SSIS 통합 런타임에 가상 네트워크에 가입할 때](join-azure-ssis-integration-runtime-virtual-network.md) 찾을 수 있습니다.

### <a name="error-message-staging-task-status-failed-staging-task-error-errorcode-2906-errormessage-package-execution-failed-output-operationerrormessages-ssis-executor-exit-code--1n-loglocation-ssistelemetryexecutionlog-effectiveintegrationruntime--executionduration--durationinqueue--integrationruntimequeue--"></a>오류 메시지: "스테이징 작업 상태: 실패했습니다. 스테이징 작업 오류: 오류 코드: 2906, ErrorMessage: 패키지 실행 실패., 출력: {"OperationErrorMessages": "SSIS 실행기 종료 코드: -1.\n", "LogLocation": "... \\SSIS Telemetry\\\\실행 로그 ...", "효과적인통합런타임": "...", "실행 기간": "기간 대기열": {"integrationRuntimeQueue": ...}"

Visual C++ 런타임이 자체 호스팅 통합 런타임 컴퓨터에 설치되어 있는지 확인합니다. 자세한 내용은 [ADF의 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성에서](self-hosted-integration-runtime-proxy-ssis.md#prepare-the-self-hosted-ir) 찾을 수 있습니다.

### <a name="multiple-package-executions-are-triggered-unexpectedly"></a>여러 패키지 실행이 예기치 않게 트리거됩니다.

* 잠재적인 원인 & 권장 조치:
  * ADF 저장 프로시저 활동 또는 조회 활동은 SSIS 패키지 실행을 트리거하는 데 사용됩니다. t-sql 명령은 일시적인 문제를 일으키고 여러 패키지 실행을 유발하는 재실행을 트리거할 수 있습니다.
  * 대신 ExecuteSISPackage 작업을 사용하여 사용자가 활동에서 다시 시도 수를 설정하지 않는 한 패키지 실행이 다시 실행되지 않도록 합니다. 세부 사항은 다음에서 찾을 수 있습니다.[https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)
  * 실행이 이미 트리거되었는지 확인하여 다시 실행할 수 있도록 t-sql 명령을 구체화합니다.

### <a name="package-execution-takes-too-long"></a>패키지 실행시간이 너무 오래 걸립니다.

잠재적인 원인과 권장 조치는 다음과 같습니다.

* SSIS 통합 런타임에 너무 많은 패키지 실행이 예약되었습니다. 이러한 모든 실행은 차례를 위해 대기열에서 대기합니다.
  * 이 수식을 사용하여 최대값을 결정합니다.

    IR당 최대 병렬 실행 횟수 = 노드 수 * 노드당 최대 병렬 실행
  * 노드 수 및 노드당 최대 병렬 실행을 설정하는 방법을 알아보려면 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기를](create-azure-ssis-integration-runtime.md)참조하십시오.
* SSIS 통합 런타임이 중지됨 또는 비정상 상태입니다. SSIS 통합 런타임 상태 및 오류를 확인하는 방법에 대한 자세한 내용은 [Azure-SSIS 통합 런타임을](monitor-integration-runtime.md#azure-ssis-integration-runtime)참조하십시오.

일반 **탭:** ![일반 탭의](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)속성 설정에서 시간 시간을 설정하는 것이 좋습니다.

### <a name="poor-performance-in-package-execution"></a>패키지 실행시 성능 저하

다음과 같이 작업해 보세요.

* SSIS 통합 런타임이 데이터 원본 및 대상과 동일한 지역에 있는지 확인합니다.

* 패키지 실행의 로깅 수준을 **성능으로** 설정하여 실행중의 각 구성 요소에 대한 기간 정보를 수집합니다. 자세한 내용은 [통합 서비스(SSIS) 로깅](https://docs.microsoft.com/sql/integration-services/performance/integration-services-ssis-logging)을 참조하십시오.

* Azure 포털에서 IR 노드 성능을 확인합니다.
  * SSIS 통합 런타임을 모니터링하는 방법에 대한 자세한 내용은 [Azure-SSIS 통합 런타임을](monitor-integration-runtime.md#azure-ssis-integration-runtime)참조하십시오.
  * Azure 포털에서 데이터 팩터리의 메트릭을 확인하여 SSIS 통합 런타임에 대한 CPU/메모리 기록을 찾을 수 있습니다.
    ![SSIS 통합 런타임의 메트릭 모니터링](media/ssis-integration-runtime-ssis-activity-faq/monitor-metrics-ssis-integration-runtime.png)
