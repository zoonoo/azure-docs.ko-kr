---
title: Azure-SSIS 통합 런타임에 대한 설정 사용자 지정
description: 이 문서에서는 Azure-SSIS 통합 런타임에 사용자 지정 설정 인터페이스를 사용하여 추가 구성 요소를 설치하거나 설정을 변경하는 방법에 대해 설명합니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 04/15/2020
ms.openlocfilehash: 3aabf68b6763e5815a03021cb02683d509c26190
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415064"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임에 대한 설정 사용자 지정

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure-SQL 서버 통합 서비스 통합 런타임(Azure-SSIS IR)에 대한 사용자 지정 설정은 Azure-SSIS IR의 설정 또는 재구성 중에 고유한 단계를 추가하기 위한 인터페이스를 제공합니다. 

사용자 지정 설정을 사용하여 기본 운영 구성 또는 환경을 변경하여 추가 Windows 서비스를 시작하거나, 파일 공유에 대한 액세스 자격 증명을 유지하거나, 강력한 암호화/보안 네트워크 프로토콜(TLS 1.2)을 사용할 수 있습니다. 또는 Azure-SSIS IR의 각 노드에 어셈블리, 드라이버 또는 확장과 같은 추가 구성 요소를 설치할 수 있습니다.

Azure-SSIS IR에 대한 사용자 지정 설정은 다음 두 가지 방법 중 하나에서 수행할 수 있습니다. 
* **스크립트 없이 사용자 지정 설정 표현**: 일부 일반적인 시스템 구성 및 Windows 명령을 실행하거나 스크립트를 사용하지 않고 인기 있거나 권장되는 추가 구성 요소를 설치합니다.
* **스크립트를 사용하여 표준 사용자 지정 설정:** 스크립트와 관련 파일을 준비하고 Azure 저장소 계정의 Blob 컨테이너에 모두 함께 업로드합니다. 그런 다음 Azure-SSIS IR을 설정하거나 다시 구성할 때 컨테이너에 대해 SAS(공유 액세스 서명) 균일 리소스 식별자(URI)를 제공합니다. Azure-SSIS IR의 각 노드는 컨테이너에서 스크립트 및 관련 파일을 다운로드하고 높은 권한으로 사용자 지정 설정을 실행합니다. 사용자 지정 설정이 완료되면 각 노드는 실행 및 기타 로그의 표준 출력을 컨테이너에 업로드합니다.

무료, 라이선스가 부여되지 않은 구성 요소 및 익스프레스 및 표준 사용자 지정 설정을 통해 유료 라이선스 구성 요소를 모두 설치할 수 있습니다. 독립 소프트웨어 공급업체(ISV)인 경우 [Azure-SSIS IR에 대해 유료 또는 사용이 허가된 구성 요소 개발을](how-to-develop-azure-ssis-ir-licensed-components.md)참조하십시오.

> [!IMPORTANT]
> 향후 향상된 기능을 활용하려면 사용자 지정 설정을 사용하여 Azure-SSIS IR에 v3 또는 이후 의 노드 시리즈를 사용하는 것이 좋습니다.

## <a name="current-limitations"></a>현재 제한 사항

다음 제한 사항은 표준 사용자 지정 설정에만 적용됩니다.

- 스크립트에서 *gacutil.exe를* 사용하여 GAC(글로벌 어셈블리 캐시)에 어셈블리를 설치하려면 사용자 지정 설정의 일부로 *gacutil.exe를* 제공해야 합니다. 또는 "지침" 섹션의 후반부에 설명된 *공개 미리 보기* 컨테이너에 제공된 복사본을 사용할 수 있습니다.

- 스크립트에서 하위 폴더를 참조하려는 경우 *msiexec.exe는* 루트 `.\` 폴더를 참조하는 표기와도 적용되지 않습니다. `msiexec /i "MySubfolder\MyInstallerx64.msi" ...` 을 대신 명령을 `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...`사용합니다.

- 관리 공유 또는 Windows에서 자동으로 만든 숨겨진 네트워크 공유는 현재 Azure-SSIS IR에서 지원되지 않습니다.

- IBM iSeries 액세스 ODBC 드라이버는 Azure-SSIS IR에서 지원되지 않습니다. 사용자 지정 설정 중에 설치 오류가 표시될 수 있습니다. 이 경우 IBM 지원에 문의하여 도움을 받으려면

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR을 사용자 지정하려면 다음 항목이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/)

- [Azure-SSIS IR 프로 비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure 저장소 계정](https://azure.microsoft.com/services/storage/). 빠른 사용자 지정 설정에 는 필요하지 않습니다. 표준 사용자 지정 설정의 경우 사용자 지정 설정 스크립트와 관련 파일을 Blob 컨테이너에 업로드하고 저장합니다. 사용자 지정 설정 프로세스에서는 실행 로그도 같은 Blob 컨테이너에 업로드합니다.

## <a name="instructions"></a>Instructions

1. PowerShell을 사용하여 Azure-SSIS IR을 설정하거나 다시 구성하려면 [Azure PowerShell](/powershell/azure/install-az-ps)을 다운로드하여 설치합니다. 익스프레스 사용자 지정 설정의 경우 4단계로 건너뜁니다.

1. 사용자 지정 설정 스크립트 및 관련 파일(예: .bat, .cmd, .exe, .dll, .msi, 또는 .ps1 파일)을 준비합니다.

   * 사용자 지정 설정의 진입점인 *main.cmd라는*스크립트 파일이 있어야 합니다.  
   * 스크립트를 자동으로 실행할 수 있도록 하려면 먼저 로컬 컴퓨터에서 테스트하는 것이 좋습니다.  
   * 컨테이너에 업로드할 다른 도구(예: *msiexec.exe)에서*생성된 추가 로그를 원하는 경우 미리 정의된 `CUSTOM_SETUP_SCRIPT_LOG_DIR`환경 변수를 스크립트의 로그 폴더(예: *msiexec /i xxx.msi /조용한/lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log)로*지정합니다.

1. [Azure 저장소 탐색기를](https://storageexplorer.com/)다운로드, 설치 및 엽니다. 이렇게 하려면 다음을 수행합니다.

   a. **아래(로컬 및 연결)** 에서 **저장소 계정을**마우스 오른쪽 단추로 클릭한 다음 Azure **저장소에 연결 을**선택합니다.

      ![Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. **저장소 계정 이름 및 키 사용을**선택한 다음 **다음**을 선택합니다.

      ![스토리지 계정 이름 및 키 사용](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   다. Azure 저장소 계정 이름 및 키를 입력하고 **다음**을 선택한 다음 **연결을**선택합니다.

      ![저장소 계정 이름 및 키 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 연결된 Azure 저장소 계정에서 **Blob 컨테이너를**마우스 오른쪽 단추로 클릭하고 **Blob 컨테이너 만들기를**선택하고 새 컨테이너의 이름을 지정합니다.

      ![Blob 컨테이너 만들기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 새 컨테이너를 선택하고 사용자 지정 설정 스크립트 및 관련 파일을 업로드합니다. 폴더가 아닌 컨테이너의 최상위 수준에 *main.cmd를* 업로드해야 합니다. 또한 컨테이너에 필요한 사용자 지정 설정 파일만 포함되어 있는지 확인하여 나중에 Azure-SSIS IR에 다운로드하는 데 시간이 오래 걸리지 않도록 합니다. 사용자 지정 설정의 최대 지속 시간은 현재 시간 초과되기 45분 전에 설정됩니다. 여기에는 컨테이너에서 모든 파일을 다운로드하고 Azure-SSIS IR에 설치하는 시간이 포함됩니다. 설치에 더 많은 시간이 필요한 경우 지원 티켓을 올립니다.

      ![Blob 컨테이너에 파일 업로드](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 컨테이너를 마우스 오른쪽 단추로 클릭한 다음 **공유 액세스 서명 받기를**선택합니다.

      ![컨테이너에 대한 공유 액세스 서명 가져오기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 충분히 긴 만료 시간과 읽기/쓰기/목록 사용 권한이 있는 컨테이너에 대한 SAS URI를 만듭니다. Azure-SSIS IR의 노드가 다시 이미지화되거나 다시 시작될 때마다 사용자 지정 설정 스크립트 및 관련 파일을 다운로드하고 실행하려면 SAS URI가 필요합니다. 설정 실행 로그를 업로드하려면 쓰기 권한이 필요합니다.

      > [!IMPORTANT]
      > 특히 이 기간 동안 Azure-SSIS IR을 정기적으로 중지하고 시작하는 경우 SAS URI가 만료되지 않고 Azure-SSIS IR의 전체 수명 주기 동안 생성에서 삭제에 이르기까지 사용자 지정 설정 리소스를 항상 사용할 수 있는지 확인합니다.

      ![컨테이너에 대한 공유 액세스 서명 생성](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 컨테이너의 SAS URI를 복사하여 저장합니다.

      ![공유 액세스 서명 복사 및 저장](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. Azure-SSIS IR을 데이터 팩터리 UI로 설정하거나 다시 구성할 때 **통합 런타임 설치** 창의 **고급 설정** 섹션에서 추가 시스템 구성/구성 요소 설치 확인란을 **사용하여 Azure-SSIS 통합 런타임 사용자 지정을** 선택하여 사용자 지정 설정을 추가하거나 제거할 수 있습니다. 

   표준 사용자 지정 설정을 추가하려면 사용자 지정 설치 컨테이너 SAS URI 상자에 컨테이너의 **SAS URI를** 입력합니다. 
   
   익스프레스 사용자 지정 설정을 추가하려면 **새로 를** 선택하여 Express 사용자 지정 **설정 창추가** 를 연 다음 **Express 사용자 지정 설정 유형** 드롭다운 목록에서 유형을 선택합니다.

   * **cmdkey 명령** 유형을 선택하면 대상 컴퓨터 이름 또는 도메인 이름, 계정 이름 또는 사용자 이름, 계정 키 또는 암호를 **/Add**, **/User**및 **/Pass** 상자에 입력하여 Azure-SSIS IR에서 파일 공유 또는 Azure Files 공유에 대한 액세스 자격 증명을 지속할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 명령을 실행하는 것과 유사합니다.
   
   * **환경 변수** 추가 유형을 선택하면 변수 이름 및 변수 **값** 상자에 환경 변수 이름과 값을 입력하여 Azure-SSIS IR에서 실행되는 패키지에 사용할 Windows **환경** 변수를 추가할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [집합](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) 명령을 실행하는 것과 유사합니다.

   * **설치 라이센스가 부여된 구성 요소** 유형을 선택하는 경우 구성 요소 **이름** 드롭다운 목록에서 ISV 파트너의 통합 구성 요소를 선택할 수 있습니다.

     * **SentryOne의 작업 팩토리** 구성 요소를 선택하면 **라이센스 키** 상자에 구입한 제품 라이센스 키를 입력하여 Azure-SSIS IR에서 SentryOne의 [작업 팩토리](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 구성 요소 제품군을 설치할 수 있습니다. 현재 통합 버전은 **2019.4.3.**

     * **oh22의 HEDDA를 선택하면. IO** 구성 요소, [HEDDA를 설치할 수 있습니다. ](https://hedda.io/ssis-component/)서비스를 구입한 후 Azure-SSIS IR의 oh22에서 IO 데이터 품질/정리 구성 요소입니다. 현재 통합 버전은 **1.0.13입니다.**

     * **oh22의 SQLPhonetics.NET** 구성 요소를 선택하면 **라이센스 키** 상자에 구입한 제품 라이센스 키를 입력하여 Azure-SSIS IR에 oh22의 [데이터](https://sqlphonetics.oh22.is/sqlphonetics-net-for-microsoft-ssis/) 품질/일치 SQLPhonetics.NET 구성 요소를 설치할 수 있습니다. 현재 통합 버전은 **1.0.43입니다.**

     * **KingswaySoft의 SSIS 통합 도구 키트** 구성 요소를 선택하면 Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce 마케팅 클라우드 등과 같은 CRM/ERP/마케팅/공동 작업 용 커넥터의 [SSIS 통합 도구 키트](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) 제품군을 Azure-SSIS IR에서 KingswaySoft에서 설치할 수 있습니다. **License key** 현재 통합 버전은 **2019.2.**

     * **KingswaySoft의 SSIS 생산성 팩** 구성 요소를 선택하면 **라이선스 키** 상자에 구입한 제품 라이센스 키를 입력하여 KingswaySoft의 [SSIS 생산성 팩](https://www.kingswaysoft.com/products/ssis-productivity-pack) 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **10.0입니다.**

     * **테오발트 소프트웨어의 Xtract IS** 구성 요소를 선택하면 Azure-SSIS IR의 테오발드 소프트웨어에서 SAP 시스템용 커넥터Xtract(ERP, S/4HANA, BW)를 설치할 수 & 해당 소프트웨어에서 구입한 제품 라이센스 파일을 **라이센스 파일** 상자로 삭제/업로드할 수 있습니다. [Xtract IS](https://theobald-software.com/en/xtract-is/) 현재 통합 버전은 **6.1.1.3입니다.**

   추가된 익스프레스 맞춤 설정이 **고급 설정** 섹션에 표시됩니다. 제거하려면 해당 확인란을 선택한 다음 **삭제를**선택합니다.

   ![사용자 지정 설정이 있는 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

1. PowerShell을 통해 Azure-SSIS IR을 설정하거나 다시 구성할 때 `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS IR을 시작하기 전에 cmdlet을 실행하여 사용자 지정 설정을 추가하거나 제거할 수 있습니다.
   
   ```powershell
   $ResourceGroupName = "[your Azure resource group name]"
   $DataFactoryName = "[your data factory name]"
   $AzureSSISName = "[your Azure-SSIS IR name]"
   # Custom setup info: Standard/express custom setups
   $SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
   $ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script

   # Add custom setup parameters if you use standard/express custom setups
   if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
   {
       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -SetupScriptContainerSasUri $SetupScriptContainerSasUri
   }
   if(![string]::IsNullOrEmpty($ExpressCustomSetup))
   {
       if($ExpressCustomSetup -eq "RunCmdkey")
       {
           $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
           $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
           $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
       }
       if($ExpressCustomSetup -eq "SetEnvironmentVariable")
       {
           $variableName = "YourVariableName"
           $variableValue = "YourVariableValue"
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
       }
       if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
       {
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
       {
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }    
       if($ExpressCustomSetup -eq "Theobald.XtractIS")
       {
           $jsonData = Get-Content -Raw -Path YourLicenseFile.json
           $jsonData = $jsonData -replace '\s',''
           $jsonData = $jsonData.replace('"','\"')
           $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
           $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
       }
       # Create an array of one or more express custom setups
       $setups = New-Object System.Collections.ArrayList
       $setups.Add($setup)

       Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
           -DataFactoryName $DataFactoryName `
           -Name $AzureSSISName `
           -ExpressCustomSetup $setups
   }
   Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
       -DataFactoryName $DataFactoryName `
       -Name $AzureSSISName `
       -Force
   ```
   
   표준 사용자 지정 설정이 완료되고 Azure-SSIS IR이 시작되면 저장소 컨테이너의 *main.cmd.log* 폴더에서 *main.cmd* 및 기타 실행 로그의 표준 출력을 찾을 수 있습니다.

1. 표준 사용자 지정 설정의 일부 샘플을 보려면 Azure 저장소 탐색기를 사용하여 공용 미리 보기 컨테이너에 연결합니다.

   a. **(로컬 및 연결)** 에서 **스토리지 계정**을 마우스 오른쪽 단추로 클릭하고 **Azure Storage에 연결**을 선택한 다음, **연결 문자열 또는 공유 액세스 서명 URI**를 선택하고 **다음**을 선택합니다.

      ![공유 액세스 서명을 사용하여 Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. **SAS URI 사용을** 선택한 다음 **URI** 상자에 다음 SAS URI를 입력합니다.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![컨테이너에 공유 액세스 서명 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   다. **다음**을 선택한 다음 **연결을**선택합니다.

   d. 왼쪽 창에서 연결된 공개 **미리 보기** 컨테이너를 선택한 다음 *CustomSetupScript* 폴더를 두 번 클릭합니다. 이 폴더에는 다음 항목이 있습니다.

      * Azure-SSIS IR의 각 노드에 기본 작업을 설치하기 위한 사용자 지정 설정이 포함된 *샘플* 폴더입니다. 이 작업은 몇 초 동안 대기하면서 아무 것도 수행하지 않습니다. 폴더에는 또한 *gacutil* 폴더가 포함되어 있으며, 전체 내용 *(gacutil.exe,* *gacutil.exe.config*및 *1033\gacutlrc.dll)을*컨테이너에 있는 것처럼 복사할 수 있습니다.

      * 실제 *사용자 시나리오에서* 여러 사용자 지정 설정 샘플을 포함 하는 UserScenario 폴더입니다.

        ![공개 미리 보기 컨테이너의 콘텐츠](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. *UserScenario* 폴더를 두 번 클릭하여 다음 항목을 찾습니다.

      * *Azure-SSIS* IR의 각 노드에서 사용자 지정 구성 요소에 필요할 수 있는 .NET Framework의 이전 버전을 설치하는 사용자 지정 설정*스크립트(main.cmd)가*포함된 .NET FRAMEWORK 3.5 폴더입니다.

      * Azure-SSIS IR의 각 노드에 대량 복사*프로그램(bcp)을*포함하여 SQL Server 명령줄*유틸리티(MsSqlCmdLnUtils.msi)를*설치하기 위한 사용자 지정 설정*스크립트(main.cmd)가*포함된 *BCP* 폴더입니다.

      * Azure-SSIS IR의 각 노드에서 Excel 파일을 동적으로 읽고 쓸 수 있는 C# 어셈블리 및 라이브러리를 설치하는 사용자 지정 설정 스크립트(main.cmd)가 포함된 EXCEL 폴더입니다. *EXCEL* *main.cmd* 
      
        먼저 [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) 및 [*DocumentFormat.OpenXml.dll을*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)다운로드한 다음 *main.cmd와* 함께 컨테이너에 업로드합니다. 또는 표준 Excel 연결 관리자, Excel 원본 및 Excel 대상만 사용하려는 경우 필요한 액세스 재배포가 이미 Azure-SSIS IR에 사전 설치되어 있으므로 사용자 지정 설정이 필요하지 않습니다.
      
      * *Azure-SSIS* IR의 각 노드에 MySQL ODBC 드라이버를 설치하는 사용자 지정 설정*스크립트(main.cmd)가*포함된 MYSQL ODBC 폴더입니다. 이 설정을 사용하면 ODBC 연결 관리자, 소스 및 대상을 사용하여 MySQL 서버에 연결할 수 있습니다. 
     
        먼저 [MySQL ODBC 드라이버 설치 프로그램의 최신 64비트 및 32비트 버전을 다운로드한](https://dev.mysql.com/downloads/connector/odbc/) 다음(예: *mysql-커넥터-odbc-8.0.13-winx64.msi* 및 *mysql-커넥터-odbc-8.0.13-win32.msi)을*모두 *컨테이너에 main.cmd와* 함께 업로드합니다.

      * 사용자 지정 설치*스크립트(main.cmd)와*자동 설치 구성*파일(client.rsp)이*포함된 *ORACLE ENTERPRISE* 폴더는 Azure-SSIS IR 엔터프라이즈 에디션의 각 노드에 Oracle 커넥터 및 OCI 드라이버를 설치합니다. 이 설정을 사용하면 Oracle 연결 관리자, 소스 및 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저, 오라클에 대한 마이크로 소프트 커넥터 v5.0을 다운로드 *(AttunitySISIsOraAdaptersSetup.msi* 및 *AttunitySIsSIsAdaptersSetup64.msi)* [마이크로 소프트 다운로드 센터와](https://www.microsoft.com/en-us/download/details.aspx?id=55179) [오라클에서](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)최신 오라클 클라이언트 (예를 들어, *winx64_12102_client.zip)* 다음, 다음 *메인.cmd* 및 *client.rsp와* 함께 그들을 모두 업로드 컨테이너에. TNS를 사용하여 오라클에 연결하는 경우 *tnsnames.ora를*다운로드하고 편집한 다음 컨테이너에 업로드해야 설치 중에 Oracle 설치 폴더에 복사할 수 있습니다.

      * *Oracle STANDARD ADO.NET* 폴더로 Azure-SSIS IR의 각 노드에 Oracle ODP.NET 드라이버를 설치하는 사용자 지정 설정 스크립트(main.cmd)가 포함되어 있습니다.*main.cmd* 이 설정을 사용하면 ADO.NET 연결 관리자, 소스 및 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 [최신 Oracle ODP.NET](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html) 드라이버(예: *ODP.NET_Managed_ODAC122cR1.zip)를*다운로드한 다음 *main.cmd와* 함께 컨테이너에 업로드합니다.
       
      * Oracle ODBC 드라이버를 설치하고 *Azure-SSIS IR의* 각 노드에 데이터 원본 이름(DSN)을 구성하는 사용자 지정 설정 스크립트(main.cmd)가 포함된 ORACLE STANDARD ODBC 폴더입니다.*main.cmd* 이 설정을 사용하면 ODBC 데이터 원본 유형이 있는 ODBC 연결 관리자, 소스 및 대상 또는 전원 쿼리 연결 관리자 및 소스를 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 최신 오라클 인스턴트 클라이언트(기본 패키지 또는 기본 라이트 패키지) 및 ODBC 패키지를 다운로드한 다음 *main.cmd와* 함께 컨테이너에 모두 업로드합니다.
        * [다운로드 64 비트 패키지](https://www.oracle.com/technetwork/topics/winx64soft-089540.html) (기본 패키지 : *인스턴트 클라이언트 - 기본 - 윈도우.x64-18.3.0.0.0.0dbru.zip;* 기본 라이트 패키지 : *인스턴트 클라이언트 - 기본 라이트 - 윈도우.x64-18.0.0.0.0.0dbru.zip;* ODBC 패키지 : *인스턴트 클라이언트 - odbc-windows.x64-18.3.0.0.0.0dbru.zip*) 
        * [32비트 패키지 다운로드(기본](https://www.oracle.com/technetwork/topics/winsoft-085727.html) 패키지: *인스턴트 클라이언트 기본 nt-18.3.0.0.0.0dbru.zip;* 기본 라이트 패키지 : *인스턴트 클라이언트 - 기본 라이트 -nt-18.3.0.0.0.0dbru.zip;* ODBC 패키지 : *인스턴트 클라이언트 - odbc-nt-18.3.0.0.0.0dbru.zip*)

      * Azure-SSIS IR의 각 노드에 Oracle OLEDB 드라이버를 설치하는 사용자 지정 설정*스크립트(main.cmd)가*포함된 *ORACLE STANDARD OLEDB* 폴더입니다. 이 설정을 사용하면 OLEDB 연결 관리자, 소스 및 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
     
        먼저 [최신 Oracle OLEDB](https://www.oracle.com/partners/campaign/index-090165.html) 드라이버(예: *ODAC122010Xcopy_x64.zip)를*다운로드한 다음 *main.cmd와* 함께 컨테이너에 업로드합니다.

      * *Azure-SSIS* IR의 각 노드에 PostgreSQL ODBC 드라이버를 설치하는 사용자 지정 설정*스크립트(main.cmd)가*포함된 POSTGRESQL ODBC 폴더입니다. 이 설정을 사용하면 ODBC 연결 관리자, 소스 및 대상을 사용하여 PostgreSQL 서버에 연결할 수 있습니다. 
     
        먼저 [PostgreSQL ODBC 드라이버 설치 프로그램의 최신 64비트 및 32비트](https://www.postgresql.org/ftp/odbc/versions/msi/) 버전(예: *psqlodbc_x64.msi* 및 *psqlodbc_x86.msi)을*다운로드한 다음 *main.cmd와* 함께 컨테이너에 업로드합니다.

      * *Azure-SSIS IR* 엔터프라이즈 에디션의 각 노드에 SAP .NET 커넥터 어셈블리(librfc32.dll)를 설치하기 위한 사용자 지정 설정 스크립트(main.cmd)가 포함된 SAP BW 폴더입니다.*main.cmd**librfc32.dll* 이 설정을 사용하면 BW(SAP 비즈니스 웨어하우스) 연결 관리자, 소스 및 대상을 사용하여 SAP BW 서버에 연결할 수 있습니다. 
      
        먼저 SAP 설치 폴더에서 64비트 또는 32비트 버전의 *librfc32.dll을* *main.cmd와* 함께 컨테이너에 업로드합니다. 그런 다음 스크립트는 설정 중에 SAP 어셈블리를 *%windir%\SysWow64* 또는 *%windir%\System32* 폴더로 복사합니다.

      * Azure-SSIS IR의 각 노드에 Azure PowerShell을 설치하는 사용자 지정 설정*스크립트(main.cmd)가*포함된 *STORAGE* 폴더입니다. 이 설정을 사용하면 PowerShell 스크립트를 실행하여 [Azure 저장소 계정을 조작하는](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)SSIS 패키지를 배포하고 실행할 수 있습니다. 
      
        *main.cmd,* 샘플 *AzurePowerShell.msi(또는* 최신 버전 사용) 및 *storage.ps1을* 컨테이너에 복사합니다. 패키지의 템플릿으로 *PowerShell.dtsx를* 사용합니다. 패키지 템플릿은 *storage.ps1을* 수정 가능한 PowerShell 스크립트로 다운로드하는 [Azure Blob 다운로드 작업과](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)각 노드에서 스크립트를 실행하는 [실행 프로세스 작업을](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)결합합니다.

      * 사용자 지정 설정*스크립트(main.cmd),* 관련*파일(install.cmd)* 및 설치 프로그램*패키지(.msi)가*포함된 *TERADATA* 폴더입니다. 이러한 파일은 Azure-SSIS IR 엔터프라이즈 에디션의 각 노드에 Teradata 커넥터, 테라데이타 병렬 전송기(TPT) API 및 ODBC 드라이버를 설치합니다. 이 설정을 사용하면 Teradata 연결 관리자, 소스 및 대상을 사용하여 Teradata 서버에 연결할 수 있습니다. 
      
        먼저 [Teradata 도구 및 유틸리티 15.x zip](http://partnerintelligence.teradata.com) 파일(예: *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip)을*다운로드한 다음 앞서 언급한 *.cmd* 및 *.msi* 파일과 함께 컨테이너에 업로드합니다.

      * 강력한 암호화/보다 안전한 네트워크 프로토콜(TLS 1.2)을 사용하고 Azure-SSIS IR의 각 노드에서 이전 SSL/TLS 버전을 사용하지 않도록 설정하는 사용자 지정 설정*스크립트(main.cmd)가*포함된 *TLS 1.2* 폴더입니다.

      * 사용자 정의 설정 스크립트 *(main.cmd)* 및 PowerShell 파일 *(install_openjdk.ps1)를*포함하는 *ZULU OPENJDK* 폴더는 Azure-SSIS IR의 각 노드에 줄루 OpenJDK를 설치합니다. 이 설정을 사용하면 Azure Data Lake Store 및 유연한 파일 커넥터를 사용하여 ORC 및 Parquet 파일을 처리할 수 있습니다. 자세한 내용은 [통합 서비스에 대한 Azure 기능 팩을](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)참조하십시오. 
      
        먼저 [최신 Zulu OpenJDK(예:](https://www.azul.com/downloads/zulu/zulu-windows/) *zulu8.33.0.1-jdk8.0.192-win_x64.zip)를*다운로드한 다음 *main.cmd* 및 *install_openjdk.ps1과* 함께 컨테이너에 업로드합니다.

        ![사용자 시나리오 폴더의 폴더](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 이러한 사용자 지정 설정 샘플을 사용해 보십시오.
   
      데이터 팩터리 UI를 사용하여 Azure-SSIS IR을 설정하거나 다시 구성할 때 **고급 설정** 섹션의 **추가 시스템 구성/구성 요소 설치 확인란을 사용하여 Azure-SSIS 통합 런타임을 사용자 지정한** 다음 사용자 지정 설정 컨테이너 SAS URI 상자에 컨테이너의 **SAS** URI를 입력합니다.
   
      PowerShell을 사용 하 고 Azure-SSIS IR을 설정 `Set-AzDataFactoryV2IntegrationRuntime` 하거나 다시 구성 하는 경우 매개 변수에 `SetupScriptContainerSasUri` 대 한 값으로 컨테이너의 SAS URI를 사용 하 고 cmdlet을 실행 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS 통합 런타임의 엔터프라이즈 버전 설정](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS 통합 런타임에 대해 유료 또는 라이선스가 부여된 사용자 지정 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)
