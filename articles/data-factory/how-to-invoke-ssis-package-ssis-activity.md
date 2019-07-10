---
title: SSIS 패키지 실행 작업으로 SSIS 패키지 실행 - Azure | Microsoft Docs
description: 이 문서에서는 Azure Data Factory 파이프라인에서 SSIS 패키지 실행 작업을 사용하여 SSIS(SQL Server Integration Services) 패키지를 실행하는 방법을 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/01/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: f33259fff17633cc4864a342609f747ebb9902ba
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67484878"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Azure Data Factory에서 SSIS 패키지 실행 작업을 사용하여 SSIS 패키지 실행
이 문서에서는 Azure 데이터 팩터리 (ADF) 파이프라인에서 SSIS 패키지 실행 작업을 사용 하 여 SQL Server Integration Services (SSIS) 패키지를 실행 하는 방법을 설명 합니다. 

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR(Integration Runtime)이 없는 경우 [자습서: Azure SSIS IR을 프로 비전](tutorial-create-azure-ssis-runtime-portal.md)합니다.

## <a name="run-a-package-in-the-azure-portal"></a>Azure Portal에서 패키지 실행
이 섹션에서는 ADF UI(사용자 인터페이스)/앱을 사용하여 SSIS 패키지를 실행하는 SSIS 패키지 실행 작업이 있는 ADF 파이프라인을 만듭니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기
이 단계에서는 ADF UI/앱을 사용하여 파이프라인을 만듭니다. 파이프라인에 SSIS 패키지 실행 작업을 추가하고 SSIS 패키지를 실행하도록 구성합니다. 

1. Azure Portal의 ADF 개요/홈페이지에서 **작성자 및 모니터** 타일을 클릭하여 별도의 탭에서 ADF UI/앱을 시작합니다. 

   ![데이터 팩터리 홈페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   **시작** 페이지에서 **파이프라인 만들기**를 클릭합니다. 

   ![시작 페이지](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. **작업** 도구 상자에서 **일반**을 펼치고, **SSIS 패키지 실행** 작업을 파이프라인 디자이너 화면으로 끌어서 놓습니다. 

   ![SSIS 패키지 실행 작업을 디자이너 화면으로 끌기](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. SSIS 패키지 실행 작업의 **일반** 탭에서 작업 이름과 설명을 입력합니다. 선택적 시간 제한 및 다시 시도 값을 설정합니다.

   ![일반 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. 에 **설정을** SSIS 패키지 실행 작업에 대 한 탭에서 패키지를 실행 하려는 Azure SSIS IR을 선택 합니다. 패키지에서 데이터 저장소에 액세스 하려면 Windows 인증을 사용 하는 경우 온-프레미스 SQL 서버/파일을 공유 하는 예를 들어 Azure Files 등을 확인 합니다 **Windows 인증** 확인란 패키지 실행 자격 증명에 대 한 값을 입력 합니다. (**도메인**/**Username**/**암호**). 또는에서 Key Vault (AKV (Azure) 해당 값으로 저장 된 비밀을 사용할 수 있습니다. 이렇게 하려면 클릭 합니다 **AZURE KEY VAULT** 관련 자격 증명, 옆의 확인란을 선택/편집 기존 AKV 연결 된 서비스 또는 새 대시보드를 만든 및 자격 증명 값에 대 한 비밀 이름/버전을 선택 합니다.  때 AKV 연결 된 서비스를 만들기/편집 선택/편집 하면 기존 AKV 또는 새 대시보드를 만든 따르면 하세요 ADF 관리 되는 id 액세스 권한을 부여 하 여 AKV 하지 않았으면 지금 이미 있는 경우. 다음 형식으로 직접 암호를 입력할 수도 있습니다: `<AKV linked service name>/<secret name>/<secret version>`합니다. 패키지를 실행하는 데 32비트 런타임이 필요한 경우 **32비트 런타임** 확인란을 선택합니다. 

   에 대 한 **패키지 위치**를 선택 **SSISDB**합니다 **파일 시스템 (패키지)** , 또는 **파일 시스템 (프로젝트)** 합니다. 선택 하는 경우 **SSISDB** 패키지를 지정 해야가 여 AZURE-SSIS IR에서 Azure SQL Database 서버/관리 인스턴스 호스트 SSIS 카탈로그 (SSISDB)를 사용 하 여 프로 비전 된 경우 자동으로 선택 됩니다을 하는 패키지 위치로, 실행 하는 SSISDB에 배포 되었습니다. Azure SSIS IR 프로그램을 실행 하는 경우와 **수동 항목** 확인란을 선택 하지 않으면, 찾아 SSISDB에서 기존 폴더/프로젝트/패키지/환경을 선택할 수 있습니다. **새로 고침** 단추를 클릭하여 새로 추가한 폴더/프로젝트/패키지/환경을 찾아서 선택할 수 있도록 SSISDB에서 페치합니다. 
   
   **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. 

   ![설정 탭의 속성 설정 - 자동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Azure SSIS IR 프로그램을 실행 하지 않는 경우 또는 **수동 항목** 확인란이 선택 되어, 다음 형식으로 직접 SSISDB에서 패키지 및 환경 경로 입력할 수 있습니다: `<folder name>/<project name>/<package name>.dtsx` 및 `<folder name>/<environment name>`합니다.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

   선택 하는 경우 **파일 시스템 (패키지)** Universal Naming Convention (제공 하 여 실행할 패키지를 지정 해야가 SSISDB 없이 여 AZURE-SSIS IR 프로 비전 된 경우 자동으로 선택 됩니다을 하는 패키지 위치로, 패키지 파일에 UNC) 경로 (`.dtsx`)에 **패키지 경로**합니다. 예를 들어, Azure Files에서 패키지를 저장 하는 경우 해당 패키지 경로으로 `\\<storage account name>.file.core.windows.net\<file share name>\<package name>.dtsx`입니다. 
   
   구성 파일에 UNC 경로 제공 해야 별도 파일에 패키지를 구성 하는 경우 (`.dtsConfig`)에 **구성 경로**합니다. 예를 들어, Azure Files에 구성을 저장 하면 해당 구성 경로 됩니다 `\\<storage account name>.file.core.windows.net\<file share name>\<configuration name>.dtsConfig`합니다.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings3.png)

   선택 하는 경우 **파일 시스템 (프로젝트)** 프로젝트 파일에 UNC 경로 제공 하 여 실행 하 여 패키지를 지정 해야 패키지 위치로 (`.ispac`)에 **프로젝트 경로** 및 패키지 파일 ( `.dtsx`)의 프로젝트에서 합니다 **패키지 이름**합니다. 예를 들어, Azure Files에서 프로젝트를 저장 하는 경우 해당 프로젝트 경로로 사용 됩니다 `\\<storage account name>.file.core.windows.net\<file share name>\<project name>.ispac`합니다.

   ![설정 탭의 속성 설정 - 수동](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings4.png)

   다음으로, 프로젝트/패키지/구성 파일에 액세스할 자격 증명을 지정 해야 합니다. 패키지 실행 자격 증명 (위 참조)에 대 한 이전 값을 입력 한 경우 다시 사용할 수 있습니다 이러한 검사는 **실행 자격 증명을 패키지 하는 것과 동일** 확인란을 선택 합니다. 패키지 액세스 자격 증명에 대 한 값을 입력 해야 하는 그렇지 않은 경우 (**도메인**/**Username**/**암호**). 예를 들어, Azure Files에서 프로젝트/패키지/구성에 저장 하는 경우는 **도메인** 은 `Azure`; **Username** 은 `<storage account name>`; 및 **암호**는 `<storage account key>`합니다. 또는 해당 값 (위 참조)으로 프로그램 AKV에 저장 된 비밀을 사용할 수 있습니다. 패키지 및 자식 패키지에서 자신의 경로/동일한 프로젝트 구성을 비롯 하 여 패키지에 지정 된 모든 실행 패키지 태스크에 액세스 하려면 이러한 자격 증명 사용 됩니다. 
   
   사용 하는 경우는 **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** 보호 수준을 SQL Server 데이터 도구 (SSDT)를 통해 패키지를 만들 때 입력 해야 값 암호를 **암호화 암호**합니다. 또는 해당 값 (위 참조)으로 프로그램 AKV에 저장 된 비밀을 사용할 수 있습니다. 사용 하는 경우는 **EncryptSensitiveWithUserKey** 또는 구성 파일에 중요 한 값을 다시 입력 해야 하는 보호 수준 합니다 **SSIS 매개 변수** /  **연결 관리자**/**속성을 재정의** 탭 (아래 참조). 사용 하는 경우는 **EncryptAllWithUserKey** 보호 수준이 지원 되지 않습니다, SSDT 통해 다른 보호 수준을 사용 하 여 패키지를 다시 구성 해야 하므로 또는 `dtutil` 명령줄 유틸리티입니다. 
   
   **로깅 수준**에서 패키지 실행에 대해 사전 정의된 로깅 범위를 선택합니다. 사용자 지정 로깅 이름을 대신 입력하려면 **사용자 지정** 확인란을 선택합니다. UNC 경로 제공 하 여 로그 폴더를 지정 해야 하는 패키지에 지정 될 수 있는 표준 로그 공급자를 사용 하 여 외에 패키지 실행을 기록 하려는 경우는 **로깅 경로**합니다. 예를 들어, Azure Files에서 로그를 저장 하는 경우 로깅 경로 됩니다 `\\<storage account name>.file.core.windows.net\<file share name>\<log folder name>`합니다. 하위 폴더는 각 개별 패키지 실행에 대 한이 경로의 만들어지고 SSIS 패키지 실행 작업 ID는 로그 파일이 생성 될 5 분 마다 실행 후 명명 된 합니다. 
   
   마지막으로, 해야 로그 폴더에 액세스 하려면 자격 증명을 지정 합니다. 패키지 액세스 자격 증명 (위 참조)에 대 한 이전 값을 입력 한 경우 다시 사용할 수 있습니다 이러한 검사는 **액세스 자격 증명을 패키지 하는 것과 동일** 확인란을 선택 합니다. 로깅 액세스 자격 증명에 대 한 값을 입력 해야 하는 그렇지 않은 경우 (**도메인**/**Username**/**암호**). 예를 들어, Azure Files에서 로그를 저장 하는 경우는 **도메인** 는 `Azure`; **Username** 는 `<storage account name>`; 및 **암호** 는 `<storage account key>`. 또는 해당 값 (위 참조)으로 프로그램 AKV에 저장 된 비밀을 사용할 수 있습니다. 이러한 자격 증명에 로그를 저장할 사용 됩니다. 
   
   위에서 언급 한 모든 UNC 경로 대 한 정규화 된 파일 이름이 260 자 미만 이어야 합니다 하 고 디렉터리 이름은 248 자 미만 이어야 합니다.

5. 에 **SSIS 매개 변수** SSIS 패키지 실행 작업, Azure SSIS IR 프로그램을 실행 중인 경우에 대 한 탭 **SSISDB** 에 패키지 위치를 선택 하며 **수동 항목** 에 확인란 **설정을** 탭이 선택 되지 않으면, SSISDB에서 선택한 프로젝트/패키지의 기존 SSIS 매개 변수 값을 할당 하 여 표시 됩니다. 그렇지 않으면 매개 변수를 하나씩 입력하여 수동으로 값을 할당할 수 있습니다. 패키지 실행에 성공하려면 매개 변수가 있고 올바르게 입력되었는지 확인합니다. 
   
   사용 하는 경우는 **EncryptSensitiveWithUserKey** SSDT 통해 패키지를 만들 때 보호 수준 및 **파일 시스템 (패키지)** /**파일 시스템 (프로젝트)** 는 선택한 패키지 위치로 해야 또는이 탭이 구성 파일에서 값을 할당 하 여 중요 한 매개 변수를 다시 입력 합니다. 
   
   값에 매개 변수를 할당할 때에 식, 함수, ADF 시스템 변수 및 ADF 파이프라인 매개 변수/변수를 사용 하 여 동적 콘텐츠를 추가할 수 있습니다. 또는 해당 값 (위 참조)으로 프로그램 AKV에 저장 된 비밀을 사용할 수 있습니다.

   ![SSIS 매개 변수 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. 에 **연결 관리자** SSIS 패키지 실행 작업, Azure SSIS IR 프로그램을 실행 중인 경우에 대 한 탭 **SSISDB** 에 패키지 위치를 선택 하며 **수동 항목**확인란을 **설정을** 탭이 선택 되지 않으면, SSISDB에서 선택한 프로젝트/패키지에서 기존 연결 관리자는 해당 속성에 값을 할당할 수 표시 됩니다. 그렇지 않으면 입력할 수 있습니다 하나씩에 해당 속성에 값을 수동으로 할당 – 존재 하 고 성공 하 여 패키지 실행에 대 한 정확 하 게 입력을 확인 하세요. 
   
   사용 하는 경우는 **EncryptSensitiveWithUserKey** SSDT 통해 패키지를 만들 때 보호 수준 및 **파일 시스템 (패키지)** /**파일 시스템 (프로젝트)** 는 선택한 패키지 위치로 해야 또는이 탭이 구성 파일에서 값을 할당 하 여 중요 한 연결 관리자 속성을 다시 입력 합니다. 
   
   연결 관리자 속성에 값을 할당할 때에 식, 함수, ADF 시스템 변수 및 ADF 파이프라인 매개 변수/변수를 사용 하 여 동적 콘텐츠를 추가할 수 있습니다. 또는 해당 값 (위 참조)으로 프로그램 AKV에 저장 된 비밀을 사용할 수 있습니다.

   ![연결 관리자 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. 에 **속성을 재정의** 탭 SSIS 패키지 실행 작업에 대 한 값을 수동으로 할당 –는 존재 하며 올바르게 확인을 하나씩 선택한 패키지에 기존 속성의 경로 입력할 수 있습니다 예: 성공 하 여 패키지 실행에 대해 입력 한 다음 형식으로 사용자 변수의 값을 재정의 하려면 해당 경로 입력: `\Package.Variables[User::<variable name>].Value`합니다. 
   
   사용 하는 경우는 **EncryptSensitiveWithUserKey** SSDT 통해 패키지를 만들 때 보호 수준 및 **파일 시스템 (패키지)** /**파일 시스템 (프로젝트)** 는 선택한 패키지 위치로 해야 또는이 탭이 구성 파일에서 값을 할당 하 여 중요 한 속성을 다시 입력 합니다. 
   
   값 속성에 할당할 때에 식, 함수, ADF 시스템 변수 및 ADF 파이프라인 매개 변수/변수를 사용 하 여 동적 콘텐츠를 추가할 수 있습니다.

   ![속성 재정의 탭의 속성 설정](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

   및 구성 파일에 할당 된 값을 *SSIS 매개 변수* 탭을 사용 하 여 재정의할 수 있습니다 합니다 **연결 관리자**/**속성을 재정의** 탭에서 할당 된 동안 합니다 **연결 관리자** 사용 하 여 탭 재정의할 수도 있습니다는 **속성을 재정의** 탭 합니다.

8. 파이프라인 구성에 대한 유효성을 검사하려면 도구 모음에서 **유효성 검사**를 클릭합니다. **파이프라인 유효성 검사 보고서**를 닫으려면 **>>** 를 클릭합니다.

9. **모두 게시** 단추를 클릭하여 ADF에 파이프라인을 게시합니다. 

### <a name="run-the-pipeline"></a>파이프라인 실행
이 단계에서는 파이프라인 실행을 트리거합니다. 

1. 파이프라인 실행을 트리거하려면 도구 모음에서 **트리거**를 클릭하고 **지금 트리거**를 클릭합니다. 

   ![지금 트리거](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. **파이프라인 실행** 창에서 **마침**을 선택합니다. 

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

1. 왼쪽의 **모니터** 탭으로 전환합니다. 다른 정보(예: 실행 시작 시간)와 함께 파이프라인 실행 및 해당 상태를 확인합니다. 보기를 새로 고치려면 **새로 고침**을 클릭합니다.

   ![파이프라인 실행](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. **작업** 열에서 **작업 실행 보기** 링크를 클릭합니다. 파이프라인에는 하나의 작업(SSIS 패키지 실행 작업)만 있으므로 하나의 작업 실행만 파이프라인으로 표시됩니다.

   ![작업 실행](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Azure SQL 서버의 SSISDB 데이터베이스에 대해 다음 **쿼리**를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

   ```sql
   select * from catalog.executions
   ```

   ![패키지 실행 확인](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. 또한 파이프라인 활동 실행의 출력에서 SSISDB 실행 ID를 가져올 하 고 ID를 사용 하 여 더 포괄적인 실행 로그 및 오류 메시지가 SQL Server Management Studio (SSMS)에서 확인할 수 있습니다.

   ![실행 ID를 가져옵니다.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약

또한 파이프라인이 일정대로(시간별, 일별, 등) 실행되도록 파이프라인에 대해 예약된 트리거를 만들 수도 있습니다. 예를 들어 [데이터 팩터리 만들기 - Data Factory UI](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule)를 참조하세요.

## <a name="run-a-package-with-powershell"></a>PowerShell을 사용하여 패키지 실행
이 섹션에서는 Azure PowerShell을 사용하여 SSIS 패키지를 실행하는 SSIS 패키지 실행 작업이 있는 ADF 파이프라인을 만듭니다. 

[Azure PowerShell을 설치 및 구성하는 방법](/powershell/azure/install-az-ps)의 단계별 지침에 따라 최신 Azure PowerShell 모듈을 설치합니다.

### <a name="create-an-adf-with-azure-ssis-ir"></a>Azure-SSIS IR이 있는 ADF 만들기
이미 Azure-SSIS IR이 프로비전된 기존 ADF를 사용하거나 [자습서: PowerShell을 통해 Azure에 SSIS 패키지 배포](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell)의 단계별 지침에 따라 Azure-SSIS IR이 있는 새 ADF를 만들 수 있습니다.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>SSIS 패키지 실행 작업으로 파이프라인 만들기 
이 단계에서는 SSIS 패키지 실행 작업으로 파이프라인을 만듭니다. 이 작업은 SSIS 패키지를 실행합니다. 

1. 다음 예제와 비슷한 내용이 포함된 **RunSSISPackagePipeline.json**이라는 JSON 파일을 **C:\ADF\RunSSISPackage** 폴더에 만듭니다.

   > [!IMPORTANT]
   > 파일을 저장하기 전에 개체 이름, 설명 및 경로, 속성 및 매개 변수 값, 암호 및 기타 변수 값을 바꾸세요. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "executionCredential": {
                       "domain": "MyDomain",
                       "userName": "MyUsername",
                       "password": {
                           "type": "SecureString",
                           "value": "**********"
                       }
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       },
                       "project_param_2": {
                           "value": {
                               "value": "@pipeline().parameters.MyPipelineParameter",
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
                               "secretName": "MySecret"
                           }
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": {
                                   "value": "@pipeline().parameters.MyUsername",
                                   "type": "Expression"
                               }
                           },
                           "passWord": {
                               "value": {
                                   "type": "AzureKeyVaultSecret",
                                   "store": {
                                       "referenceName": "myAKV",
                                       "type": "LinkedServiceReference"
                                   },
                                   "secretName": "MyPassword",
                                   "secretVersion": "3a1b74e361bf4ef4a00e47053b872149"
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

2. Azure PowerShell에서 `C:\ADF\RunSSISPackage` 폴더로 전환합니다.

3. 파이프라인을 만듭니다 **RunSSISPackagePipeline**을 실행 합니다 **집합 AzDataFactoryV2Pipeline** cmdlet.

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
사용 된 **Invoke AzDataFactoryV2Pipeline** 파이프라인을 실행 하려면 cmdlet. Cmdlet은 향후 모니터링을 위해 파이프라인 실행 ID를 캡처합니다.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>파이프라인 모니터링

다음 PowerShell 스크립트를 실행하여 데이터 복사가 완료될 때까지 지속적으로 파이프라인 실행 상태를 검사합니다. PowerShell 창에서 다음 스크립트를 복사/붙여넣기하고 ENTER 키를 누릅니다. 

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

Azure Portal을 사용하여 파이프라인을 모니터링 할 수도 있습니다. 단계별 지침은 [파이프라인 모니터링](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)을 참조하세요.

### <a name="schedule-the-pipeline-with-a-trigger"></a>트리거를 사용하여 파이프라인 예약
이전 단계에서는 파이프라인을 주문형으로 실행했습니다. 일정 트리거를 만들어 파이프라인을 예약형으로 실행할 수도 있습니다(매시간, 매일 등).

1. **C:\ADF\RunSSISPackage** 폴더에 다음 내용이 포함된 **MyTrigger.json**이라는 JSON 파일을 만듭니다. 

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
2. **Azure PowerShell**에서 **C:\ADF\RunSSISPackage** 폴더로 전환합니다.
3. 실행 합니다 **집합 AzDataFactoryV2Trigger** 트리거를 생성 하는 cmdlet입니다. 

   ```powershell
   Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. 기본적으로 트리거는 중지된 상태입니다. 실행 하 여 트리거를 시작 합니다 **시작 AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. 트리거를 실행 하 여 시작됨인지 확인 합니다 **Get AzDataFactoryV2Trigger** cmdlet. 

   ```powershell
   Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. 한 시간 후에 다음 명령을 실행합니다. 예를 들어 현재 시간이 오후 3:25(UTC)인 경우 오후 4시(UTC)에 명령을 실행합니다. 
    
   ```powershell
   Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Azure SQL Server의 SSISDB 데이터베이스에 대해 다음 쿼리를 실행하여 패키지가 실행되었는지 확인할 수 있습니다. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>다음 단계
다음 블로그 게시물을 참조하십시오.
-   [ADF 파이프라인에서 SSIS 작업을 사용하여 ETL/ELT 워크플로 현대화 및 확장](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
