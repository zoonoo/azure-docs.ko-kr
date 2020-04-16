---
title: SSDT에서 SSIS 패키지 실행
description: SSDT에서 Azure에서 SSIS 패키지를 실행하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 11e76fea87c60ae2b56cc15d5827be6e1b2b5a01
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399437"
---
# <a name="execute-ssis-packages-in-azure-from-ssdt"></a>SSDT에서 Azure에서 SSIS 패키지 실행

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure 데이터 팩터리(ADF)에서 AZURE-SSIS 통합 런타임(IR)에서 패키지를 실행할 수 있는 SSIS(Azure 지원 SQL Server 통합 서비스) 프로젝트의 기능에 대해 설명합니다.  이 기능을 사용하여 기존 SSIS 패키지를 테스트한 & Azure로 이동/마이그레이션하거나 Azure에서 실행할 새 SSIS 패키지를 개발할 수 있습니다.

이 기능을 사용하면 새 Azure-SSIS IR을 만들거나 기존 IR을 SSIS 프로젝트에 연결한 다음 패키지를 실행할 수 있습니다.  프로젝트 배포 모델의 SSIS 카탈로그(SSISDB)에 배포할 패키지 실행 및 패키지 배포 모델의 파일 시스템/파일 공유/Azure 파일에 배포할 패키지를 지원합니다. 

## <a name="prerequisites"></a>사전 요구 사항
이 기능을 사용하려면 [여기에서](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects) 또는 [여기에서](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)독립 실행형 설치 관리자로 Visual Studio용 SSIS 프로젝트 확장 기능이 있는 최신 SSDT를 다운로드하여 설치하십시오.

## <a name="azure-enable-ssis-projects"></a>Azure 지원 SSIS 프로젝트
SSDT에서 **통합 서비스 프로젝트(Azure 지원)** 템플릿을 사용하여 새 Azure 지원 SSIS 프로젝트를 만들 수 있습니다.

![새 Azure 지원 SSIS 프로젝트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-new-project.png)

또는 SSDT의 솔루션 탐색기 패널에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 팝업한 다음 **Azure 지원** 메뉴 항목을 선택하여 기존 SSIS 프로젝트를 Azure에서 활성화할 수 있습니다.

![Azure-인에이블 기존 SSIS 프로젝트](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project.png)

기존 SSIS 프로젝트를 Azure에서 사용하도록 설정하려면 대상 서버 버전을 현재 **SQL Server 2017인**Azure-SSIS IR에서 지원하는 최신 서버 버전으로 설정해야 하므로 아직 수행하지 않은 경우 대화 창이 나타납니다.

## <a name="connect-azure-enabled-projects-to-ssis-in-azure-data-factory"></a>Azure 지원 프로젝트를 Azure 데이터 팩터리에서 SSIS에 연결
Azure 지원 프로젝트를 ADF의 SSIS에 연결하여 패키지를 Azure 파일에 업로드하고 Azure-SSIS IR에서 실행할 수 있습니다.  이렇게 하려면 다음 단계를 따르십시오.

1. SSDT의 솔루션 탐색기 패널에서 프로젝트 또는 **연결된 Azure 리소스** 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 팝업하고 Azure 데이터 팩터리 메뉴 **항목에서 SSIS에 연결을** 선택하여 **ADF 연결 마법사에서 SSIS를**시작합니다.

   ![ADF에서 SSIS에 연결](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-existing-project2.png)

2. ADF 소개 페이지의 **SSIS에서** 소개를 검토하고 **다음** 단추를 클릭하여 계속합니다.

   ![ADF 소개의 SSIS](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard.png)

3. **ADF의 SSIS IR 선택** 페이지에서 기존 ADF 및 Azure-SSIS IR을 선택하여 패키지를 실행하거나 없는 경우 새 패키지를 만듭니다.
   - 기존 Azure-SSIS IR을 선택하려면 먼저 관련 Azure 구독 및 ADF를 선택합니다.
   - Azure-SSIS IR이 없는 기존 ADF를 선택한 경우 **SSIS IR 만들기** 단추를 클릭하여 ADF 포털/앱에서 새 ADF를 만듭니다.
   - ADF가 없는 기존 Azure 구독을 선택하는 경우 **SSIS IR 만들기** 단추를 클릭하여 **통합 런타임 생성 마법사를**시작하고 위치 및 접두사를 입력하여 다음 패턴에 이름을 지정된 다음 패턴인 **YourPrefix-RG/DF/IR-YourCreationTime에**이름을 지정하여 새 Azure 리소스 그룹, 데이터 팩터리 및 SSIS IR을 자동으로 만들 수 있습니다.
   
   ![ADF에서 SSIS IR 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard2.png)

4. Azure **Storage 선택** 페이지에서 기존 Azure Storage 계정을 선택하여 Azure 파일에 패키지를 업로드하거나 없는 경우 새 계정을 만듭니다.
   - 기존 Azure Storage 계정을 선택하려면 먼저 관련 Azure 구독을 선택합니다.
   - Azure 저장소 계정이 없는 Azure-SSIS IR과 동일한 Azure 구독을 선택하는 경우 Azure-SSIS IR 이름과 생성 날짜의 접두사를 결합하여 이름이 지정된 Azure-SSIS IR과 동일한 위치에서 Azure **저장소 만들기** 단추를 클릭합니다.
   - Azure Storage 계정이 없는 다른 Azure 구독을 선택하는 경우 **Azure 저장소 만들기** 단추를 클릭하여 Azure Portal에서 새 구독을 만듭니다.
   
   ![Azure Storage 선택](media/how-to-invoke-ssis-package-ssdt/ssis-in-adf-connection-wizard3.png)

5. **연결** 버튼을 클릭하여 연결을 완료합니다.  선택한 Azure-SSIS IR 및 Azure 저장소 계정이 연결된 **Azure 리소스** 노드 에 SSDT의 솔루션 탐색기 패널에 표시됩니다.  또한 Azure-SSIS IR의 상태를 새로 고칠 수 있지만 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 팝업한 다음 ADF 포털/앱으로 이동한 **메뉴 항목 시작을** 선택하여 관리할 수 있습니다.

## <a name="execute-ssis-packages-in-azure"></a>Azure에서 SSIS 패키지 실행
### <a name="starting-package-executions"></a>패키지 실행 시작
ADF에서 SSIS에 프로젝트를 연결한 후 Azure-SSIS IR에서 패키지를 실행할 수 있습니다.  패키지 실행을 시작하는 두 가지 옵션이 있습니다.
-  SSDT 도구 모음의 **시작** 버튼을 클릭하여 메뉴를 드롭다운하고 **Azure 메뉴 항목에서 실행을** 선택합니다. 

   ![Azure에서 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package.png)

-  SSDT의 솔루션 탐색기 패널에서 패키지 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 팝업하고 Azure 메뉴 **항목에서 패키지 실행을** 선택합니다.

   ![Azure에서 패키지 실행](media/how-to-invoke-ssis-package-ssdt/ssdt-azure-enabled-execute-package2.png)

> [!NOTE]
> Azure에서 패키지를 실행하려면 Azure-SSIS IR을 실행해야 하므로 Azure-SSIS IR이 중지되면 대화 상자 창이 나타나서 시작해야 합니다.  사용자 지정 설정 시간을 제외한 이 프로세스는 5분 이내에 완료되어야 하지만 Azure-SSIS IR이 가상 네트워크에 가입하는 데 약 20~30분이 걸릴 수 있습니다.  Azure에서 패키지를 실행한 후 Azure-SSIS IR을 중지하여 SSDT의 솔루션 탐색기 패널에서 노드를 마우스 오른쪽 단추로 클릭하여 메뉴를 팝업한 다음 ADF 포털/앱으로 이동하도록 하는 **메뉴 항목 시작을** 선택하여 실행 비용을 관리할 수 있습니다.

### <a name="checking-package-execution-logs"></a>패키지 실행 로그 확인
패키지 실행을 시작하면 SSDT의 진행률 창에 로그 형식이 지정되고 표시됩니다.  장기 실행 패키지의 경우 주기적으로 로그를 분별로 업데이트합니다.  SSDT 도구 모음의 **중지** 단추를 클릭하여 즉시 취소하여 패키지 실행을 중지할 수 있습니다.  또한 UNC(유니버설 명명 규칙) 경로에서 `\\<YourConnectedAzureStorage>.file.core.windows.net\ssdtexecution\<YourProjectName-FirstConnectTime>\<YourPackageName-tmp-ExecutionTime>\logs`로그 원시 데이터를 일시적으로 찾을 수 있지만 하루 후에 정리합니다.

### <a name="switching-package-protection-level"></a>스위칭 패키지 보호 수준
Azure에서 SSIS 패키지를 실행하는 것은 **암호화를 지원하지 않습니다.EncryptSensitiveWithUserKey**/**암호화AllWithUserKey** 보호 수준.  따라서 패키지가 구성되는 경우 Azure-SSIS IR에서 실행하기 위해 패키지를 Azure 파일에 업로드할 때 임의로 생성된 암호로 각각 **EncryptSensitiveWithPassword**/**암호화AllWithPassword로**일시적으로 전환합니다.

> [!NOTE]
> 패키지에 **암호화감 있는**/사용자 키**암호화AllWithUserKey** 보호 수준으로 구성된 다른 패키지를 참조하는 실행 패키지 작업이 포함되어 있는 경우 패키지를 실행하기 전에 각각 **EncryptSensitiveWithPassword**/**암호화암호 암호화를**사용하도록 다른 패키지를 수동으로 다시 구성해야 합니다.

패키지가 이미 암호화로 구성된 **경우암호화암호암호암호보호**/**EncryptAllWithPassword** 레벨은 변경되지 않지만 Azure-SSIS IR에서 실행하기 위해 Azure 파일에 패키지를 업로드할 때 임의로 생성된 암호를 계속 사용합니다.

### <a name="using-package-configuration-file"></a>패키지 구성 파일 사용
패키지 배포 모델의 패키지 구성 파일을 사용하여 런타임에 변수 값을 변경하는 경우 Azure-SSIS IR에서 실행하기 위해 패키지가 있는 해당 파일을 Azure 파일에 자동으로 업로드합니다.

### <a name="using-execute-package-task"></a>패키지 작업 실행 사용
패키지에 로컬 파일 시스템에 저장된 다른 패키지를 참조하는 패키지 작업 실행이 포함되어 있는 경우 다음과 같은 추가 설정을 수행해야 합니다.

1. 프로젝트에 연결된 동일한 Azure Storage 계정으로 다른 패키지를 Azure 파일에 업로드하고 새 UNC 경로를 가져옵니다.`\\test.file.core.windows.net\ssdtexecution\Package1.dtsx`

2. 패키지 작업 실행의 파일 연결 관리자에서 해당 다른 패키지의 파일 경로를 새 UNC 경로로 바꿉니다.
   - SSDT를 실행하는 컴퓨터가 새 UNC 경로에 액세스할 수 없는 경우 파일 연결 관리자의 속성 패널에서 파일 경로를 변경할 수 있습니다.
   - 또는 파일 경로에 변수를 사용하여 런타임에 올바른 값을 할당할 수 있습니다.

패키지에 동일한 프로젝트의 다른 패키지를 참조하는 패키지 작업 실행 작업이 포함되어 있는 경우 추가 설정이 필요하지 않습니다.

## <a name="next-steps"></a>다음 단계
SSDT에서 Azure에서 패키지를 실행하는 데 만족하면 ADF 파이프라인에서 SSIS 패키지 실행 활동으로 배포하고 실행할 수 [있으며 ADF 파이프라인에서 SSIS 패키지 실행 활동으로 SSIS 패키지 실행을 참조할](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)수 있습니다.
