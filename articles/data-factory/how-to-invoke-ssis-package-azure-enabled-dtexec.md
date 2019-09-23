---
title: Azure 지원 dtexec 유틸리티를 사용 하 여 SQL Server Integration Services (SSIS) 패키지 실행 | Microsoft Docs
description: Azure 지원 dtexec 유틸리티를 사용 하 여 SSIS (SQL Server Integration Services) 패키지를 실행 하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/21/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 80d4bd2f4f9ea4c451f312f05fd42fbc1ba433ab
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2019
ms.locfileid: "71181328"
---
# <a name="run-sql-server-integration-services-ssis-packages-with-azure-enabled-dtexec-utility"></a>Azure 지원 dtexec 유틸리티를 사용 하 여 SQL Server Integration Services (SSIS) 패키지 실행
이 문서에서는 Azure 지원 **dtexec** (**azuredtexec**) 명령 프롬프트 유틸리티에 대해 설명 합니다.  Azure Data Factory (ADF)에서 SSIS 패키지를 Azure-SSIS Integration Runtime (IR)에서 실행 하는 데 사용 됩니다.

기존 **dtexec** 유틸리티는 SQL Server와 함께 제공 됩니다. 자세한 내용은 [dtexec 유틸리티](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) 설명서를 참조 하세요.  일반적으로 온-프레미스에서 SSIS 패키지를 실행 하기 위해 타사 orchestrator/스케줄러 (예: 활성 일괄 처리, 제어-M 등)에서 호출 됩니다.  최신 **Azuredtexec** 유틸리티는 SSMS (SQL Server Management Studio) 도구와 함께 제공 됩니다.  Azure에서 SSIS 패키지를 실행 하기 위해 타사 orchestrator/스케줄러에 의해 호출 될 수도 있습니다.  이를 통해 SSIS 패키지를 클라우드로 전환 하는 것을 쉽게 & 수 있습니다.  마이그레이션 후 일상 작업에서 타사 orchestrator/스케줄러를 계속 사용 하려는 경우 이제 **dtexec**대신 **azuredtexec** 를 호출할 수 있습니다.

**Azuredtexec** 는 adf 파이프라인에서 ssis 패키지 실행 작업으로 패키지를 실행 합니다. 자세한 내용은 [ADF 작업으로 ssis 패키지 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 문서를 참조 하세요.  SSMS를 통해 ADF에서 파이프라인을 생성 하는 AAD (Azure Active Directory) 응용 프로그램을 사용 하도록 구성할 수 있습니다.  또한 패키지를 저장 하는 파일 시스템/파일 공유/Azure Files에 액세스 하도록 구성할 수 있습니다.  사용자가 호출 옵션에 대해 지정한 값에 따라 **Azuredtexec** 는 SSIS 패키지 실행 작업을 사용 하 여 고유한 ADF 파이프라인을 생성 하 고 실행 합니다.  해당 옵션에 대해 동일한 값을 사용 하 여 **Azuredtexec** 를 호출 하면 기존 파이프라인이 다시 실행 됩니다.  해당 옵션에 대 한 새 값을 사용 하 여 **Azuredtexec** 를 호출 하면 새 파이프라인이 생성 됩니다.

## <a name="prerequisites"></a>사전 요구 사항
**Azuredtexec**를 사용 하려면 [여기](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)에서 최신 SSMS (버전 18.3 이상)를 다운로드 하 여 설치 합니다.

## <a name="configure-azuredtexec-utility"></a>AzureDTExec 유틸리티 구성
로컬 컴퓨터에 SSMS를 설치 하면 **Azuredtexec**도 설치 됩니다.  설정을 구성 하려면 **관리자 권한으로 실행** 옵션을 사용 하 여 SSMS를 실행 하 고, 종속 된 드롭다운 메뉴 항목 **도구-> azure로 마이그레이션-> Azure 사용 DTExec 구성**을 선택 합니다.

![Azure 사용 dtexec 메뉴 구성](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

이 작업을 수행 하면 **Azuredtexec. 설정** 파일에 쓰기 위해 관리자 권한으로 열어야 하는 **Azuredtexecconfig** 창이 표시 됩니다.  SSMS를 관리자 권한으로 실행 하지 않은 경우 관리자 암호를 입력 하 여 권한을 상승 시킬 수 있도록 UAC (사용자 계정 컨트롤) 창이 표시 됩니다.

![Azure 사용 dtexec 설정 구성](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

**Azuredtexecconfig** 창에서 다음과 같이 구성 설정을 입력할 수 있습니다.

- **ApplicationId**: ADF에 파이프라인을 생성 하는 데 적합 한 권한으로 만든 AAD 앱의 고유 식별자를 입력 합니다. 자세한 내용은 [Azure Portal를 통해 AAD 앱 및 서비스 주체 만들기](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) 를 참조 하세요.

- **Authenticationkey**: AAD 앱에 대 한 인증 키를 입력 합니다.

- **TenantId**: Aad 테 넌 트의 고유 식별자를 입력 합니다 .이 id는 AAD 앱을 만듭니다.

- **SubscriptionId**: ADF를 만든 Azure 구독의 고유 식별자를 입력 합니다.

- **ResourceGroup**: ADF를 만든 Azure 리소스 그룹의 이름을 입력 합니다.

- **DataFactory**:  에서 SSIS 패키지 실행 작업을 사용 하는 고유 파이프라인이 **Azuredtexec**를 호출할 때 제공 되는 옵션의 값에 따라 생성 되는 ADF의 이름을 입력 합니다.

- **Irname**: **Azuredtexec** 를 호출할 때 UNC (범용 명명 규칙) 경로에 지정 된 패키지가 실행 될 ADF에 Azure-SSIS IR 이름을 입력 합니다.

- **Packageaccessdomain**: **Azuredtexec**를 호출할 때 지정 된 UNC 경로에서 패키지에 액세스 하려면 도메인 자격 증명을 입력 합니다.

- **Packageaccessusername**:  **Azuredtexec**를 호출할 때 지정 된 UNC 경로에서 패키지에 액세스 하려면 사용자 이름 자격 증명을 입력 합니다.

- **PackageAccessPassword**: **Azuredtexec**를 호출할 때 지정 된 UNC 경로에서 패키지에 액세스 하려면 암호 자격 증명을 입력 합니다.

- **LogPath**:  로그 폴더의 UNC 경로를 입력 합니다 .이 경로에는 Azure-SSIS IR에 대 한 패키지 실행의 로그 파일이 기록 됩니다.

- **LogLevel**:  Azure-SSIS IR의 패키지 실행에 대해 미리 정의 된 **null**/**기본**/**정보**/표시**성능** 옵션에서 선택한 로깅 범위를 입력 합니다.

- **Logaccessdomain**: **LogPath** 가 지정 되 고 **LogLevel** 이 **null**이 아닌 경우에 필요한 로그 파일을 작성할 때 로그 폴더의 로그 폴더에 액세스 하려면 도메인 자격 증명을 입력 합니다.

- **Logaccessusername**: 로그 파일을 작성할 때 필요한 사용자 이름 자격 증명을 입력 하 여 해당 UNC 경로에 액세스 합니다. **LogPath** 가 지정 되 고 **LogLevel** 이 **null**이 아닌 경우 필요 합니다.

- **LogAccessPassword**: **LogPath** 가 지정 되 고 **LogLevel** 이 **null**이 아닌 경우 필요 하 고 로그 파일을 쓸 때 UNC 경로에서 로그 폴더에 액세스 하려면 암호 자격 증명을 입력 합니다.

- **PipelineNameHashStrLen**: **Azuredtexec**를 호출할 때 제공 하는 옵션 값에서 생성할 해시 문자열의 길이를 입력 합니다.  문자열은 Azure-SSIS IR에서 패키지를 실행 하는 ADF 파이프라인의 고유 이름을 구성 하는 데 사용 됩니다.  길이가 32 자인 경우에는 충분 합니다.

패키지와 로그 파일을 온-프레미스의 파일 시스템/파일 공유에 저장 하려는 경우 온-프레미스 네트워크에 연결 된 VNet에 Azure-SSIS IR를 조인 하 여 패키지를 가져오고 로그 파일을 작성할 수 있습니다. [vnet에 Azure-SSIS IR 조인](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network) 을 참조 하세요. 문서를 참조 하세요.

**Azuredtexec 설정** 파일에 기록 된 중요 한 값이 일반 텍스트로 표시 되지 않도록 하려면 Base64 인코딩의 문자열로 인코딩합니다.  **Azuredtexec**를 호출 하면 모든 b a s e 64로 인코딩된 문자열은 원래 값으로 다시 디코딩됩니다.  액세스할 수 있는 계정을 제한 하 여 **Azuredtexec 설정** 파일의 보안을 강화할 수 있습니다.

## <a name="invoke-azuredtexec-utility"></a>AzureDTExec 유틸리티 호출
명령줄 프롬프트에서 **Azuredtexec** 를 호출 하 고 사용 사례 시나리오에서 특정 옵션에 대 한 관련 값을 제공할 수 있습니다. 예를 들면 다음과 같습니다.

`AzureDTExec.exe
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"
  /Set \package.variables[MyVariable].Value;MyValue
  /De MyEncryptionPassword`

**Azuredtexec** 호출은 **dtexec**호출과 비슷한 옵션을 제공 합니다. 자세한 내용은 [dtexec 유틸리티](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017) 설명서를 참조 하세요.  현재 지원 되는 옵션은 다음과 같습니다.

- **/F [기타]** : 파일 시스템/파일 공유/Azure Files에 저장 된 패키지를 로드 합니다.  이 옵션의 값으로 package.dtsx 확장을 사용 하 여 파일 시스템/파일 공유/Azure Files에서 패키지 파일에 대 한 UNC 경로를 지정할 수 있습니다.  지정 된 UNC 경로에 공백이 있으면 전체 경로에 따옴표를 넣어야 합니다.

- **/또는 [igFile]** : 값을 추출할 구성 파일을 지정 합니다.  이 옵션을 사용 하면 디자인 타임에 지정 된 것과 다른 패키지에 대 한 런타임 구성을 설정할 수 있습니다.  XML 구성 파일에 다른 설정을 저장 한 다음 패키지를 실행 하기 전에 로드할 수 있습니다.  자세한 내용은 [SSIS 패키지 구성](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017) 문서를 참조 하세요.  이 옵션의 값으로 Ssistutorial.dtsconfig 확장을 사용 하 여 파일 시스템/파일 공유/Azure Files의 구성 파일에 대 한 UNC 경로를 지정할 수 있습니다.  지정 된 UNC 경로에 공백이 있으면 전체 경로에 따옴표를 넣어야 합니다.

- **/Conn [방법]** : 패키지의 기존 연결 관리자에 대 한 연결 문자열을 지정 합니다.  이 옵션을 사용 하면 디자인 타임에 지정 된 연결 관리자와 다른 패키지의 기존 연결 관리자에 대 한 런타임 연결 문자열을 설정할 수 있습니다.  이 옵션의 값으로 다음과 `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`같이 지정할 수 있습니다.

- **/Set**: 패키지에서 매개 변수, 변수, 속성, 컨테이너, 로그 공급자, Foreach 열거자 또는 연결의 구성을 재정의 합니다.  이 옵션은 여러 번 지정할 수 있습니다.  이 옵션의 값으로 다음과 같이 지정할 수 있습니다. 예를 들어 `property_path;value` `counter` 변수 값을 `\package.variables[counter].Value;1` 1로 재정의 합니다.  패키지 구성 마법사를 사용 하 여 값을 재정의 하려는 패키지의 항목 `property_path` 에 대 한 값을 찾아서 복사 하 고 붙여 넣을 수 있습니다. 자세한 내용은 [패키지 구성 마법사](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014) 설명서를 참조 하십시오.

- **/De [** 다음으로]: **EncryptAllWithPassword**/**EncryptSensitiveWithPassword** protection 수준으로 구성 된 패키지의 암호 해독 암호를 설정 합니다.

## <a name="next-steps"></a>다음 단계
SSIS 패키지 실행 작업을 사용 하는 고유 파이프라인은 **Azuredtexec**를 호출한 후 생성 되 고 실행 됩니다. 이러한 파이프라인은 편집 하거나 adf 포털에서 다시 실행할 수 있습니다. 자세한 내용은 [ADF 작업으로 SSIS 패키지 실행](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 문서를 참조 하세요.
