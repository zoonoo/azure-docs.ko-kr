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
ms.date: 09/29/2020
ms.openlocfilehash: 29d072c513d9a75055d4bb486f44b17b00b7f0a9
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638349"
---
# <a name="manage-packages-with-azure-ssis-integration-runtime-package-store"></a>Azure-SSIS Integration Runtime 패키지 저장소를 사용 하 여 패키지 관리

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

온-프레미스 SQL Server Integration Services (SSIS) 워크 로드를 클라우드로 전환 & 하려면 Azure Data Factory (ADF)에서 Azure-SSIS Integration Runtime (IR)를 프로 비전 할 수 있습니다. 자세한 내용은 [Azure-SSIS IR 프로 비전](./tutorial-deploy-ssis-packages-azure.md)을 참조 하세요. Azure-SSIS IR에서 지원하는 작업은 다음과 같습니다.

- Azure SQL Database 서버/Managed Instance(프로젝트 배포 모델)가 호스트하는 SSIS 카탈로그(SSISDB)에 배포된 패키지 실행
- Azure SQL Managed Instance(패키지 배포 모델)가 호스트하는 파일 시스템, Azure Files 또는 SQL Server 데이터베이스(MSDB)에 배포된 패키지 실행

패키지 배포 모델을 사용 하는 경우 패키지 저장소를 사용 하 여 Azure-SSIS IR를 프로 비전 할 것인지 여부를 선택할 수 있습니다. Azure SQL Managed Instance에서 호스트 하는 파일 시스템, Azure Files 또는 MSDB 위에 패키지 관리 계층을 제공 합니다. Azure-SSIS IR 패키지 저장소를 사용 하면 패키지 가져오기/내보내기/삭제/실행 및 [레거시 SSIS 패키지 저장소](/sql/integration-services/service/package-management-ssis-service?view=sql-server-2017)와 유사한 SQL SERVER MANAGEMENT STUDIO (SSMS)를 통해 실행 중인 패키지를 모니터링/중지할 수 있습니다. 

## <a name="connect-to-azure-ssis-ir"></a>Azure-SSIS IR에 연결

Azure-SSIS IR 프로 비전 되 면이에 연결 하 여 SSMS의 패키지 저장소를 찾아볼 수 있습니다.

![Azure-SSIS IR에 연결](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect.png)

SSMS의 **개체 탐색기** 창에 있는 **연결** 드롭다운 메뉴에서 **Azure-SSIS Integration Runtime** 를 선택 합니다. 그런 다음 Azure에 로그인 하 고 패키지 저장소로 프로 비전 한 관련 구독, ADF 및 Azure-SSIS IR를 선택 합니다. **실행 중인 패키지** 및 **저장 된 패키지** 노드를 실행 하는 Azure-SSIS IR 표시 됩니다. **저장 된 패키지** 노드를 확장 하 여 아래의 패키지 저장소를 확인 합니다. 패키지 저장소를 확장 하 여 아래에 폴더와 패키지를 표시 합니다. SSMS가 자동으로 연결 하지 못하는 경우 패키지 저장소에 대 한 액세스 자격 증명을 입력 하 라는 메시지가 표시 될 수 있습니다. 예를 들어 MSDB 위에 있는 패키지 저장소를 확장 하는 경우 먼저 Azure SQL Managed Instance에 연결 하 라는 메시지가 표시 될 수 있습니다.

![Azure SQL Managed Instance에 연결](media/azure-ssis-integration-runtime-package-store/ssms-package-store-connect2.png)

## <a name="manage-folders-and-packages"></a>폴더 및 패키지 관리

SSMS에서 Azure-SSIS IR에 연결한 후 패키지 저장소, 폴더 또는 패키지를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝 하 고 **새 폴더** , **패키지 가져오기** , **패키지 내보내기** , **삭제** 또는 **새로 고침** 을 선택할 수 있습니다.

   ![폴더 및 패키지 관리](media/azure-ssis-integration-runtime-package-store/ssms-package-store-manage.png)

   *  **새 폴더** 를 선택 하 여 가져온 패키지에 대 한 새 폴더를 만듭니다.

   *  패키지 **가져오기** 를 선택 하 여 **파일 시스템** , **SQL Server** (MSDB) 또는 레거시 **SSIS 패키지 저장소** 에서 패키지 저장소로 패키지를 가져옵니다.

      ![패키지 가져오기](media/azure-ssis-integration-runtime-package-store/ssms-package-store-import.png)

      가져올 **패키지 위치** 에 따라 관련 **서버** / **인증 유형을** 선택 하 고 필요한 경우 액세스 자격 증명을 입력 한 다음 **패키지 경로** 를 선택 하 고 새 **패키지 이름을** 입력 합니다. 패키지를 가져올 때 해당 보호 수준을 변경할 수 없습니다. 이를 변경 하려면 SQL Server Data Tools (SSDT) 또는 `dtutil` 명령줄 유틸리티를 사용 합니다.

      > [!NOTE]
      > Azure-SSIS IR 패키지 저장소에 SSIS 패키지를 가져오는 것은 한 번만 수행할 수 있으며, SQL Server/SSIS 버전을 유지 하면서 기본 MSDB/파일 시스템/Azure Files에만 복사 합니다. 
      >
      > Azure-SSIS IR 현재 **SQL Server 2017** 을 기반으로 하기 때문에 하위 버전 패키지를 실행 하면 런타임에 SSIS 2017 패키지로 업그레이드 됩니다. 더 높은 버전의 패키지 실행은 지원 되지 않습니다.
      >
      > 또한 레거시 SSIS 패키지 저장소는 특정 SQL Server 버전에 바인딩되고 해당 버전의 SSMS 에서만 액세스할 수 있으므로, 이전 SSIS 패키지 저장소의 하위 버전 패키지를 SSMS 2019 이상 버전을 사용 하 여 Azure-SSIS IR 패키지 저장소로 가져오려면 먼저 지정 된 SSMS 버전을 사용 하 여 파일 시스템으로 내보내야 합니다.
      >
      > 또는 보호 수준을 전환 하는 동안 여러 SSIS 패키지를 Azure-SSIS IR 패키지 저장소로 가져오려면 [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) 명령줄 유틸리티를 사용 합니다. [dtutil를 사용 하 여 여러 패키지 배포](#deploying-multiple-packages-with-dtutil)를 참조 하세요.

   *  패키지 **내보내기** 를 선택 하 여 패키지 저장소에서 **파일 시스템** , **SQL Server** (MSDB) 또는 레거시 **SSIS 패키지 저장소** 로 패키지를 내보냅니다.

      ![패키지 내보내기](media/azure-ssis-integration-runtime-package-store/ssms-package-store-export.png)

      내보낼 **패키지 위치** 에 따라 관련 **서버** / **인증 유형을** 선택 하 고 필요한 경우 액세스 자격 증명을 입력 한 다음 **패키지 경로** 를 선택 합니다. 패키지를 내보낼 때 암호화 된 경우 먼저 암호를 입력 하 여 암호를 해독 한 다음 중요 한 데이터를 저장 하거나 사용자 키 또는 암호를 사용 하 여 암호화 하거나 암호화 하지 않도록 방지 하는 등의 보호 수준을 변경할 수 있습니다.

      > [!NOTE]
      > Azure-SSIS IR 패키지 저장소에서 SSIS 패키지를 내보내는 것은 한 번만 수행할 수 있으며, 보호 수준을 전환 하지 않고 수행 하면 SQL Server/SSIS 버전을 유지 하는 동안에만 복사 됩니다. 그렇지 않으면 SSIS 2019 이상 버전 패키지로 업그레이드 됩니다.
      >
      > Azure-SSIS IR 현재 **SQL Server 2017** 을 기반으로 하기 때문에 하위 버전 패키지를 실행 하면 런타임에 SSIS 2017 패키지로 업그레이드 됩니다. 더 높은 버전의 패키지 실행은 지원 되지 않습니다.
      >
      > 또는 보호 수준을 전환 하는 동안 Azure-SSIS IR 패키지 저장소에서 여러 SSIS 패키지를 내보내려면 [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) 명령줄 유틸리티를 사용 합니다. [dtutil로 여러 패키지 배포](#deploying-multiple-packages-with-dtutil)를 참조 하세요.

   *  **삭제** 를 선택 하 여 패키지 저장소에서 기존 폴더/패키지를 삭제 합니다.

   *  새로 추가 된 폴더/패키지를 패키지 저장소에 표시 하려면 **새로 고침** 을 선택 합니다.

## <a name="execute-packages"></a>패키지 실행

SSMS에서 Azure-SSIS IR에 연결한 후 저장 된 패키지를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 팝 하 고 **패키지 실행** 을 선택할 수 있습니다.  이렇게 하면 ADF 파이프라인에서 SSIS 패키지 실행 작업으로 Azure-SSIS IR 패키지 실행을 구성할 수 있는 **패키지 실행 유틸리티** 대화 상자가 열립니다.

![패키지 실행 유틸리티 페이지 1 & 2](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute.png)

![패키지 실행 유틸리티 페이지 3 & 4](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute2.png)

**패키지 실행 유틸리티** 대화 상자의 **일반** , **구성** , **실행 옵션** 및 **로깅** 페이지는 SSIS 패키지 실행 작업의 **설정** 탭에 해당 합니다. 이러한 페이지에서 패키지에 대 한 암호화 암호와 패키지 구성 파일에 대 한 액세스 정보를 입력할 수 있습니다. 로그 폴더에 대 한 액세스 정보 뿐만 아니라 패키지 실행 자격 증명 및 속성을 입력할 수도 있습니다.  **패키지 실행 유틸리티** 대화 상자의 **값 설정** 페이지는 재정의할 기존 패키지 속성을 입력할 수 있는 SSIS 패키지 실행 작업의 **속성 재정의** 탭에 해당 합니다. 자세한 내용은 [ADF 파이프라인에서 ssis 패키지를 실행 하는 Ssis 패키지 작업](./how-to-invoke-ssis-package-ssis-activity.md)을 참조 하세요.

**실행** 단추를 선택 하면 SSIS 패키지 실행 작업을 사용 하는 새 ADF 파이프라인이 자동으로 생성 되 고 트리거됩니다. 설정이 같은 ADF 파이프라인이 이미 있으면 다시 실행 되 고 새 파이프라인이 생성 되지 않습니다. ADF 파이프라인과 SSIS 패키지 실행 작업은 `Pipeline_SSMS_YourPackageName_HashString` 각각 및로 지정 됩니다 `Activity_SSMS_YourPackageName` .

![패키지 실행 유틸리티 단추](media/azure-ssis-integration-runtime-package-store/ssms-package-store-execute3.png)

![SSIS 패키지 작업 실행](media/azure-ssis-integration-runtime-package-store/ssis-activity-package-store.png)

## <a name="monitor-and-stop-running-packages"></a>실행 중인 패키지 모니터링 및 중지

SSMS에서 Azure-SSIS IR에 연결한 후 **실행 중인 패키지** 노드를 확장 하 여 아래에서 현재 실행 중인 패키지를 볼 수 있습니다.  그 중 하나를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 하 고 **중지** 또는 **새로 고침** 을 선택 합니다.

   ![실행 중인 패키지 모니터링 및 중지](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor.png)

   *  **중지** 를 선택 하 여 SSIS 패키지 실행 작업으로 패키지를 실행 하는 현재 실행 중인 ADF 파이프라인을 취소 합니다.

   *  새로 **고침** 을 선택 하 여 패키지 저장소에서 새로 실행 되는 패키지를 표시 합니다.

## <a name="monitor-azure-ssis-ir-and-edit-package-stores"></a>Azure-SSIS IR 모니터링 및 패키지 저장소 편집

SSMS에서 Azure-SSIS IR에 연결한 후 메뉴를 마우스 오른쪽 단추로 클릭 하 여 메뉴를 표시 하 고 Azure Data Factory 포털 또는 **새로 고침** **으로 이동** 을 선택할 수 있습니다.

   ![ADF 포털로 이동](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor2.png)

   *  **Azure Data Factory 포털로 이동** 을 선택 하 여 Azure-SSIS IR을 모니터링할 수 있는 ADF 모니터링 허브의 **통합 런타임** 페이지를 엽니다. **패키지 저장소** 타일에서 Azure-SSIS IR에 연결 된 패키지 저장소 수를 확인할 수 있습니다.  이 숫자를 선택 하면 패키지 저장소에 대 한 액세스 정보를 저장 하는 ADF 연결 된 서비스를 편집할 수 있는 창이 표시 됩니다.

      ![패키지 저장소 편집](media/azure-ssis-integration-runtime-package-store/ssms-package-store-monitor3.png)

   *  새로 추가 된 폴더/패키지를 패키지 저장소에 표시 하 고 패키지 저장소에서 실행 중인 패키지를 표시 하려면 **새로 고침** 을 선택 합니다.

## <a name="deploying-multiple-packages-with-dtutil"></a>Dtutil를 사용 하 여 여러 패키지 배포

레거시 패키지 배포 모델을 유지 하는 동안 온-프레미스 SSIS 워크 로드를 ADF의 SSIS로 이동 & 하려면 파일 시스템, SQL Server에서 호스트 하는 MSDB 또는 레거시 SSIS 패키지 저장소에서 Azure Files, Azure SQL Managed Instance에서 호스트 되는 MSDB Azure-SSIS IR 또는 패키지 저장소에 패키지를 배포 해야 합니다. 동시에 아직 수행 하지 않은 경우 보호 수준을 사용자 키로 암호화에서 암호화 되지 않음 또는 암호로 암호화로 전환 해야 합니다.

SQL Server/SSIS 설치와 함께 제공 되는 [dtutil](/sql/integration-services/dtutil-utility?view=sql-server-2017) 명령줄 유틸리티를 사용 하 여 여러 패키지를 배치로 배포할 수 있습니다. 특정 SSIS 버전에 바인딩되어 있으므로이를 사용 하 여 보호 수준을 전환 하지 않고 하위 버전의 패키지를 배포 하는 경우에는 SSIS 버전을 유지 하면서 복사 하기만 하면 됩니다. 이를 사용 하 여 배포 하 고 보호 수준을 동시에 전환 하는 경우 SSIS 버전으로 업그레이드 합니다.

 Azure-SSIS IR 현재 **SQL Server 2017** 을 기반으로 하기 때문에 하위 버전 패키지를 실행 하면 런타임에 SSIS 2017 패키지로 업그레이드 됩니다. 더 높은 버전의 패키지 실행은 지원 되지 않습니다.

따라서 런타임 업그레이드를 방지 하려면 패키지 배포 모델에서 Azure-SSIS IR 실행 되는 패키지를 배포할 때 SQL Server/SSIS 2017 설치와 함께 제공 되는 dtutil 2017을 사용 해야 합니다. 이 목적을 위해 무료 [SQL Server/SSIS 2017 Developer Edition](https://go.microsoft.com/fwlink/?linkid=853016) 을 다운로드 하 여 설치할 수 있습니다. 설치 되 면이 폴더에서 dtutil 2017를 찾을 수 있습니다 `YourLocalDrive:\Program Files\Microsoft SQL Server\140\DTS\Binn` .

### <a name="deploying-multiple-packages-from-file-system-on-premises-into-azure-files-with-dtutil"></a>Dtutil를 사용 하 여 온-프레미스 파일 시스템에서 여러 패키지를 Azure Files에 배포

 여러 패키지를 파일 시스템에서 Azure Files에 배포 하 고 해당 보호 수준을 동시에 전환 하려면 명령 프롬프트에서 다음 명령을 실행 합니다. 해당 사례와 관련 된 모든 문자열을 바꾸세요.
  
```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Go to a local folder where you store your packages
cd YourLocalDrive:\...\YourPackageFolder

REM Run dtutil in a loop to deploy your packages from the local folder into Azure Files while switching their protection level
for %f in (*.dtsx) do dtutil.exe /FILE %f /ENCRYPT FILE;Z:\%f;2;YourEncryptionPassword
```

배치 파일에서 위의 명령을 실행 하려면를 `%f` 로 바꿉니다 `%%f` .

레거시 SSIS 패키지의 여러 패키지를 파일 시스템 위에 있는 Azure Files에 배포 하 고 해당 보호 수준을 동시에 전환 하려면 동일한 명령을 사용할 수 있지만를 `YourLocalDrive:\...\YourPackageFolder` 기존 ssis 패키지 저장소에 사용 되는 로컬 폴더로 `YourLocalDrive:\Program Files\Microsoft SQL Server\YourSQLServerDefaultCompatibilityLevel\DTS\Packages\YourPackageFolder` 바꿉니다. 예를 들어 레거시 SSIS 패키지 저장소가 SQL Server 2016에 바인딩되어 있는 경우로 이동 `YourLocalDrive:\Program Files\Microsoft SQL Server\130\DTS\Packages\YourPackageFolder` 합니다.  [SQL Server 기본 호환성 수준 목록](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?view=sql-server-ver15#arguments)에서 `YourSQLServerDefaultCompatibilityLevel` 값을 찾을 수 있습니다.

Azure Files 위에 Azure-SSIS IR 패키지 저장소를 구성한 경우 SSMS 2019 이상 버전에서 Azure-SSIS IR에 연결할 때 배포 된 패키지가 해당 패키지에 표시 됩니다.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-msdb-in-azure-with-dtutil"></a>Dtutil를 사용 하 여 온-프레미스의 MSDB에서 Azure의 MSDB로 여러 패키지 배포

 SQL Server 또는 레거시 SSIS 패키지 저장소에서 호스트 되는 MSDB의 여러 패키지를 Azure SQL Managed Instance에서 호스트 되는 MSDB에 배포 하 고 동시에 해당 보호 수준을 전환 하려면 SSMS에서 SQL Server에 연결 하 고 SSMS 개체 탐색기에서 노드를 마우스 오른쪽 단추로 클릭 `Databases->System Databases->msdb` 하 **Object Explorer** 여 **새 쿼리** 창을 열고 다음 t-sql 스크립트를 실행 합니다. 해당 사례와 관련 된 모든 문자열을 바꾸세요.  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT SQL;'+f.foldername+'\'+NAME+';2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourSQLAuthUsername /DestPassword YourSQLAuthPassword'
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

Azure SQL Managed Instance의 개인/공용 끝점을 사용 하려면를로 대체 `YourSQLManagedInstanceEndpoint` `YourSQLMIName.YourDNSPrefix.database.windows.net` / `YourSQLMIName.public.YourDNSPrefix.database.windows.net,3342` 합니다.

이 스크립트는 다중 사용자, 복사 & 붙여넣기 및 명령 프롬프트에서 실행할 수 있는 MSDB의 모든 패키지에 대 한 dtutil 명령줄을 생성 합니다.

![Dtutil 명령줄 생성](media/azure-ssis-integration-runtime-package-store/sql-server-msdb-to-sql-mi-msdb.png)

```dos
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT SQL;YourFolder\YourPackage1;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT SQL;YourFolder\YourPackage2;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT SQL;YourFolder\YourPackage3;2;YourEncryptionPassword /DestServer YourSQLManagedInstanceEndpoint /DestUser YourUserName /DestPassword YourPassword
```

MSDB 위에 Azure-SSIS IR 패키지 저장소를 구성한 경우 SSMS 2019 이상 버전에서 Azure-SSIS IR에 연결 하면 배포 된 패키지가 해당 패키지에 표시 됩니다.

### <a name="deploying-multiple-packages-from-msdb-on-premises-into-azure-files-with-dtutil"></a>Dtutil를 사용 하 여 온-프레미스의 MSDB에서 여러 패키지를 Azure Files에 배포

 SQL Server 또는 레거시 SSIS 패키지 저장소에서 호스트 되는 MSDB의 여러 패키지를 Azure Files으로 배포 하 고 해당 보호 수준을 동시에 전환 하려면 SSMS에서 SQL Server에 연결 하 고 `Databases->System Databases->msdb` ssms의 **개체 탐색기** 에서 노드를 마우스 오른쪽 단추로 클릭 하 여 **새 쿼리** 창을 열고 다음 t-sql 스크립트를 실행 합니다. 해당 사례와 관련 된 모든 문자열을 바꾸세요.  
  
```sql
BEGIN
  SELECT 'dtutil /SQL '+f.foldername+'\'+NAME+' /ENCRYPT FILE;Z:\'+f.foldername+'\'+NAME+'.dtsx;2;YourEncryptionPassword' 
  FROM msdb.dbo.sysssispackages p
  inner join msdb.dbo.sysssispackagefolders f
  ON p.folderid = f.folderid
END
```

이 스크립트는 다중 사용자, 복사 & 붙여넣기 및 명령 프롬프트에서 실행할 수 있는 MSDB의 모든 패키지에 대 한 dtutil 명령줄을 생성 합니다.

```dos
REM Persist the access credentials for Azure Files on your local machine
cmdkey /ADD:YourStorageAccountName.file.core.windows.net /USER:azure\YourStorageAccountName /PASS:YourStorageAccountKey

REM Connect Azure Files to a drive on your local machine
net use Z: \\YourStorageAccountName.file.core.windows.net\YourFileShare /PERSISTENT:Yes

REM Multiselect, copy & paste, and run the T-SQL-generated dtutil command lines to deploy your packages from MSDB on premises into Azure Files while switching their protection level
dtutil /SQL YourFolder\YourPackage1 /ENCRYPT FILE;Z:\YourFolder\YourPackage1.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage2 /ENCRYPT FILE;Z:\YourFolder\YourPackage2.dtsx;2;YourEncryptionPassword
dtutil /SQL YourFolder\YourPackage3 /ENCRYPT FILE;Z:\YourFolder\YourPackage3.dtsx;2;YourEncryptionPassword
```

Azure Files 위에 Azure-SSIS IR 패키지 저장소를 구성한 경우 SSMS 2019 이상 버전에서 Azure-SSIS IR에 연결할 때 배포 된 패키지가 해당 패키지에 표시 됩니다.

## <a name="next-steps"></a>다음 단계

자동 생성 된 ADF 파이프라인은 SSIS 패키지 실행 작업을 사용 하 여 다시 실행/편집 하거나 ADF 포털에서 새로 만들 수 있습니다. 자세한 내용은 [ADF 파이프라인에서 ssis 패키지를 실행 하는 Ssis 패키지 작업](./how-to-invoke-ssis-package-ssis-activity.md)을 참조 하세요.