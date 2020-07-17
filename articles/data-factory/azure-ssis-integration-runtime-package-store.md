---
title: Azure-SSIS Integration Runtime 패키지 저장소를 사용 하 여 패키지 관리
description: Azure-SSIS Integration Runtime 패키지 저장소를 사용 하 여 패키지를 관리 하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.custom: seo-lt-2019
ms.date: 05/25/2020
ms.openlocfilehash: fa1ab60ae5690bd3782e4c849ca7cb5c29d640c2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84198870"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Azure-SSIS Integration Runtime 패키지 저장소를 사용 하 여 패키지 관리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

온-프레미스 SQL Server Integration Services (SSIS) 워크 로드를 클라우드로 전환 & 하려면 Azure Data Factory (ADF)에서 Azure-SSIS Integration Runtime (IR)를 프로 비전 할 수 있습니다. 자세한 내용은 [Azure-SSIS IR 프로 비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)을 참조 하세요. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

패키지 배포 모델을 사용 하는 경우 Azure SQL Managed Instance에서 호스트 하는 파일 시스템/Azure Files/MSDB 위에 패키지 관리 계층을 제공 하는 패키지 저장소를 사용 하 여 Azure-SSIS IR를 프로 비전 할 것인지 선택할 수 있습니다. Azure-SSIS IR 패키지 저장소를 사용 하면 패키지 가져오기/내보내기/삭제/실행 및 [레거시 SSIS 패키지 저장소](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)와 유사한 SQL SERVER MANAGEMENT STUDIO (SSMS)를 통해 실행 중인 패키지를 모니터링/중지할 수 있습니다. 

## <a name="connect-to-azure-ssis-ir"></a>Azure-SSIS IR에 연결

Azure-SSIS IR 프로 비전 되 면이에 연결 하 여 SSMS의 패키지 저장소를 찾아볼 수 있습니다.

![Azure-SSIS IR에 연결](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

SSMS의 **개체 탐색기** 창에 있는 **연결** 드롭다운 메뉴에서 **Azure-SSIS Integration Runtime** 를 선택 합니다. 그런 다음 Azure에 로그인 하 고 패키지 저장소가 연결 된 관련 구독, ADF 및 Azure-SSIS IR를 선택 합니다. **실행 중인 패키지** 및 **저장 된 패키지** 노드를 실행 하는 Azure-SSIS IR 표시 됩니다. **저장 된 패키지** 노드를 확장 하 여 아래의 패키지 저장소를 확인 합니다. 패키지 저장소를 확장 하 여 아래에 폴더와 패키지를 표시 합니다. SSMS가 자동으로 연결 하지 못하는 경우 패키지 저장소에 대 한 액세스 자격 증명을 입력 하 라는 메시지가 표시 될 수 있습니다. 예를 들어 MSDB 위에 있는 패키지 저장소를 확장 하는 경우 먼저 Azure SQL Managed Instance에 연결 하 라는 메시지가 표시 될 수 있습니다.

![Azure SQL Managed Instance에 연결](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>폴더 및 패키지 관리

SSMS에서 Azure-SSIS IR를 검색할 때 패키지 저장소/폴더/패키지를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 하 고 **새 폴더**, **패키지 가져오기**, **패키지 내보내기**, **삭제**또는 **새로 고침**을 선택할 수 있습니다.

   ![폴더 및 패키지 관리](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  **새 폴더** 를 선택 하 여 가져온 패키지에 대 한 새 폴더를 만듭니다.

   *  패키지 **가져오기** 를 선택 하 여 **파일 시스템**, **SQL Server** (MSDB) 또는 레거시 **SSIS 패키지 저장소** 에서 패키지 저장소로 패키지를 가져옵니다.

      ![패키지 가져오기](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      가져올 **패키지 위치** 에 따라 관련 **서버** / **인증 유형을**선택 하 고 필요한 경우 액세스 자격 증명을 입력 한 다음 **패키지 경로**를 선택 하 고 새 **패키지 이름을**입력 합니다. 패키지를 가져올 때 해당 보호 수준을 변경할 수 없습니다. 이를 변경 하려면 SQL Server Data Tools (SSDT) 또는 `dtutil` 명령줄 유틸리티를 사용 합니다.

   *  패키지 **내보내기** 를 선택 하 여 패키지 저장소에서 **파일 시스템**, **SQL Server** (MSDB) 또는 레거시 **SSIS 패키지 저장소**로 패키지를 내보냅니다.

      ![패키지 내보내기](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      내보낼 **패키지 위치** 에 따라 관련 **서버** / **인증 유형을**선택 하 고 필요한 경우 액세스 자격 증명을 입력 한 다음 **패키지 경로**를 선택 합니다. 패키지를 내보낼 때 암호화 된 경우 먼저 암호를 입력 하 여 암호를 해독 한 다음 중요 한 데이터를 저장 하거나 사용자 키/암호를 사용 하 여 모든/중요 한 데이터를 암호화 하지 않도록 보호 수준을 변경할 수 있습니다.

   *  **삭제** 를 선택 하 여 패키지 저장소에서 기존 폴더/패키지를 삭제 합니다.

   *  새로 추가 된 폴더/패키지를 패키지 저장소에 표시 하려면 **새로 고침** 을 선택 합니다.

## <a name="execute-packages"></a>패키지 실행

SSMS에서 Azure-SSIS IR를 검색할 때 저장 된 패키지를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝 하 고 **패키지 실행**을 선택할 수 있습니다.  이렇게 하면 ADF 파이프라인에서 SSIS 패키지 실행 작업으로 Azure-SSIS IR 패키지 실행을 구성할 수 있는 **패키지 실행 유틸리티** 대화 상자가 열립니다.

![패키지 실행 유틸리티 페이지 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![패키지 실행 유틸리티 페이지 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**패키지 실행 유틸리티** 대화 상자의 **일반**, **구성**, **실행 옵션**및 **로깅** 페이지는 SSIS 패키지 실행 작업의 **설정** 탭에 해당 합니다. 여기에서 패키지에 대 한 암호화 암호를 입력 하 고, 패키지 구성 파일에 대 한 정보에 액세스 하 고, 패키지 실행 자격 증명/속성을 입력 하 고, 로그 폴더에 대 한 정보를 액세스할 수 있습니다.  **패키지 실행 유틸리티** 대화 상자의 **값 설정** 페이지는 재정의할 기존 패키지 속성을 입력할 수 있는 SSIS 패키지 실행 작업의 **속성 재정의** 탭에 해당 합니다. 자세한 내용은 [ADF 파이프라인에서 ssis 패키지를 실행 하는 Ssis 패키지 작업](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조 하세요.

**패키지 실행 유틸리티** 대화 상자에서 **실행** 단추를 선택 하면 SSIS 패키지 실행 작업을 사용 하는 새 ADF 파이프라인이 자동으로 생성 되 고 트리거됩니다. 동일한 패키지 실행 설정이 있는 ADF 파이프라인이 이미 있는 경우 다시 실행 되 고 새 파이프라인이 생성 되지 않습니다. ADF 파이프라인과 SSIS 패키지 실행 작업은 `Pipeline_SSMS_YourPackageName_HashString` 각각 및로 지정 됩니다 `Activity_SSMS_YourPackageName` .

![패키지 실행 유틸리티 단추](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS 패키지 작업 실행](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>실행 중인 패키지 모니터링 및 중지

SSMS에서 Azure-SSIS IR를 검색할 때 **실행 중인 패키지** 노드를 확장 하 여 현재 실행 중인 패키지를 볼 수 있습니다.  그 중 하나를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 하 고 **중지** 또는 **새로 고침**을 선택 합니다.

   ![실행 중인 패키지 모니터링 및 중지](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  **중지** 를 선택 하 여 SSIS 패키지 실행 작업으로 패키지를 실행 하는 현재 실행 중인 ADF 파이프라인을 취소 합니다.

   *  새로 **고침** 을 선택 하 여 패키지 저장소에서 새로 실행 되는 패키지를 표시 합니다.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR 모니터링 및 패키지 저장소 편집

SSMS에서 Azure-SSIS IR를 검색할 때이를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 하 고 Azure Data Factory 포털 또는 **새로 고침** **으로 이동** 을 선택할 수 있습니다.

   ![ADF 포털로 이동](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  **Azure Data Factory 포털로 이동** 을 선택 하 여 Azure-SSIS IR을 모니터링할 수 있는 ADF 모니터링 허브의 **통합 런타임** 페이지를 엽니다. **패키지 저장소** 타일에서 Azure-SSIS IR에 연결 된 패키지 저장소 수를 확인할 수 있습니다.  이 숫자를 선택 하면 패키지 저장소에 대 한 액세스 정보를 저장 하는 ADF 연결 된 서비스를 편집할 수 있는 창이 표시 됩니다.

      ![패키지 저장소 편집](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  새로 추가 된 폴더/패키지를 패키지 저장소에 표시 하 고 패키지 저장소에서 실행 중인 패키지를 표시 하려면 **새로 고침** 을 선택 합니다.

## <a name="next-steps"></a>다음 단계

자동 생성 된 ADF 파이프라인은 SSIS 패키지 실행 작업을 사용 하 여 다시 실행/편집 하거나 ADF 포털에서 새로 만들 수 있습니다. 자세한 내용은 [ADF 파이프라인에서 ssis 패키지를 실행 하는 Ssis 패키지 작업](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)을 참조 하세요.