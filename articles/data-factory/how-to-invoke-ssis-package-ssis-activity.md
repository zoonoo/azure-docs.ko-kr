---
title: SSIS 패키지 실행 활동으로 SSIS 패키지 실행
description: 이 문서에서는 SSIS 패키지 실행 작업을 사용하여 Azure 데이터 팩터리 파이프라인에서 SQL 서버 통합 서비스(SSIS) 패키지를 실행하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 11/14/2019
ms.openlocfilehash: f505313b37d5289a5af10c40ede7f376eab4841d
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605952"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory에서 SSIS 패키지 실행 작업을 사용하여 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 SSIS 패키지 실행 작업을 사용하여 Azure 데이터 팩터리 파이프라인에서 SQL 서버 통합 서비스(SSIS) 패키지를 실행하는 방법을 설명합니다. 

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[자습서: Azure-SSIS IR 프로비저닝의](tutorial-create-azure-ssis-runtime-portal.md)단계별 지침에 따라 아직 없는 경우 Azure-SSIS 통합 런타임(IR)을 만듭니다.

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal에서 패키지 실행
이 섹션에서는 UI(데이터 팩터리 사용자 인터페이스) 또는 앱을 사용하여 SSIS 패키지를 실행하는 SSIS 패키지 실행 작업을 사용하여 데이터 팩터리 파이프라인을 만듭니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기
이 단계에서는 데이터 팩터리 UI 또는 앱을 사용하여 파이프라인을 만듭니다. 파이프라인에 SSIS 패키지 실행 작업을 추가하고 SSIS 패키지를 실행하도록 구성합니다. 

1. Azure 포털의 데이터 팩터리 개요 또는 홈 페이지에서 **모니터 작성자 &** 선택하여 별도의 탭에서 데이터 팩터리 UI 또는 앱을 시작합니다. 

   ![Data Factory 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **시작** 페이지에서 **파이프라인 만들기**를 선택합니다. 

   ![시작 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

1. **활동** 도구 상자에서 **일반**을 확장합니다. 그런 다음 **SSIS 패키지 실행** 활동을 파이프라인 디자이너 표면으로 끕을 끕습니다. 

   ![SSIS 패키지 실행 작업을 디자이너 화면으로 끌기](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

1. SSIS 패키지 실행 활동에 대한 **일반** 탭에서 활동에 대한 이름과 설명을 제공합니다. 선택적 **시간 설정** 및 **재시도** 값을 설정합니다.

   ![일반 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

1. SSIS 패키지 실행 활동에 대한 **설정** 탭에서 패키지를 실행할 Azure-SSIS IR을 선택합니다. 패키지에서 Windows 인증을 사용하여 데이터 저장소(예: SQL 서버 또는 파일 공유 온-프레미스 또는 Azure Files)에 액세스하는 경우 **Windows 인증** 확인란을 선택합니다. **도메인,** **사용자 이름**및 **암호** 상자에 패키지 실행 자격 증명에 대한 값을 입력합니다. 

    또는 Azure 키 자격 증명 모음에 저장된 비밀을 해당 값으로 사용할 수 있습니다. 이렇게 하려면 관련 자격 증명 옆에 있는 **AZURE KEY VAULT** 확인란을 선택합니다. 기존 키 자격 증명 모음 연결 서비스를 선택하거나 편집하거나 새 서비스를 만듭니다. 그런 다음 자격 증명 값에 대한 비밀 이름 또는 버전을 선택합니다.

    키 자격 증명 모음 링크 서비스를 만들거나 편집할 때 기존 키 자격 증명 모음을 선택하거나 편집하거나 새 키 자격 증명 모음을 만들 수 있습니다. 아직 키 자격 증명 모음에 데이터 팩터리 관리 ID 액세스를 부여해야 합니다. 다음 형식으로 직접 비밀을 입력할 수도 `<Key vault linked service name>/<secret name>/<secret version>`있습니다. 패키지를 실행하려면 32비트 런타임이 필요한 경우 **32비트 런타임** 확인란을 선택합니다.

   **패키지 위치의**경우 **SSISDB,** **파일 시스템(패키지)**, **파일 시스템(프로젝트)** 또는 **포함된 패키지를**선택합니다. Azure-SSIS IR이 Azure SQL Database 서버 또는 관리되는 인스턴스에서 호스팅하는 SSIS 카탈로그(SSISDB)로 프로비전된 경우 자동으로 선택되는 패키지 위치로 **SSISDB를** 선택하는 경우 SSISDB에 배포된 패키지를 지정합니다. 

    Azure-SSIS IR이 실행 중이고 **수동 항목** 확인란이 지워진 경우 SSISDB에서 기존 폴더, 프로젝트, 패키지 또는 환경을 찾아보고 선택합니다. 새로 추가된 폴더, 프로젝트, 패키지 또는 환경을 SSISDB에서 가져오려면 **새로 고침을** 선택하여 검색 및 선택할 수 있도록 합니다. 패키지 실행에 대한 환경을 찾아보거나 선택하려면 프로젝트를 미리 구성하여 SSISDB 아래의 동일한 폴더에서 참조로 해당 환경을 추가해야 합니다. 자세한 내용은 [SSIS 환경 만들기 및 맵을](https://docs.microsoft.com/sql/integration-services/create-and-map-a-server-environment?view=sql-server-2014)참조하십시오.

   **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 대신 **사용자 지정** 로깅 이름을 입력하려면 사용자 지정 확인란을 선택합니다. 

   ![설정 탭의 속성 설정 - 자동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Azure-SSIS IR이 실행되고 있지 않거나 **수동 항목** 확인란을 선택한 경우 SSISDB의 패키지 및 환경 `<folder name>/<project name>/<package name>.dtsx` 경로를 `<folder name>/<environment name>`다음 형식으로 직접 입력합니다.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   Azure-SSIS IR이 SSISDB 없이 프로비전된 경우 자동으로 선택되는 패키지 위치로 **파일 시스템(패키지)을** 선택하는 경우 **패키지 경로** 상자에서 패키지 파일()에`.dtsx`범용 명명 규칙(UNC) 경로를 제공하여 실행할 패키지를 지정합니다. 예를 들어 Azure Files에 패키지를 저장하는 경우 해당 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`패키지 경로는 입니다. 
   
   패키지를 별도의 파일로 구성하는 경우 구성`.dtsConfig` **경로** 상자에 구성 파일()에 UNC 경로를 제공해야 합니다. 예를 들어 Azure Files에 구성을 저장하는 경우 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`해당 구성 경로는 입니다.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   **파일 시스템(Project)을** 패키지 위치로 선택한 경우 프로젝트 경로 상자의 프로젝트 파일()에`.ispac`UNC **Project path** 경로를 제공하고 패키지`.dtsx` **이름** 상자의 프로젝트에서 패키지 파일()에 UNC 경로를 제공하여 실행할 패키지를 지정합니다. 예를 들어 Azure Files에 프로젝트를 저장하는 경우 해당 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`프로젝트 경로는 입니다.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   다음으로 프로젝트, 패키지 또는 구성 파일에 액세스할 자격 증명을 지정합니다. 이전에 패키지 실행 자격 증명에 대한 값을 입력한 경우(이전 참조) **패키지 실행 자격 증명과 동일함을** 선택하여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인,** **사용자 이름**및 **암호** 상자에 패키지 액세스 자격 증명에 대한 값을 입력합니다. 예를 들어 Azure Files에 프로젝트, 패키지 또는 구성을 저장하는 `Azure`경우 도메인은 `<storage account name>`'사용자 이름' 및 암호는 `<storage account key>`입니다. 

   또는 키 자격 증명 모음에 저장된 비밀을 해당 값으로 사용할 수 있습니다(이전 참조). 이러한 자격 증명은 패키지 실행 작업에서 패키지 및 자식 패키지에 액세스하는 데 사용되며, 모두 자체 경로 또는 동일한 프로젝트뿐만 아니라 패키지에 지정된 구성을 포함합니다. 

   **임베디드 패키지를** 패키지 위치로 선택한 경우 패키지를 끌어서 놓아 실행하거나 파일 폴더에서 제공된 상자에 **업로드합니다.** 패키지는 자동으로 압축되고 활동 페이로드에 포함됩니다. 일단 포함되면 나중에 편집을 위해 패키지를 **다운로드할** 수 있습니다. 또한 여러 활동에 사용할 수 있는 파이프라인 매개 변수에 할당하여 임베디드 패키지를 **매개 변수화하여** 파이프라인 페이로드 크기를 최적화할 수도 있습니다. 포함된 패키지가 모두 암호화되지 않고 패키지 작업 실행 이 사용이 감지되면 **패키지 작업 실행** 확인란이 자동으로 선택되고 파일 시스템 참조가 있는 관련 자식 패키지가 자동으로 추가되어 패키지가 포함됩니다. 패키지 작업 실행의 사용을 감지할 수 없는 경우 패키지 작업 **실행** 확인란을 수동으로 선택하고 파일 시스템 참조가 있는 관련 자식 패키지를 하나씩 추가하여 패키지 패키지를 포함해야 합니다. 자식 패키지가 SQL Server 참조를 사용하는 경우 Azure-SSIS IR에서 SQL Server에 액세스할 수 있는지 확인하십시오.  자식 패키지에 대한 프로젝트 참조의 사용은 현재 지원되지 않습니다.
   
   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings5.png)
   
   SQL Server 데이터 도구를 통해 패키지를 만들 때 **암호화AllWithPassword** 또는 **암호화에** 민감한 암호 보호 수준을 사용한 경우 **암호화 암호** 상자에 암호 값을 입력합니다. 또는 키 자격 증명 모음에 저장된 비밀을 해당 값으로 사용할 수 있습니다(이전 참조). **암호화감 민감대비 사용자 키** 보호 수준을 사용한 경우 구성 파일 또는 **SSIS 매개 변수,** **연결 관리자**또는 **속성 재정의** 탭에 중요한 값을 다시 입력합니다(이후 참조). 

   **암호화AllWithUserKey** 보호 수준을 사용하는 경우 지원되지 않습니다. SQL Server 데이터 도구 또는 `dtutil` 명령줄 유틸리티를 통해 다른 보호 수준을 사용하도록 패키지를 다시 구성해야 합니다. 
   
   **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 대신 **사용자 지정** 로깅 이름을 입력하려면 사용자 지정 확인란을 선택합니다. 패키지에 지정할 수 있는 표준 로그 공급자를 사용하는 것 이상으로 패키지 실행을 기록하려면 **로깅 경로** 상자에 UNC 경로를 제공하여 로그 폴더를 지정합니다. 예를 들어 로그를 Azure 파일에 저장하는 경우 로깅 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`경로는 입니다. 하위 폴더는 각 개별 패키지 실행에 대해 이 경로에 만들어지며 5분마다 로그 파일이 생성되는 실행 SSIS 패키지 활동 실행 ID의 이름을 따서 명명됩니다. 
   
   마지막으로 로그 폴더에 액세스할 자격 증명을 지정합니다. 이전에 패키지 액세스 자격 증명에 대한 값을 입력한 경우(이전 참조) **패키지 액세스 자격 증명과 동일함을** 선택하여 다시 사용할 수 있습니다. 그렇지 않으면 **도메인,** **사용자 이름**및 **암호** 상자에 로깅 액세스 자격 증명에 대한 값을 입력합니다. 예를 들어 Azure Files에 로그를 저장하는 경우 `Azure`도메인은 '사용자 `<storage account name>`이름은 은 `<storage account key>`이며 암호는 입니다. 

    또는 키 자격 증명 모음에 저장된 비밀을 해당 값으로 사용할 수 있습니다(이전 참조). 이러한 자격 증명은 로그를 저장하는 데 사용됩니다. 
   
   앞에서 언급한 모든 UNC 경로의 경우 정규화된 파일 이름은 260자 미만이어야 합니다. 디렉터리 이름은 248자 미만이어야 합니다.

1. SSIS 패키지 실행 활동에 대한 **SSIS 매개 변수** 탭에서 Azure-SSIS IR이 실행 중인 경우 **SSISDB가** 패키지 위치로 선택되고 **설정** 탭의 **수동 항목** 확인란이 지워지면 선택한 프로젝트 또는 SSISDB의 패키지에 있는 기존 SSIS 매개변수가 표시되어 값을 할당할 수 있습니다. 그렇지 않으면 하나씩 입력하여 수동으로 값을 할당할 수 있습니다. 패키지 실행이 성공하려면 패키지 실행이 존재하고 올바르게 입력되었는지 확인합니다. 
   
   SQL Server 데이터 도구 및 **파일 시스템(패키지)** 또는 **파일 시스템(프로젝트)을** 통해 패키지를 만들 때 **암호화감 있는사용자 키** 보호 수준을 패키지 위치로 선택한 경우 구성 파일이나 이 탭에서 값을 할당하기 위해 중요한 매개 변수를 다시 입력해야 합니다. 
   
   매개 변수에 값을 할당할 때 표현식, 함수, 데이터 팩터리 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용하여 동적 콘텐츠를 추가할 수 있습니다. 또는 키 자격 증명 모음에 저장된 비밀을 해당 값으로 사용할 수 있습니다(이전 참조).

   ![SSIS 매개 변수 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

1. SSIS 패키지 실행 활동에 대한 **연결 관리자** 탭에서 Azure-SSIS IR이 실행 중인 경우 **SSISDB가** 패키지 위치로 선택되고 **설정** 탭의 **수동 항목** 확인란이 지워지면 선택한 프로젝트 또는 패키지의 기존 연결 관리자가 해당 속성에 값을 할당하도록 표시됩니다. 그렇지 않으면 하나씩 입력하여 해당 속성에 값을 수동으로 할당할 수 있습니다. 패키지 실행이 성공하려면 패키지 실행이 존재하고 올바르게 입력되었는지 확인합니다. 
   
   SQL Server 데이터 도구 및 **파일 시스템(패키지)** 또는 **파일 시스템(프로젝트)을** 통해 패키지를 만들 때 **암호화감 있는사용자 키** 보호 수준을 패키지 위치로 선택한 경우 구성 파일이나 이 탭에서 값을 할당하기 위해 중요한 연결 관리자 속성을 다시 입력해야 합니다. 
   
   연결 관리자 속성에 값을 할당할 때 표현식, 함수, 데이터 팩터리 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용하여 동적 콘텐츠를 추가할 수 있습니다. 또는 키 자격 증명 모음에 저장된 비밀을 해당 값으로 사용할 수 있습니다(이전 참조).

   ![연결 관리자 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

1. SSIS 패키지 실행 활동에 대한 **속성 재정의** 탭에서 선택한 패키지에 기존 속성의 경로를 하나씩 입력하여 수동으로 값을 할당합니다. 패키지 실행이 성공하려면 패키지 실행이 존재하고 올바르게 입력되었는지 확인합니다. 예를 들어 사용자 변수의 값을 재정의하려면 다음 형식으로 경로를 `\Package.Variables[User::<variable name>].Value`입력합니다. 
   
   SQL Server 데이터 도구 및 **파일 시스템(패키지)** 또는 **파일 시스템(프로젝트)을** 통해 패키지를 만들 때 **암호화감 있는사용자 키** 보호 수준을 패키지 위치로 선택한 경우 구성 파일이나 이 탭에서 값을 할당하기 위해 중요한 속성을 다시 입력해야 합니다. 
   
   속성에 값을 할당할 때 표현식, 함수, 데이터 팩터리 시스템 변수 및 Data Factory 파이프라인 매개 변수 또는 변수를 사용하여 동적 콘텐츠를 추가할 수 있습니다.

   ![속성 재정의 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   구성 파일 및 **SSIS 매개 변수** 탭에 할당된 값은 **연결 관리자** 또는 속성 **재정의** 탭을 사용하여 재정의할 수 있습니다. **속성 재정의** 탭을 사용하여 **연결 관리자** 탭에 할당된 값을 재정의할 수도 있습니다.

1. 파이프라인 구성의 유효성을 검사하려면 도구 모음에서 **유효성 검사를** 선택합니다. **파이프라인 유효성 검사 보고서를** **>>** 닫려면 을 선택합니다.

1. 파이프라인을 데이터 팩터리에 게시하려면 **모두 게시를**선택합니다. 

### <a name="run-the-pipeline"></a>파이프라인 실행
이 단계에서는 파이프라인 실행을 트리거합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거를** 선택하고 **지금 트리거를 선택합니다.** 

   ![지금 트리거](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. 파이프라인 실행 및 해당 상태와 **실행 시작** 시간과 같은 다른 정보가 표시됩니다. 보기를 새로 고치려면 **새로 고침**을 선택합니다.

   ![파이프라인 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **작업** 열에서 **활동 실행 보기** 링크를 선택합니다. 파이프라인에 하나의 활동만 있기 때문에 하나의 활동 만 실행됩니다. SSIS 패키지 실행 활동입니다.

   ![작업 실행](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. SQL 서버의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인합니다. 

   ```sql
   select * from catalog.executions
   ```

   ![패키지 실행 확인](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 또한 파이프라인 활동 실행의 출력에서 SSISDB 실행 ID를 얻고 ID를 사용하여 SQL Server 관리 스튜디오에서 보다 포괄적인 실행 로그 및 오류 메시지를 확인할 수 있습니다.

   ![실행 ID를 가져옵니다.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약

파이프라인이 매시간 또는 매일과 같은 일정에 따라 실행되도록 파이프라인에 대해 예약된 트리거를 만들 수도 있습니다. 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="run-a-package-with-powershell"></a>PowerShell을 사용하여 패키지 실행
이 섹션에서는 Azure PowerShell을 사용하여 SSIS 패키지를 실행하는 SSIS 패키지 실행 작업을 사용하여 데이터 팩터리 파이프라인을 만듭니다. 

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-az-ps)의 단계별 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

### <a name="create-a-data-factory-with-azure-ssis-ir"></a>Azure-SSIS IR을 사용하여 데이터 팩터리 만들기
Azure-SSIS IR프로비전된 기존 데이터 팩터리를 사용하거나 Azure-SSIS IR을 사용하여 새 데이터 팩터리를 만들 수 있습니다. 자습서의 단계별 지침: [PowerShell을 통해 Azure에 SSIS 패키지를 배포합니다.](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기 
이 단계에서는 SSIS 패키지 실행 작업으로 파이프라인을 만듭니다. 이 작업은 SSIS 패키지를 실행합니다. 

1. 다음 예제와 유사한 내용이 있는 *C:\ADF\RunSIs패키지* 폴더에서 *RunSISPackagePipeline.json이라는* JSON 파일을 만듭니다.

   > [!IMPORTANT]
   > 파일을 저장하기 전에 개체 이름, 설명 및 경로, 속성 또는 매개 변수 값, 암호 및 기타 변수 값을 바꿉니다. 
    
   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "MySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "MyAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyExecutionDomain",
                       "username": "MyExecutionUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "MyExecutionPassword"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "MyFolder/MyProject/MyPackage.dtsx",
                       "type": "SSISDB"
                   },
                   "environmentPath": "MyFolder/MyEnvironment",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyProjectParameter",
                               "type": "Expression"
                           }
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       },
                       "package_param_2": {
                           "value": {
                               "type": "AzureKeyVaultSecret",
                               "store": {
                                   "referenceName": "myAKV",
                                   "type": "LinkedServiceReference"
                               },
                               "secretName": "MyPackageParameter"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "username": {
                               "value": "MyConnectionUsername"
                           },
                           "password": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "MyConnectionPassword"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "username": {
                               "value": {
                                   "value": "@pipeline().parameters.MyConnectionUsername",
                                   "type": "Expression"
                               }
                           },
                           "password": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyConnectionPassword",
                                   "secretVersion": "MyConnectionPasswordVersion"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

   파일 시스템, 파일 공유 또는 Azure 파일에 저장된 패키지를 실행하려면 다음과 같이 패키지 및 로그 위치 속성에 대한 값을 입력합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   },
                   "logLocation": {
                       "logPath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyLogFolder",
                       "type": "File",
                       "typeProperties": {
                           "accessCredential": {
                               "domain": "Azure",
                               "username": "MyStorageAccount",
                               "password": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                           },
                                   "secretName": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   파일 시스템, 파일 공유 또는 Azure 파일에 저장된 프로젝트 내에서 패키지를 실행하려면 다음과 같이 패키지 위치 속성의 값을 입력합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "packagePath": "//MyStorageAccount.file.core.windows.net/MyFileShare/MyProject.ispac:MyPackage.dtsx",
                       "type": "File",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "accessCredential": {
                               "domain": "Azure",
                               "userName": "MyStorageAccount",
                               "password": {
                                   "type": "SecureString",
                                   "value": "MyAccountKey"
                               }
                           }
                       }
                   }
               }
           }
       }
   }
   ```

   포함된 패키지를 실행하려면 다음과 같이 패키지 위치 속성의 값을 입력합니다.

   ```json
   {
       {
           {
               {
                   "packageLocation": {
                       "type": "InlinePackage",
                       "typeProperties": {
                           "packagePassword": {
                               "type": "SecureString",
                               "value": "MyEncryptionPassword"
                           },
                           "packageName": "MyPackage.dtsx",
                           "packageContent":"My compressed/uncompressed package content",
                           "packageLastModifiedDate": "YYYY-MM-DDTHH:MM:SSZ UTC-/+HH:MM"
                       }
                   }
               }
           }
       }
   }
   ```

2. Azure PowerShell에서 *C:\ADF\RunSIs패키지* 폴더로 전환합니다.

3. 파이프라인 **RunSIS패키지파이프라인을**만들려면 **Set-AzDataFactoryV2파이프라인** cmdlet을 실행합니다.

   ```powershell
   $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   샘플 출력은 다음과 같습니다.

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>파이프라인 실행
**Invoke-AzDataFactoryV2파이프라인** cmdlet을 사용하여 파이프라인을 실행합니다. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사하거나 붙여넣은 다음 스크립트를 복사하고 Enter를 선택합니다. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Azure 포털을 사용하여 파이프라인을 모니터링할 수도 있습니다. 단계별 지침은 [파이프라인 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약
이전 단계에서는 필요에 따라 파이프라인을 실행했습니다. 매시간 또는 매일과 같은 일정에 따라 파이프라인을 실행하는 일정 트리거를 만들 수도 있습니다.

1. 다음 내용이 있는 *C:\ADF\RunSIsPackage* 폴더에서 *MyTrigger.json이라는* JSON 파일을 만듭니다. 
        
   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
    
1. Azure PowerShell에서 *C:\ADF\RunSIs패키지* 폴더로 전환합니다.
1. 트리거를 만드는 **Set-AzDataFactoryV2트리거** cmdlet을 실행합니다. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
1. 기본적으로 트리거는 중지된 상태입니다. **시작-AzDataFactoryV2트리거** cmdlet을 실행 하여 트리거를 시작합니다. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
1. **Get-AzDataFactoryV2트리거** cmdlet을 실행하여 트리거가 시작되어 있는지 확인합니다. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
1. 한 시간 후에 다음 명령을 실행합니다. 예를 들어 현재 시간이 오후 3:25(UTC)인 경우 오후 4시(UTC)에 명령을 실행합니다. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   SQL 서버의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인합니다. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>다음 단계
다음 블로그 게시물을 참조하십시오.
- [Azure 데이터 팩터리 파이프라인에서 SSIS 활동으로 ETL/ELT 워크플로를 현대화하고 확장](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Modernize-and-Extend-Your-ETL-ELT-Workflows-with-SSIS-Activities/ba-p/388370)
