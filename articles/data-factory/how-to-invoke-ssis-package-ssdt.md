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
ms.date: 09/06/2020
ms.openlocfilehash: fb5b5cb0ac4a9ace7b5de5e92308da58fd2b1fec
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89504947"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT에서 Azure에서 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 SQL Server Data Tools (SSDT)에서 Azure 사용 가능 SQL Server Integration Services (SSIS) 프로젝트의 기능을 설명 합니다. SSIS 패키지의 클라우드 호환성을 평가 하 고 Azure Data Factory (ADF)에서 Azure-SSIS Integration Runtime (IR)에서 실행할 수 있습니다. 이 기능을 사용 하 여 기존 패키지를 테스트 한 후 Azure로 이동 하거나 azure에서 실행할 새 패키지를 개발 & 수 있습니다.

이 기능을 사용 하 여 새로 만든/기존 Azure-SSIS IR을 SSIS 프로젝트에 연결한 다음 패키지를 실행할 수 있습니다.  Azure SQL Database 서버 또는 프로젝트 배포 모델의 관리 되는 인스턴스에서 호스팅하는 SSIS 카탈로그 (SSISDB)에 배포할 패키지 실행을 지원 합니다. 패키지 배포 모델에서 Azure SQL 관리 되는 인스턴스에 의해 호스트 되는 파일 시스템/Azure Files/SQL Server 데이터베이스 (MSDB)에 배포 되는 패키지 실행도 지원 합니다. 

## <a name="prerequisites"></a>필수 구성 요소

이 기능을 사용 하려면 [여기](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)에서 Visual STUDIO 용 SSIS 프로젝트 확장 (VS)을 사용 하 여 최신 SSDT를 다운로드 하 여 설치 하세요. 또는 [여기](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)에서 최신 SSDT를 독립 실행형 설치 관리자로 다운로드 하 여 설치할 수도 있습니다.

## <a name="azure-enable-ssis-projects"></a>Azure-SSIS 프로젝트 사용

### <a name="creating-new-azure-enabled-ssis-projects"></a>새 Azure 사용 가능 SSIS 프로젝트 만들기

SSDT에서 **Integration Services 프로젝트 (Azure 사용 가능)** 템플릿을 사용 하 여 새 azure 사용 가능 SSIS 프로젝트를 만들 수 있습니다.

   ![새 Azure 사용 가능 SSIS 프로젝트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

Azure 사용 프로젝트가 생성 된 후 Azure Data Factory에서 SSIS에 연결 하 라는 메시지가 표시 됩니다.

   ![연결 Azure-SSIS IR 프롬프트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-integration-runtime-connect-prompt.png)

Azure-SSIS IR에 즉시 연결 하려면 [Azure-SSIS IR에 연결](#connectssisir) 을 참조 하세요. 자세한 내용은을 참조 하세요. SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 고 메뉴를 팝업 하 여 나중에 연결할 수도 있습니다. 그런 다음 Azure Data Factory 하위 메뉴 **에서** ssis의 **AZURE DATA FACTORY 항목에서 ssis에 연결** 을 선택 합니다.

### <a name="azure-enabling-existing-ssis-projects"></a><a name="azureenableproject"></a> Azure-기존 SSIS 프로젝트를 사용 하도록 설정

기존 SSIS 프로젝트의 경우 다음 단계에 따라 Azure를 사용 하도록 설정할 수 있습니다.

1. SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 합니다. 그런 다음 Azure Data Factory 하위 메뉴 **에서 SSIS** 의 **azure 사용 프로젝트** 항목을 선택 하 여 **azure 사용 가능 프로젝트 마법사**를 시작 합니다.

   ![Azure-기존 SSIS 프로젝트 사용](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-for-existing-project.png)

2. **Visual Studio 구성 선택** 페이지에서 기존 VS 구성을 선택 하 여 Azure에서 패키지 실행 설정을 적용 합니다. 새 [VS 구성 만들기](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019)를 아직 수행 하지 않은 경우 새 항목을 만들 수도 있습니다. 로컬 및 클라우드 환경에서 패키지를 실행 하기 위해 두 개 이상의 서로 다른 VS 구성을 사용 하는 것이 좋습니다. 따라서 클라우드 구성에 대해 프로젝트를 사용 하도록 설정할 수 있습니다. 이러한 방식으로 프로젝트 또는 패키지를 매개 변수화 한 경우 런타임에 다른 실행 환경 (로컬 컴퓨터 또는 Azure)을 기반으로 하 여 프로젝트 또는 패키지 매개 변수에 다른 값을 할당할 수 있습니다. 예를 들어 [패키지 실행 환경 전환](#switchenvironment)을 참조 하세요.

   ![Visual Studio 구성 선택](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-select-visual-studio-configurations.png)

3. Azure-기존 SSIS 프로젝트를 사용 하도록 설정 하려면 대상 서버 버전을 Azure-SSIS IR에서 지 원하는 최신 버전으로 설정 해야 합니다. Azure-SSIS IR 현재 **SQL Server 2017**을 기반으로 합니다. SQL Server 2017에서 지원 되지 않는 추가 구성 요소가 패키지에 포함 되어 있지 않은지 확인 하세요. 또한 사용자 지정 설치를 통해 Azure-SSIS IR에 호환 되는 추가 구성 요소가 모두 설치 되어 있는지 확인 하세요. [Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup)사용자 지정을 참조 하세요. 계속 하려면 **다음** 단추를 선택 합니다.

   ![대상 서버 버전 전환](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-switch-target-server-version-step.png)

4. [Azure-SSIS IR에 연결](#connectssisir) 을 참조 하 여 프로젝트를 Azure-SSIS IR 연결을 완료 합니다.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a><a name="connectssisir"></a> Azure Data Factory에서 SSIS에 Azure 사용 프로젝트 연결

Azure 사용 프로젝트를 ADF의 SSIS에 연결 하면 패키지를 Azure Files로 업로드 하 고 Azure-SSIS IR에서 실행할 수 있습니다. 이렇게 하려면 다음 단계를 수행 합니다.

1. **ADF의 SSIS 소개** 페이지에서 소개를 검토 하 고 **다음** 단추를 선택 하 여 계속 합니다.

   ![ADF의 SSIS 소개](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

2. **Adf에서 SSIS IR 선택** 페이지에서 기존 ADF를 선택 하 고 패키지를 실행할 Azure-SSIS IR 합니다. 새 항목을 만들 수도 있습니다 (없는 경우).
   - 기존 Azure-SSIS IR을 선택 하려면 먼저 관련 Azure 구독 및 ADF를 선택 합니다.
   - Azure-SSIS IR 없는 기존 ADF를 선택 하는 경우에는 **SSIS IR 만들기** 단추를 선택 하 여 adf 포털에서 새 항목을 만듭니다. 만든 후에는이 페이지로 돌아와서 새 Azure-SSIS IR을 선택할 수 있습니다.
   - ADF가 없는 기존 Azure 구독을 선택 하는 경우 **CREATE SSIS IR** 단추를 선택 하 여 **Integration Runtime 만들기 마법사**를 시작 합니다. 마법사에서 사용자를 대신 하 여 새 Azure 리소스 그룹, Data Factory 및 SSIS IR을 자동으로 만들도록 지정 된 위치와 접두사를 입력할 수 있습니다 .이에 대 한 자세한 **접두사-RG/DF/IR-YourCreationTime**. 만든 후에는이 페이지로 돌아와서 새 ADF를 선택 하 고 Azure-SSIS IR 수 있습니다.

   ![ADF에서 SSIS IR 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

3. **Azure Storage 선택** 페이지에서 기존 Azure Storage 계정을 선택 하 Azure Files 패키지를 업로드 합니다. 새 항목을 만들 수도 있습니다 (없는 경우).
   - 기존 Azure Storage 계정을 선택 하려면 먼저 관련 Azure 구독을 선택 합니다.
   - Azure Storage 계정이 없는 Azure-SSIS IR와 동일한 Azure 구독을 선택 하는 경우 **Azure Storage 만들기** 단추를 선택 합니다. 사용자를 대신 하 여 Azure-SSIS IR와 동일한 위치에 자동으로 새 이름을 만들고 Azure-SSIS IR 이름과 만든 날짜의 접두사를 결합 하 여 이름을 지정 합니다. 만든 후에는이 페이지로 돌아와서 새 Azure Storage 계정을 선택할 수 있습니다.
   - Azure Storage 계정이 없는 다른 Azure 구독을 선택 하는 경우 **Azure Storage 만들기** 단추를 선택 하 여 Azure Portal에 새 구독을 만듭니다. 만든 후에는이 페이지로 돌아와서 새 Azure Storage 계정을 선택할 수 있습니다.

   ![Azure Storage 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

4. **연결** 단추를 선택 하 Azure-SSIS IR 프로젝트에 대 한 연결을 완료 합니다. SSDT의 솔루션 탐색기 창에 있는 **연결 된 Azure 리소스** 노드 아래에 선택한 Azure-SSIS IR 및 Azure Storage 계정을 표시 합니다. 또한 정기적으로 새로 고치고 Azure-SSIS IR의 상태를 표시 합니다. 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음,이를 위해 ADF 포털로 이동 하는 **Start\stop\manage** 항목을 선택 하 여 Azure-SSIS IR를 관리할 수 있습니다.

## <a name="assess-ssis-projectpackages-for-executions-in-azure"></a>Azure에서 실행에 대 한 SSIS project\\oms 패키지 평가

### <a name="assessing-single-or-multiple-packages"></a>단일 또는 여러 패키지 평가

Azure에서 패키지를 실행 하기 전에이를 평가 하 여 잠재적인 클라우드 호환성 문제를 파악할 수 있습니다. 여기에는 마이그레이션 차단기 및 알고 있어야 하는 추가 정보가 포함 됩니다. 
-  프로젝트에서 단일 패키지를 한 번에 하나씩 또는 모든 패키지를 평가 하는 옵션이 있습니다.

   ![패키지 평가](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-package.png)

   ![프로젝트 평가](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project.png)

-  SSDT의 **평가 보고서** 창에서 표시 되는 모든 잠재적 클라우드 호환성 문제를 확인할 수 있습니다. 각 문제에 대 한 자세한 설명 및 권장 사항이 있습니다. 이러한 문제를 완화 해야 하는 사용자와 공유할 수 있는 CSV 파일로 평가 보고서를 내보낼 수도 있습니다. 

   ![평가 보고서](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assess-project-result.png)

### <a name="suppressing-assessment-rules"></a>평가 규칙 표시 안 함

일부 잠재적인 클라우드 호환성 문제가 적용 되지 않거나 패키지에서 적절 하 게 완화 된 것이 확실 한 경우 해당 문제를 노출 하는 관련 평가 규칙을 억제할 수 있습니다. 이렇게 하면 후속 평가 보고서의 노이즈가 줄어듭니다.
-  SSDT의 평가 **보고서** 창에서 평가 **규칙** 제거 제거 링크를 선택 하 여 평가 규칙 **억제 설정** 창을 표시 합니다. 여기서 표시 하지 않을 평가 규칙을 선택할 수 있습니다.

   ![평가 규칙 비 표시 설정](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings.png)

-  또는 SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝업 합니다. Azure Data Factory 하위 메뉴 **에서 SSIS** 의 **Azure 사용 설정** 항목을 선택 하 여 프로젝트 속성 페이지를 포함 하는 창을 팝업 합니다. **Azure 사용 설정** 섹션에서 **억제 된 평가 규칙 id** 속성을 선택 합니다. 마지막으로, 줄임표 (**...**) 단추를 선택 하 여 **평가 규칙 억제 설정** 창을 팝 합니다. 여기서 표시 하지 않을 평가 규칙을 선택할 수 있습니다.

   ![Azure 사용 설정](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

   ![Azure 사용 설정을 통한 평가 규칙 비 표시 설정](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-assessment-rule-suppression-settings-via-azure-enabled-settings.png)

## <a name="execute-ssis-packages-in-azure"></a>Azure에서 SSIS 패키지 실행

### <a name="configuring-azure-enabled-settings"></a><a name="azureenabledsettings"></a> Azure 사용 설정 구성

Azure에서 패키지를 실행 하기 전에 azure에서 사용할 수 있는 설정을 구성할 수 있습니다. 예를 들어 다음 단계를 수행 하 여 Azure-SSIS IR에서 Windows 인증을 사용 하도록 설정 하 여 온-프레미스/클라우드 데이터 저장소에 액세스할 수 있습니다.

1. SSDT의 솔루션 탐색기 창에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 합니다. 그런 다음 Azure Data Factory 하위 메뉴 **에서 SSIS** 의 **Azure 사용 설정** 항목을 선택 하 여 프로젝트 속성 페이지를 포함 하는 창을 표시 합니다.

   ![Azure 사용 설정](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-azure-enabled-setting.png)

2. **Azure 사용 설정** 에서 **Windows 인증 사용** 속성 섹션을 선택 하 고 드롭다운 메뉴에서 **True** 를 선택 합니다. 그런 다음 **Windows 인증 자격 증명** 속성을 선택 하 고 줄임표 (**...**) 단추를 선택 하 여 **windows 인증 자격 증명** 창을 표시 합니다.

   ![Windows 인증을 사용하도록 설정](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-open.png)

3. Windows 인증 자격 증명을 입력 합니다. 예를 들어 Azure Files에 액세스 하려면 `Azure` `YourStorageAccountName` `YourStorageAccountKey` **도메인**, **사용자 이름**및 **암호**에 각각, 및을 입력 하면 됩니다.

   ![Windows 인증 자격 증명](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-windows-authentication-credential.png)

### <a name="starting-package-executions"></a>패키지 실행 시작

Azure 사용 프로젝트를 ADF의 SSIS에 연결 하 고, 클라우드 호환성을 평가 하 고, 잠재적인 문제를 완화 하 고 나면 Azure-SSIS IR에서 패키지를 실행/테스트할 수 있습니다.
-  SSDT 도구 모음에서 **시작** 단추를 선택 하 여 메뉴를 드롭다운 합니다. 다음으로 **Azure에서 실행** 항목을 선택 합니다.

   ![Azure에서 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  또는 SSDT의 솔루션 탐색기 창에서 패키지 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝업 합니다. 다음으로 Azure 항목 **에서 패키지 실행** 을 선택 합니다.

   ![Azure에서 패키지 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Azure에서 패키지를 실행 하려면 실행 중인 Azure-SSIS IR 있어야 합니다. 따라서 Azure-SSIS IR 중지 된 경우에는 대화 상자 창에서 해당 작업을 시작 합니다. 사용자 지정 설치 시간을 제외 하 고이 프로세스는 5 분 이내에 완료 되어야 하지만 Azure-SSIS IR 가상 네트워크에 가입 하는 데 약 20-30 분이 걸릴 수 있습니다. Azure에서 패키지를 실행 한 후에는 SSDT의 솔루션 탐색기 창에서 해당 노드를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 한 다음, ADF 포털로 이동 하는 **Start\stop\manage** 항목을 선택 하 여 실행 비용을 관리 Azure-SSIS IR을 중지할 수 있습니다.

### <a name="using-execute-package-task"></a>패키지 실행 태스크 사용

패키지에 로컬 파일 시스템에 저장 된 자식 패키지를 참조 하는 패키지 실행 태스크가 포함 되어 있는 경우 다음과 같은 추가 단계를 수행 합니다.

1. 프로젝트에 연결 된 동일한 Azure Storage 계정으로 Azure Files에 자식 패키지를 업로드 하 고 새 UNC (범용 명명 규칙) 경로를 가져옵니다. 예를 들면 `\\YourStorageAccountName.file.core.windows.net\ssdtexecution\YourChildPackage1.dtsx`

2. 패키지 실행 태스크의 파일 연결 관리자에 있는 자식 패키지의 파일 경로를 새 UNC 경로로 바꿉니다.
   - SSDT를 실행 하는 로컬 컴퓨터에서 새 UNC 경로에 액세스할 수 없는 경우 파일 연결 관리자의 속성 패널에 해당 경로를 입력할 수 있습니다.
   - 또는 런타임에 파일 경로에 대 한 변수를 사용 하 여 올바른 값을 할당할 수 있습니다.

패키지에 동일한 프로젝트의 자식 패키지를 참조 하는 패키지 실행 태스크가 포함 되어 있으면 추가 단계가 필요 하지 않습니다.

### <a name="switching-package-protection-level"></a>패키지 보호 수준 전환

Azure에서 SSIS 패키지를 실행 하는 것은 **EncryptSensitiveWithUserKey** / **EncryptAllWithUserKey** 보호 수준을 지원 하지 않습니다. 따라서 패키지가 해당 패키지를 사용 하도록 구성 된 경우에는 각각 **EncryptSensitiveWithPassword** / **EncryptAllWithPassword** 보호 수준을 사용 하 여 임시로 변환 합니다. 또한 Azure-SSIS IR에 대 한 실행을 위해 패키지를 Azure Files으로 업로드 하는 경우에도 암호화 암호를 임의로 생성 합니다.

> [!NOTE]
> **EncryptSensitiveWithUserKey**EncryptAllWithUserKey 보호 수준을 사용 하도록 구성 된 자식 패키지를 참조 하는 패키지 실행 태스크가 패키지에 포함 되어 있는 경우 / **EncryptAllWithUserKey** 패키지를 **EncryptSensitiveWithPassword** / 실행 하기 전에 각각 EncryptSensitiveWithPassword**EncryptAllWithPassword** protection 수준을 사용 하도록 해당 자식 패키지를 수동으로 다시 구성 해야 합니다.

패키지가 이미 **EncryptSensitiveWithPassword**EncryptAllWithPassword 보호 수준을 사용 하도록 구성 된 경우에는 / **EncryptAllWithPassword** 변경 되지 않은 상태로 유지 됩니다. Azure-SSIS IR에서 실행 하기 위해 패키지를 Azure Files에 업로드할 때에도 암호화 암호가 임의로 생성 됩니다.

### <a name="switching-package-execution-environments"></a><a name="switchenvironment"></a> 패키지 실행 환경 전환

프로젝트 배포 모델에서 프로젝트/패키지를 매개 변수화 하는 경우 여러 VS 구성을 만들어 패키지 실행 환경을 전환할 수 있습니다. 이러한 방식으로 런타임에 프로젝트/패키지 매개 변수에 환경 특정 값을 할당할 수 있습니다. 로컬 및 클라우드 환경에서 패키지를 실행 하기 위해 두 개 이상의 서로 다른 VS 구성을 사용 하는 것이 좋습니다. 따라서 클라우드 구성에 대해 프로젝트를 사용 하도록 설정할 수 있습니다. 로컬 컴퓨터와 Azure 간에 패키지 실행 환경을 전환 하는 단계별 예제는 다음과 같습니다.

1. 패키지에 파일의 특성을 설정 하는 파일 시스템 태스크가 포함 되어 있다고 가정해 보겠습니다. 로컬 컴퓨터에서 실행 하는 경우 로컬 파일 시스템에 저장 된 파일의 특성을 설정 합니다. Azure-SSIS IR에서 실행 하면 Azure Files에 저장 된 파일의 특성을 설정 하는 것이 좋습니다. 먼저 문자열 형식의 패키지 매개 변수를 만들고 대상 파일 경로의 값을 저장할 파일 이름을 **FilePath** 로 만듭니다.

   ![패키지 매개 변수 만들기](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-define-parameters.png)

2. 그런 다음 **파일 시스템 태스크 편집기** 창의 **일반** 페이지에서 **원본 연결** 섹션의 **SourceVariable** 속성을 **FilePath** package 매개 변수와 매개 변수화 합니다. 

   ![소스 연결 매개 변수화](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-update-task-with-parameters.png)

3. 기본적으로 **개발**이라는 로컬 환경에서 패키지 실행에 대 한 기존 VS 구성이 있습니다. **Azure**라는 클라우드 환경에서 패키지 실행에 대 한 새 vs 구성을 만듭니다. 아직 수행 하지 않은 경우 [새 vs 구성 만들기](https://docs.microsoft.com/visualstudio/ide/how-to-create-and-edit-configurations?view=vs-2019)를 참조 하세요.

4. 패키지의 매개 변수를 볼 때 **구성에 매개 변수 추가** 단추를 선택 하 여 패키지에 대 한 **매개 변수 값 관리** 창을 엽니다. 그런 다음 **개발** 및 **Azure** 구성에서 **FilePath** 패키지 매개 변수에 다른 대상 파일 경로 값을 할당 합니다.

   ![매개 변수 값 할당](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-override-parameter.png)

5. Azure-클라우드 구성에 대해 프로젝트를 사용 하도록 설정 합니다. 아직 수행 하지 않은 경우 [azure-기존 SSIS 프로젝트 사용](#azureenableproject)을 참조 하세요. 그런 다음 Azure-SSIS IR에 대 한 Windows 인증을 사용 하 여 Azure Files에 액세스할 수 있도록 Azure 사용 설정을 구성 합니다. 아직 수행 하지 않은 경우 [azure 사용 설정 구성](#azureenabledsettings)을 참조 하세요.

6. Azure에서 패키지를 실행 합니다. **개발** 구성을 선택 하 여 패키지 실행 환경을 다시 로컬 컴퓨터로 전환할 수 있습니다.

   ![Visual Studio 구성 전환](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-example-switch-configurations.png)

### <a name="using-package-configuration-file"></a>패키지 구성 파일 사용

패키지 배포 모델에서 패키지 구성 파일을 사용 하는 경우 런타임에 패키지 속성에 환경 관련 값을 할당할 수 있습니다. Azure-SSIS IR에서 실행할 수 있도록 패키지를 포함 하는 파일을 자동으로 Azure Files에 업로드 합니다.

### <a name="checking-package-execution-logs"></a>패키지 실행 로그를 확인 하는 중

패키지 실행을 시작한 후 SSDT의 **진행률** 창에서 해당 로그를 포맷 하 고 표시 합니다. 장기 실행 패키지의 경우 로그를 정기적으로 분 단위로 업데이트 합니다. SSDT 도구 모음에서 **중지** 단추를 선택 하 여 패키지 실행을 즉시 취소할 수 있습니다. 또한 다음 UNC 경로에서 로그의 원시 데이터를 일시적으로 찾을 수 있습니다. 단 `\\<YourStorageAccountName>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs` , 1 일 후에 정리 합니다.

## <a name="current-limitations"></a>현재 제한 사항

-  Azure 지원 SSDT은 상용/글로벌 클라우드 지역만 지원 하며 지금은 정부/국가 클라우드 지역을 지원 하지 않습니다.

## <a name="next-steps"></a>다음 단계

SSDT에서 Azure의 패키지를 실행 하는 것이 만족 스 러 우면 ADF 파이프라인에서 ssis 패키지 실행 작업으로 배포 하 고 실행할 수 있습니다. [adf 파이프라인에서](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)ssis 패키지를 실행 하는 ssis 패키지 작업을 참조 하세요.
