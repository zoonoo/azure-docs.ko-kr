---
title: Azure SQL 관리 인스턴스 에이전트에 의한 SSIS 패키지 실행
description: Azure SQL 관리 인스턴스 에이전트에서 SSIS 패키지를 실행하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: lle
author: lle
ms.date: 04/14/2020
ms.openlocfilehash: b3b7a25149a9d075c81b30307ade2beb71907637
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81394723"
---
# <a name="execute-ssis-packages-by-azure-sql-managed-instance-agent"></a>Azure SQL 관리 인스턴스 에이전트에 의한 SSIS 패키지 실행
이 문서에서는 Azure SQL 관리 인스턴스 에이전트를 사용하여 Sql Server 통합 서비스(SSIS) 패키지를 실행하는 방법을 설명합니다. 이 기능은 온프레미 환경에서 SQL Server 에이전트가 SSIS 패키지를 예약할 때와 유사한 동작을 제공합니다.

이 기능을 사용하면 Azure SQL 관리 인스턴스또는 Azure 파일과 같은 파일 시스템의 SSISDB에 저장된 SSIS 패키지를 실행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
이 기능을 사용하려면 버전 18.5 이상인 최신 버전의 SSMS를 다운로드하여 설치합니다. [이 웹 사이트](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)에서 다운로드합니다.

또한 Azure SQL 관리 인스턴스를 끝점 서버로 사용하는 Azure 데이터 팩터리에서 Azure-SSIS 통합 런타임을 프로비전해야 합니다. 당신은 이미 프로비전하지 않은 경우, [튜토리얼의](tutorial-create-azure-ssis-runtime-portal.md)지침에 따라 프로비전 . 

## <a name="run-ssis-packages-in-ssisdb-by-azure-sql-managed-instance-agent"></a>Azure SQL 관리 인스턴스 에이전트에 의해 SSISDB에서 SSIS 패키지 실행
이 단계에서는 Azure SQL 관리 인스턴스 에이전트를 사용하여 Azure SQL 관리 인스턴스의 SSISDB에 저장된 SSIS 패키지를 호출합니다.
1. 최신 버전의 SSMS에서는 Azure SQL 관리 인스턴스에 연결합니다.
2. 새 에이전트 작업 및 새 작업 단계를 만듭니다.

![새 에이전트 작업](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. 새 **작업 단계** 페이지에서 **SQL 서버 통합 서비스 패키지** 유형을 선택합니다.

![새로운 SSIS 작업 단계](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. **패키지** 탭에서 **SSIS 카탈로그를** 패키지 소스 유형으로 선택합니다.
5. SSISDB가 동일한 Azure SQL 관리 인스턴스에 있으므로 인증을 지정할 필요가 없습니다.
6. SSISDB에서 SSIS 패키지를 지정합니다.

![패키지 소스 유형 - SSIS 카탈로그](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb.png)

7. **구성** 탭에서 **매개 변수** 값을 지정하고, **연결 관리자에서**값을 재정의하고, **속성을** 재정의하고, **로깅 수준을**선택할 수 있습니다.

![패키지 소스 유형 - SSIS 카탈로그 구성](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-ssisdb-configuration.png)

8. 위의 모든 구성을 완료한 후 **확인을** 클릭하여 에이전트 작업 구성을 저장합니다.
9. 에이전트 작업을 시작하여 SSIS 패키지를 실행합니다.


## <a name="run-ssis-packages-in-file-system-by-azure-sql-managed-instance-agent"></a>Azure SQL 관리 인스턴스 에이전트에 의해 파일 시스템에서 SSIS 패키지 실행
이 단계에서는 Azure SQL 관리 인스턴스 에이전트를 사용하여 파일 시스템에 저장된 SSIS 패키지를 호출하여 실행합니다.
1. 최신 버전의 SSMS에서는 Azure SQL 관리 인스턴스에 연결합니다.
2. 새 에이전트 작업 및 새 작업 단계를 만듭니다.

   ![새 에이전트 작업](./media/how-to-invoke-ssis-package-managed-instance-agent/new-agent-job.png)

3. 새 **작업 단계** 페이지에서 **SQL 서버 통합 서비스 패키지** 유형을 선택합니다.

   ![새로운 SSIS 작업 단계](./media/how-to-invoke-ssis-package-managed-instance-agent/new-ssis-job-step.png)

4. **패키지** 탭에서 **파일 시스템을** 패키지 소스 유형으로 선택합니다.

   ![패키지 소스 유형 - 파일 시스템](./media/how-to-invoke-ssis-package-managed-instance-agent/package-source-file-system.png)

   1. 패키지가 Azure File에 업로드된 경우 **Azure 파일 공유를** 파일 소스 유형으로 선택합니다.
      - 패키지 경로는 ** \\ <storage account name>\<.file.core.windows.net 파일 \<공유 이름>패키지 이름>.dtsx입니다.**
      - Azure 파일에 액세스하려면 패키지 파일 **액세스 자격 증명에서** Azure 파일 계정 이름 및 계정 키를 입력합니다. 도메인이 **Azure로**설정됩니다.
   2. 패키지가 네트워크 공유에 업로드된 경우 **네트워크 공유를** 파일 소스 유형으로 선택합니다.
      - 패키지 경로는 dtsx 확장이 있는 패키지 파일의 **UNC 경로입니다.**
      - 네트워크 공유 패키지 파일에 액세스하려면 해당 **도메인,** **사용자 이름**및 **암호를** 입력합니다.
   3. 패키지 파일이 암호로 암호화된 경우 **암호 암호화를** 선택하고 암호를 입력합니다.

 5. **구성** 탭에서 SSIS 패키지를 실행하기 위해 구성 파일이 필요한 경우 구성 **파일 경로를** 입력합니다.
 6. 실행 **옵션** 탭에서 **Windows 인증을** 사용할지 또는 **32비트 런타임을** 사용하여 SSIS 패키지를 실행할지 선택할 수 있습니다.
 7. **로깅** 탭에서 **로깅 경로** 및 해당 로깅 액세스 자격 증명을 선택하여 로그 파일을 저장할 수 있습니다. 기본적으로 로깅 경로는 패키지 폴더 경로와 동일하며 로깅 액세스 자격 증명은 패키지 액세스 자격 증명과 동일합니다.
 8. 값 **집합** 탭에서 **속성 경로** 및 **값을** 입력하여 패키지 속성을 재정의할 수 있습니다.
 예를 들어 사용자 변수의 값을 재정의하려면 **\Package.Variables[User::<variable name>]]로 경로를 입력합니다. 값**.
 9. 위의 모든 구성을 완료한 후 **확인을** 클릭하여 에이전트 작업 구성을 저장합니다.
 10. 에이전트 작업을 시작하여 SSIS 패키지를 실행합니다.


 ## <a name="cancel-ssis-package-execution"></a>SSIS 패키지 실행 취소
 Azure SQL Managed 에이전트 작업에서 패키지 실행을 취소하려면 에이전트 작업을 직접 중지하는 대신 아래 단계를 수행해야 합니다.
 1. **msdb.dbo.sysjobs에서**SQL 에이전트 **작업ID를** 찾습니다.
 2. 아래 쿼리에 의해 작업 ID에 따라 해당 SSIS **executionId를** 찾습니다.
    ```sql
    select * from ssisdb.internal.execution_parameter_values_noncatalog where  parameter_value = 'SQL_Agent_Job_{jobId}' order by execution_id desc
    ```
 3. SSIS 카탈로그에서 **활성 작업을** 선택합니다.

    ![패키지 소스 유형 - 파일 시스템](./media/how-to-invoke-ssis-package-managed-instance-agent/catalog-active-operations.png)

 4. **executionId에**따라 해당 작업을 중지합니다.

## <a name="next-steps"></a>다음 단계
 Azure 데이터 팩터리를 사용하여 SSIS 패키지를 예약할 수도 있습니다. 단계별 지침은 Azure 데이터 [팩터리 이벤트 트리거를](how-to-create-event-trigger.md)참조하십시오. 