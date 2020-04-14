---
title: Azure 지원 dtexec 유틸리티를 사용하여 SQL 서버 통합 서비스(SSIS) 패키지 실행
description: Azure 지원 dtexec 유틸리티를 사용하여 SQL Server 통합 서비스(SSIS) 패키지를 실행하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/12/2020
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.openlocfilehash: 006d4fa9ed09170a423e796e893b817e079e861b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261937"
---
# <a name="run-sql-server-integration-services-packages-with-the-azure-enabled-dtexec-utility"></a>Azure 지원 dtexec 유틸리티를 사용하여 SQL 서버 통합 서비스 패키지 실행
이 문서에서는 Azure 지원 dtexec(AzureDTExec) 명령 프롬프트 유틸리티에 대해 설명합니다. Azure 데이터 팩터리의 IR(Azure-SSIS 통합 런타임)에서 SQL 서버 통합 서비스(SSIS) 패키지를 실행하는 데 사용됩니다.

기존의 dtexec 유틸리티는 SQL Server와 함께 제공됩니다. 자세한 내용은 [dtexec 유틸리티](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)를 참조하십시오. ActiveBatch 및 Control-M과 같은 타사 오케스트레이터 또는 스케줄러가 SSIS 패키지를 온-프레미스에서 실행하기 위해 호출하는 경우가 많습니다. 

최신 AzureDTExec 유틸리티에는 Sql 서버 관리 스튜디오(SSMS) 도구가 함께 제공됩니다. 또한 타사 오케스트레이터 또는 스케줄러가 Azure에서 SSIS 패키지를 실행하기 위해 호출할 수도 있습니다. 이를 통해 SSIS 패키지를 클라우드로 리프팅 및 이동 또는 마이그레이션할 수 있습니다. 마이그레이션 후 일상적인 작업에서 타사 오케스트레이터 또는 스케줄러를 계속 사용하려는 경우 이제 dtexec 대신 AzureDTExec을 호출할 수 있습니다.

AzureDTExec는 데이터 팩터리 파이프라인에서 SSIS 패키지 활동을 실행하면서 패키지를 실행합니다. 자세한 내용은 [SSIS 패키지 실행을 Azure 데이터 팩터리 활동으로](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)참조하십시오. 

AzureDTExec은 SSMS를 통해 데이터 팩터리에서 파이프라인을 생성하는 Azure Active Directory(Azure AD) 응용 프로그램을 사용하도록 구성할 수 있습니다. 패키지를 저장하는 파일 시스템, 파일 공유 또는 Azure 파일에 액세스하도록 구성할 수도 있습니다. 호출 옵션에 대해 지정한 값에 따라 AzureDTExec은 SSIS 패키지 실행 활동이 있는 고유한 데이터 팩터리 파이프라인을 생성하고 실행합니다. 옵션에 대해 동일한 값을 가진 AzureDTExec을 호출하면 기존 파이프라인이 다시 실행됩니다.

## <a name="prerequisites"></a>사전 요구 사항
AzureDTExec을 사용하려면 버전 18.3 이상인 최신 버전의 SSMS를 다운로드하여 설치합니다. [이 웹 사이트](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)에서 다운로드합니다.

## <a name="configure-the-azuredtexec-utility"></a>AzureDTExec 유틸리티 구성
로컬 컴퓨터에 SSMS를 설치하면 AzureDTExec도 설치됩니다. 설정을 구성하려면 **관리자로 실행** 옵션을 사용하여 SSMS를 시작합니다. 그런 다음 Azure**구성 Azure 지원 DTExec으로****마이그레이션하는** >  **도구를** > 선택합니다.

![Azure 지원 dtexec 메뉴 구성](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-menu.png)

이 작업은 *AzureDTExec.settings* 파일에 쓸 수 있도록 관리 권한으로 열어야 하는 **AzureDTExecConfig** 창을 엽니다. 관리자로 SSMS를 실행하지 않은 경우 UAC(사용자 계정 제어) 창이 열립니다. 관리자 암호를 입력하여 권한을 높입니다.

![Azure 지원 dtexec 설정 구성](media/how-to-invoke-ssis-package-azure-enabled-dtexec/ssms-azure-enabled-dtexec-settings.png)

**AzureDTExecConfig** 창에서 다음과 같이 구성 설정을 입력합니다.

- **ApplicationId**: 데이터 팩터리에서 파이프라인을 생성하는 올바른 권한으로 만드는 Azure AD 앱의 고유 식별자를 입력합니다. 자세한 내용은 [Azure 포털을 통해 Azure AD 앱 및 서비스 주체 만들기를](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)참조하십시오.
- **인증키**: Azure AD 앱의 인증 키를 입력합니다.
- **TenantId**: Azure AD 앱이 만들어지는 Azure AD 테넌트의 고유 식별자를 입력합니다.
- **DataFactory**: AzureDTExec을 호출할 때 제공된 옵션 값에 따라 SSIS 패키지 작업을 실행하는 고유한 파이프라인이 생성되는 데이터 팩터리의 이름을 입력합니다.
- **IRName**: AzureDTExec을 호출할 때 UNC(유니버설 명명 규칙) 경로에 지정된 패키지가 실행되는 데이터 팩터리에서 Azure-SSIS IR의 이름을 입력합니다.
- **파이프라인NameHashStrLen**: AzureDTExec을 호출할 때 제공하는 옵션 의 값에서 생성할 해시 문자열의 길이를 입력합니다. 문자열은 Azure-SSIS IR에서 패키지를 실행하는 데이터 팩터리 파이프라인에 대한 고유한 이름을 형성하는 데 사용됩니다. 일반적으로 길이는 32자면 충분합니다.
- **리소스 그룹**: 데이터 팩터리가 만들어진 Azure 리소스 그룹의 이름을 입력합니다.
- **SubscriptionId**: 데이터 팩터리가 만들어진 Azure 구독의 고유 식별자를 입력합니다.
- **LogAccessDomain**: **LogPath가** 지정되고 **LogLevel이** **null이**아닐 때 필요한 로그 파일을 작성할 때 UNC 경로에서 로그 폴더에 액세스하기 위해 도메인 자격 증명을 입력합니다.
- **LogAccessPassword**: **LogPath가** 지정되고 **LogLevel이** **null이**아닐 때 필요한 로그 파일을 작성할 때 UNC 경로에서 로그 폴더에 액세스하려면 암호 자격 증명을 입력합니다.
- **LogAccessUserName**: **LogPath가** 지정되고 **LogLevel이** **null이**아닐 때 필요한 로그 파일을 작성할 때 UNC 경로에서 로그 폴더에 액세스하기 위해 사용자 이름 자격 증명을 입력합니다.
- **LogLevel**: Azure-SSIS IR에서 패키지 실행에 대해 미리 정의된 **null,** **기본,** **세부**정보 또는 **성능** 옵션에서 선택한 로깅 범위를 입력합니다.
- **LogPath**: Azure-SSIS IR에서 패키지 실행의 로그 파일이 기록되는 로그 폴더의 UNC 경로를 입력합니다.
- **PackageAccessDomain**: AzureDTExec을 호출할 때 지정된 UNC 경로에서 패키지에 액세스하기 위해 도메인 자격 증명을 입력합니다.
- **패키지액세스암호**: AzureDTExec을 호출할 때 지정된 UNC 경로에서 패키지에 액세스하려면 암호 자격 증명을 입력합니다.
- **PackageAccessUserName**: AzureDTExec을 호출할 때 지정된 UNC 경로에서 패키지에 액세스하기 위해 사용자 이름 자격 증명을 입력합니다.

패키지 및 로그 파일을 파일 시스템에 저장하거나 온-프레미스에 파일을 저장하려면 Azure-SSIS IR을 온-프레미스 네트워크에 연결된 가상 네트워크에 가입하여 패키지를 가져오고 로그 파일을 작성할 수 있습니다. 자세한 내용은 [가상 네트워크에 Azure-SSIS IR 조인을](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)참조하십시오.

일반 텍스트로 *AzureDTExec.settings* 파일에 기록된 중요한 값을 표시하지 않으려면 Base64 인코딩의 문자열로 인코딩합니다. AzureDTExec을 호출하면 모든 Base64 인코딩된 문자열이 원래 값으로 다시 디코딩됩니다. 액세스할 수 있는 계정을 제한하여 *AzureDTExec.settings* 파일을 추가로 보호할 수 있습니다.

## <a name="invoke-the-azuredtexec-utility"></a>AzureDTExec 유틸리티 호출
명령줄 프롬프트에서 AzureDTExec을 호출하고 사용 사례 시나리오의 특정 옵션에 대한 관련 값을 제공할 수 있습니다.

유틸리티가 에 `{SSMS Folder}\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn`설치됩니다. 경로는 'PATH' 환경 변수에 추가하여 어디서나 호출할 수 있습니다.

```dos
> cd "C:\Program Files (x86)\Microsoft SQL Server Management Studio 18\Common7\IDE\CommonExtensions\Microsoft\SSIS\150\Binn"
> AzureDTExec.exe  ^
  /F \\MyStorageAccount.file.core.windows.net\MyFileShare\MyPackage.dtsx  ^
  /Conf \\MyStorageAccount.file.core.windows.net\MyFileShare\MyConfig.dtsConfig  ^
  /Conn "MyConnectionManager;Data Source=MyDatabaseServer.database.windows.net;User ID=MyAdminUsername;Password=MyAdminPassword;Initial Catalog=MyDatabase"  ^
  /Set \package.variables[MyVariable].Value;MyValue  ^
  /De MyEncryptionPassword
```

AzureDTExec을 호출하는 것은 dtexec를 호출하는 것과 유사한 옵션을 제공합니다. 자세한 내용은 [dtexec Utility](https://docs.microsoft.com/sql/integration-services/packages/dtexec-utility?view=sql-server-2017)를 참조하세요. 현재 지원되는 옵션은 다음과 같습니다.

- **/F[ile]**: 파일 시스템, 파일 공유 또는 Azure 파일에 저장된 패키지를 로드합니다. 이 옵션의 값으로 .dtsx 확장자가 있는 파일 시스템, 파일 공유 또는 Azure 파일에서 패키지 파일에 대한 UNC 경로를 지정할 수 있습니다. 지정된 UNC 경로에 공백이 포함되어 있으면 전체 경로 주위에 따옴표를 배치합니다.
- **/Conf[igFile]**: 값을 추출할 구성 파일을 지정합니다. 이 옵션을 사용하면 디자인 타임에 지정된 것과 다른 패키지에 대한 런타임 구성을 설정할 수 있습니다. 다른 설정을 XML 구성 파일에 저장한 다음 패키지 실행 전에 로드할 수 있습니다. 자세한 내용은 [SSIS 패키지 구성을](https://docs.microsoft.com/sql/integration-services/packages/package-configurations?view=sql-server-2017)참조하십시오. 이 옵션의 값을 지정하려면 파일 시스템, 파일 공유 또는 dtsConfig 확장자가 있는 Azure 파일의 구성 파일에 UNC 경로를 사용합니다. 지정된 UNC 경로에 공백이 포함되어 있으면 전체 경로 주위에 따옴표를 배치합니다.
- **/Conn[ection]**: 패키지의 기존 연결 관리자에 대한 연결 문자열을 지정합니다. 이 옵션을 사용하면 패키지의 기존 연결 관리자에 대해 디자인 타임에 지정된 것과 다른 런타임 연결 문자열을 설정할 수 있습니다. 이 옵션의 값을 다음과 `connection_manager_name_or_id;connection_string [[;connection_manager_name_or_id;connection_string]...]`같이 지정합니다.
- **/set**: 패키지의 매개 변수, 변수, 속성, 컨테이너, 로그 공급자, Foreach 열거자 또는 연결의 구성을 재정의합니다. 이 옵션은 여러 번 지정할 수 있습니다. 이 옵션의 값을 다음과 `property_path;value`같이 지정합니다. 예를 들어 `\package.variables[counter].Value;1` `counter` 변수 값을 1로 재정의합니다. **패키지 구성** 마법사를 사용하여 재정의하려는 패키지의 `property_path` 항목에 대한 값을 찾고 복사하고 붙여넣기할 수 있습니다. 자세한 내용은 [패키지 구성 마법사를](https://docs.microsoft.com/sql/integration-services/package-configuration-wizard-ui-reference?view=sql-server-2014)참조하십시오.
- **/De[crypt]**: **암호화AllWithPassword 암호화비밀비밀보호**/수준으로 구성된 패키지의 암호 해독 암호를**설정합니다.**

> [!NOTE]
> 옵션에 대한 새 값을 사용 하 고 AzureDTExec을 호출 하는 옵션 **/De[cript]** 를 제외 한 새 파이프라인을 생성 합니다.

## <a name="next-steps"></a>다음 단계

SSIS 패키지 실행 활동이 있는 고유한 파이프라인이 생성되고 AzureDTExec을 호출할 때 실행되면 데이터 팩터리 포털에서 모니터링할 수 있습니다. 데이터 팩터리를 사용하여 오케스트레이션/예약하려는 경우 데이터 팩터리 트리거를 할당할 수도 있습니다. 자세한 내용은 [SSIS 패키지 실행을 데이터 팩터리 활동으로](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)참조하십시오.

> [!WARNING]
> 생성된 파이프라인은 AzureDTExec에서만 사용할 것으로 예상됩니다. 해당 속성 또는 매개 변수는 나중에 변경될 수 있으므로 다른 용도로 수정하거나 다시 사용하지 마십시오. 수정하면 AzureDTExec이 중단될 수 있습니다. 이 경우 파이프라인을 삭제합니다. AzureDTExec다음에 호출될 때 새 파이프라인을 생성합니다.