---
title: SSDT에서 SSIS 패키지 실행
description: SSDT에서 Azure에서 SSIS 패키지를 실행 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: sawinark
author: swinarko
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 07/31/2019
ms.openlocfilehash: 1d8261d05f59c7f40ba6b1e2d59d2b15ad56de95
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84424585"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT에서 Azure에서 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SQL Server Data Tools (SSDT)에서 Azure 사용 가능 SQL Server Integration Services (SSIS) 프로젝트의 기능에 대해 설명 합니다 .이 기능을 사용 하면 ADF (Azure Data Factory Azure-SSIS Integration Runtime)에서 패키지를 실행할 수 있습니다.  이 기능을 사용 하 여 기존 SSIS 패키지를 테스트 하기 전에 기존 SSIS 패키지를 테스트 하 여 Azure로 이동 & 하거나 Azure에서 실행할 새 SSIS 패키지를 개발할 수 있습니다.

이 기능을 사용 하 여 새 Azure-SSIS IR을 만들거나 SSIS 프로젝트에 기존 항목을 연결한 다음 패키지를 실행할 수 있습니다.  프로젝트 배포 모델의 SSISDB (SSIS 카탈로그)에 배포할 패키지를 실행 하 고 패키지 배포 모델에서 파일 시스템/파일 공유/Azure Files에 배포할 패키지를 지원 합니다. 

## <a name="prerequisites"></a>사전 요구 사항
이 기능을 사용 하려면 [여기](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) 에서 Visual STUDIO 용 SSIS 프로젝트 확장을 사용 하 여 최신 SSDT를 다운로드 하 여 설치 하거나 [여기](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)에서 독립 실행형 설치 관리자로 설치 하세요.

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS 프로젝트 사용
### <a name="create-new-azure-enabled-ssis-projects"></a>새 Azure 사용 가능 SSIS 프로젝트 만들기
SSDT에서 **Integration Services 프로젝트 (Azure 사용 가능)** 템플릿을 사용 하 여 새 azure 사용 가능 SSIS 프로젝트를 만들 수 있습니다.

   ![새 Azure 사용 가능 SSIS 프로젝트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Azure 사용 프로젝트가 생성 된 후 Azure Data Factory에서 SSIS에 연결 하 라는 메시지가 표시 됩니다.

   ![연결 Azure-SSIS IR 프롬프트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Azure-SSIS IR에 즉시 연결 하려는 경우 자세한 내용은 [Azure-SSIS IR에 연결](#irconnect) 을 참조 하세요. 또한 SSDT의 솔루션 탐색기 panel에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음 **Azure Data Factory의 ssis** 하위 메뉴에 **있는 Azure Data Factory에서 ssis에 연결** 메뉴 항목을 선택 하 여 나중에 연결할 수도 있습니다.

### <a name="azure-enable-existing-ssis-projects"></a>Azure-기존 SSIS 프로젝트를 사용 하도록 설정
기존 SSIS 프로젝트의 경우 다음 단계에 따라 Azure를 사용 하도록 설정할 수 있습니다.

1. SSDT의 솔루션 탐색기 패널에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음 Azure Data Factory 하위 메뉴 **의 SSIS** 아래에서 **Azure 사용 가능 프로젝트** 메뉴 항목을 선택 하 여 **azure 사용 프로젝트 마법사**를 시작 합니다.

   ![Azure-기존 SSIS 프로젝트 사용](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. **Visual Studio 구성 선택** 페이지에서 visual studio 구성을 선택 하 여 Azure에서 패키지 실행 설정을 적용 합니다. 클라우드 구성에 대해 프로젝트를 사용 하도록 설정 하는 클라우드 및 Azure에 대 한 새 Visual Studio 구성을 만드는 것이 좋습니다. 여러 구성을 사용 하 여 다른 환경 (로컬 또는 Azure)을 기반으로 매개 변수에 다른 값을 할당할 수 있습니다. 자세한 내용은 [이 예제](#example) 를 참조 하세요.

   ![Visual Studio 구성 선택](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure-기존 SSIS 프로젝트를 사용 하도록 설정 하려면 대상 서버 버전을 Azure-SSIS IR에서 지원 되는 최신 버전으로 설정 해야 합니다 .이 버전은 현재 **SQL Server 2017**입니다. 따라서 아직 수행 하지 않은 경우 SQL Server 2017에서 지원 되지 않는 추가 구성 요소가 패키지에 포함 되어 있는지 확인 하 고 문제가 발생 하지 않으면 다음 단추를 클릭 하 여 계속 진행 합니다.

   ![대상 서버 버전 전환](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. [Azure-SSIS IR에 연결](#irconnect) 을 참조 하 여 Azure-SSIS IR에 대 한 연결을 완료 합니다.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="irconnect"></a>Azure Data Factory에서 SSIS에 Azure 사용 프로젝트 연결

Azure 사용 프로젝트를 ADF의 SSIS에 연결 하면 패키지를 Azure Files로 업로드 하 고 Azure-SSIS IR에서 실행할 수 있습니다. 이렇게 하려면 다음 단계를 수행 하세요.

1. **ADF의 SSIS 소개** 페이지에서 소개를 검토 하 고 **다음** 단추를 클릭 하 여 계속 합니다.

   ![ADF의 SSIS 소개](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. **Adf에서 SSIS IR 선택** 페이지에서 기존 ADF를 선택 하 고 패키지를 실행 하려면 Azure-SSIS IR 하 고, 없는 경우 새로 만듭니다.
   - 기존 Azure-SSIS IR을 선택 하려면 먼저 관련 Azure 구독 및 ADF를 선택 합니다.
   - Azure-SSIS IR 없는 기존 ADF를 선택 하는 경우에는 **SSIS IR 만들기** 단추를 클릭 하 여 adf 포털/앱에 새 항목을 만듭니다.
   - ADF가 없는 기존 Azure 구독을 선택 하는 경우 **SSIS Ir 만들기** 단추를 클릭 하 여 **Integration Runtime 만들기 마법사**를 시작 합니다 .이 마법사에서는 사용자를 대신 하 여 자동으로 새 Azure 리소스 그룹, Data Factory 및 SSIS IR을 만들 수 있습니다 .이에 대 한 자세한 **접두사-RG/DF/IR-YourCreationTime**.

   ![ADF에서 SSIS IR 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. **Azure Storage 선택** 페이지에서 기존 Azure Storage 계정을 선택 하 여 Azure Files 패키지를 업로드 하거나, 없는 경우 새로 만듭니다.
   - 기존 Azure Storage 계정을 선택 하려면 먼저 관련 Azure 구독을 선택 합니다.
   - Azure Storage 계정이 없는 Azure-SSIS IR와 동일한 Azure 구독을 선택 하는 경우 **Azure Storage 만들기** 단추를 클릭 하 여 Azure-SSIS IR 이름과 만든 날짜의 접두사를 결합 하 여 Azure-SSIS IR와 동일한 위치에 새 항목을 자동으로 만듭니다.
   - Azure Storage 계정이 없는 다른 Azure 구독을 선택 하는 경우 **Azure Storage 만들기** 단추를 클릭 하 여 Azure Portal에 새 구독을 만듭니다.

   ![Azure Storage 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. **연결 단추를** 클릭 하 여 연결을 완료 합니다.  SSDT의 솔루션 탐색기 패널에서 **연결 된 Azure 리소스** 노드 아래에 선택한 Azure-SSIS IR 및 Azure Storage 계정을 표시 합니다.  또한 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음, ADF 포털/앱으로 이동 하 여이 작업을 수행 하는 **시작 메뉴 항목을 선택** 하 여 해당 노드를 마우스 오른쪽 단추로 클릭 하 여 관리할 수 있는 Azure-SSIS IR의 상태를 새로 고칩니다.

## <a name="execute-ssis-packages-in-azure"></a>Azure에서 SSIS 패키지 실행
### <a name="azure-enabled-setting"></a>Azure 사용 설정
Azure에서 패키지를 실행 하기 전에 실행 설정을 구성 하도록 선택할 수 있습니다. SSIS 패키지에 대해 Windows 인증을 사용 하도록 설정 하려면 다음 단계를 수행 하세요.

1. SSDT의 솔루션 탐색기 패널에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음 **Azure Data Factory의 SSIS** 하위 메뉴에서 **Azure 사용 설정** 메뉴 항목을 선택 합니다.

   ![Azure 사용 설정](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. **Windows 인증 사용** 드롭다운 목록을 클릭 하 고 **True**를 선택 합니다. 그런 다음 **Windows 인증 자격 증명** 에 대 한 편집 단추를 클릭 하 여 자격 증명을 입력 합니다.

   ![Windows 인증 사용](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. **Windows 인증 자격 증명** 편집기에서 자격 증명을 제공 합니다.

   ![Windows 인증 자격 증명](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>패키지 실행 시작
ADF에서 SSIS에 프로젝트를 연결한 후 Azure-SSIS IR에서 패키지를 실행할 수 있습니다.  패키지 실행을 시작 하는 두 가지 옵션이 있습니다.
-  SSDT 도구 모음에서 **시작** 단추를 클릭 하 여 메뉴를 드롭다운 하 고 **Azure에서 실행** 메뉴 항목을 선택 합니다. 

   ![Azure에서 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  SSDT의 솔루션 탐색기 패널에서 패키지 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝 하 고 **Azure에서 패키지 실행** 메뉴 항목을 선택 합니다.

   ![Azure에서 패키지 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Azure에서 패키지를 실행 하려면 실행 중인 Azure-SSIS IR 있어야 합니다. 따라서 Azure-SSIS IR 중지 된 경우에는 대화 상자 창에서 해당 작업을 시작 합니다.  사용자 지정 설치 시간을 제외 하 고이 프로세스는 5 분 이내에 완료 되어야 하지만 Azure-SSIS IR 가상 네트워크에 가입 하는 데 약 20-30 분이 걸릴 수 있습니다.  Azure에서 패키지를 실행 한 후에는 SSDT의 솔루션 탐색기 패널에서 해당 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음,이 작업을 수행 하기 위해 ADF 포털/앱으로 이동 하는 시작 메뉴 항목을 선택 하 여 Azure-SSIS IR 실행 비용을 관리 **하는 작업** 을 중지할 수 있습니다.

### <a name="checking-package-execution-logs"></a>패키지 실행 로그를 확인 하는 중
패키지 실행을 시작할 때 SSDT의 진행률 창에 해당 로그를 포맷 하 고 표시 합니다.  장기 실행 패키지의 경우 해당 로그를 분 단위로 정기적으로 업데이트 합니다.  SSDT 도구 모음에서 **중지** 단추를 클릭 하 여 패키지 실행을 중지할 수 있습니다. 그러면 즉시 취소 됩니다.  또한 UNC (범용 명명 규칙) 경로에서 로그 원시 데이터를 일시적으로 찾을 수 있습니다. 단 `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , 1 일 후에 정리 하겠습니다.

### <a name="switching-package-protection-level"></a>패키지 보호 수준 전환
Azure에서 SSIS 패키지를 실행 하는 것은 **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** 보호 수준을 지원 하지 않습니다.  따라서 패키지를 구성 하는 경우 **EncryptSensitiveWithPassword** / Azure-SSIS IR에서 실행 하기 위해 패키지를 Azure Files에 업로드할 때 임의로 생성 된 암호를 사용 하 여 해당 패키지를 일시적으로 EncryptSensitiveWithPassword**EncryptAllWithPassword**로 전환 합니다.

> [!NOTE]
> **EncryptSensitiveWithUserKey**EncryptAllWithUserKey 보호 수준으로 구성 된 다른 패키지를 참조 하는 패키지 실행 태스크가 패키지에 포함 되어 있는 경우 / **EncryptAllWithUserKey** 패키지를 **EncryptSensitiveWithPassword** / 실행 하기 전에 각각 EncryptSensitiveWithPassword**EncryptAllWithPassword**를 사용 하도록 다른 패키지를 수동으로 다시 구성 해야 합니다.

**EncryptSensitiveWithPassword**EncryptAllWithPassword 보호 수준을 사용 하 여 패키지를 이미 구성한 경우에는 해당 패키지를 / **EncryptAllWithPassword** 변경 되지 않은 상태로 유지 하지만 Azure-SSIS IR에서 실행할 수 있도록 패키지를 Azure Files에 업로드할 때 임의로 생성 된 암호를 계속 사용 합니다.

### <a name="using-package-configuration-file"></a>패키지 구성 파일 사용
패키지 배포 모델의 패키지 구성 파일을 사용 하 여 런타임에 변수 값을 변경 하는 경우 Azure-SSIS IR에서 실행할 수 있도록 패키지를 포함 하는 파일을 자동으로 Azure Files에 업로드 합니다.

### <a name="using-execute-package-task"></a>패키지 실행 태스크 사용
패키지에 로컬 파일 시스템에 저장 된 다른 패키지를 참조 하는 패키지 실행 태스크가 포함 되어 있는 경우 다음과 같은 추가 설정 작업을 수행 해야 합니다.

1. 프로젝트에 연결 된 동일한 Azure Storage 계정으로 Azure Files에 다른 패키지를 업로드 하 고 새 UNC 경로를 가져옵니다. 예를 들면`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 패키지 실행 태스크의 파일 연결 관리자에 있는 다른 패키지의 파일 경로를 새 UNC 경로로 바꿉니다.
   - SSDT를 실행 하는 컴퓨터에서 새 UNC 경로에 액세스할 수 없는 경우 파일 연결 관리자의 속성 패널에서 파일 경로를 변경할 수 있습니다.
   - 또는 런타임에 파일 경로에 대 한 변수를 사용 하 여 올바른 값을 할당할 수 있습니다.

패키지에 동일한 프로젝트의 다른 패키지를 참조 하는 패키지 실행 태스크가 포함 되어 있으면 추가 설정이 필요 하지 않습니다.

## <a name="switching-package-execution-environments-with-azure-enabled-projects"></a><a name="example"></a>Azure 사용 프로젝트를 사용 하 여 패키지 실행 환경 전환

Azure 사용 프로젝트를 사용 하 여 패키지 실행 환경을 전환 하려면 여러 Visual Studio 구성을 만들어 환경 관련 매개 변수에 다른 값을 적용할 수 있습니다. 예를 들어, 지정 된 파일의 특성을 설정 하는 **파일 시스템 태스크** 를 포함 하는 간단한 SSIS 패키지가 있습니다. 다음 단계를 사용 하 여 간단히 클라우드로 마이그레이션할 수 있습니다.

1. 대상 파일의 파일 경로인 문자열 형식의 매개 변수 **FilePath** 를 정의 합니다.

   ![패키지 매개 변수 정의](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. 이 매개 변수와 **원본 연결** 을 연결 합니다. 

   ![원본 연결 업데이트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. Visual Studio Configuration Manager에서 클라우드에 대 한 새 Visual Studio 구성을 만듭니다.

4. 각 Visual Studio 구성에 대해이 매개 변수에 대 한 값을 정의 합니다.

   ![매개 변수 값 재정의](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure-cloud 용 Visual Studio 구성에 대해이 SSIS 프로젝트를 사용 하도록 설정 합니다.

6. Azure에서이 패키지를 실행 합니다. 현재 Visual Studio 구성을 전환 하 여 환경을 다시 로컬 환경으로 쉽게 전환할 수 있습니다.

   ![Visual Studio 구성 전환](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

## <a name="next-steps"></a>다음 단계
SSDT에서 Azure의 패키지를 실행 하는 것이 만족 스 러 우면 ADF 파이프라인에서 ssis 패키지 실행 작업으로 배포 하 고 실행할 수 있습니다. [adf 파이프라인에서](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)ssis 패키지를 실행 하려면 Ssis 패키지 실행을 참조 하세요.
