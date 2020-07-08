---
title: Azure-SSIS Integration Runtime 설치 사용자 지정
description: 이 문서에서는 Azure-SSIS Integration Runtime에 대한 사용자 지정 설치 인터페이스를 사용하여 추가 구성 요소를 설치하거나 설정을 변경하는 방법을 설명합니다.
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
ms.date: 06/03/2020
ms.openlocfilehash: 576861265771977f7e13140dd595f47bf556e585
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84331902"
---
# <a name="customize-the-setup-for-an-azure-ssis-integration-runtime"></a>Azure-SSIS Integration Runtime 설치 사용자 지정

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory (ADF)에서 Azure SQL Server Integration Services (SSIS) Integration Runtime (IR)의 사용자 지정 설정은 Azure-SSIS IR를 프로 비전 하거나 재구성 하는 동안 고유한 단계를 추가 하기 위한 인터페이스를 제공 합니다. 

예를 들어 사용자 지정 설치를 사용하면 기본 운영 구성 또는 환경을 변경하여 추가 Windows 서비스를 시작하거나, 파일 공유에 대한 액세스 자격 증명을 유지하거나, 강력한 암호화/더 안전한 네트워크 프로토콜(TLS 1.2)을 사용할 수 있습니다. 또는 Azure-SSIS IR의 각 노드에 어셈블리, 드라이버 또는 확장과 같은 추가 사용자 지정/타사 구성 요소를 설치할 수 있습니다. 기본 제공/미리 설치된 구성 요소에 대한 자세한 내용은 [Azure-SSIS IR의 기본 제공/미리 설치된 구성 요소](https://docs.microsoft.com/azure/data-factory/built-in-preinstalled-components-ssis-integration-runtime)를 참조하세요.

다음 두 가지 방법 중 하나를 사용하여 Azure-SSIS IR에서 사용자 지정 설치를 수행할 수 있습니다. 
* **스크립트를 사용하는 표준 사용자 지정 설치**: 스크립트 및 관련 파일을 준비하고, Azure 스토리지 계정의 Blob 컨테이너에 모두 업로드합니다. 그런 다음, Azure-SSIS IR을 설치하거나 재구성할 때 컨테이너에 대한 SAS(공유 액세스 서명) URI(Uniform Resource Identifier)를 제공합니다. 그러면 각 Azure-SSIS IR의 각 노드에서 컨테이너로부터 스크립트 및 관련 파일을 다운로드하고, 관리자 권한으로 사용자 지정 설치를 실행합니다. 사용자 지정 설치가 완료되면 각 노드에서 실행에 대한 표준 출력 및 기타 로그를 컨테이너에 업로드합니다.
* **스크립트를 사용하지 않는 기본 사용자 지정 설치**: 일부 일반적인 시스템 구성 및 Windows 명령을 실행하거나 스크립트를 사용하지 않고 자주 사용하거나 추천되는 추가 구성 요소를 설치합니다.

표준 및 express 사용자 지정 설치를 사용 하 여 사용이 허가 되지 않은 무료 구성 요소와 유료 라이선스 구성 요소를 모두 설치할 수 있습니다. ISV (독립 소프트웨어 공급 업체) 인 경우 [Azure-SSIS IR에 대 한 유료 또는 라이선스 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)을 참조 하세요.

> [!IMPORTANT]
> 향후 향상된 기능을 활용하려면 사용자 지정 설치에서 v3 이상 시리즈의 노드를 Azure-SSIS IR에 사용하는 것이 좋습니다.

## <a name="current-limitations"></a>현재 제한 사항

표준 사용자 지정 설치에만 적용되는 제한 사항은 다음과 같습니다.

- 스크립트에서 *gacutil.exe*를 사용하여 어셈블리를 GAC(글로벌 어셈블리 캐시)에 설치하려면 사용자 지정 설치의 일부로 *gacutil.exe*를 제공해야 합니다. 또는 "지침" 섹션의 뒷부분에서 설명하는 *공개 미리 보기* 컨테이너에 제공된 복사본을 사용할 수 있습니다.

- 스크립트에서 하위 폴더를 참조하려는 경우 *msiexec.exe*에서 루프 폴더를 참조하는 `.\` 표기법을 지원하지 않습니다. `msiexec /i ".\MySubfolder\MyInstallerx64.msi" ...` 대신 `msiexec /i "MySubfolder\MyInstallerx64.msi" ...`와 같은 명령을 사용합니다.

- 관리 공유 또는 Windows에서 자동으로 만드는 숨겨진 네트워크 공유는 현재 Azure-SSIS IR에서 지원되지 않습니다.

- IBM iSeries Access ODBC 드라이버는 Azure-SSIS IR에서 지원되지 않습니다. 사용자 지정 설치 중에 설치 오류가 표시될 수 있습니다. 이 경우 IBM 지원에 문의하여 지원을 요청하세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure-SSIS IR을 사용자 지정하려면 다음 항목이 필요합니다.

- [Azure 구독](https://azure.microsoft.com/)

- [Azure-SSIS IR 프로 비전](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)

- [Azure Storage 계정](https://azure.microsoft.com/services/storage/) 기본 사용자 지정 설치에는 필요하지 않습니다. 표준 사용자 지정 설치의 경우 사용자 지정 설치 스크립트 및 관련 파일을 Blob 컨테이너에 업로드하고 저장합니다. 사용자 지정 설정 프로세스에서는 실행 로그도 같은 Blob 컨테이너에 업로드합니다.

## <a name="instructions"></a>Instructions

ADF UI에 대 한 사용자 지정 설정으로 Azure-SSIS IR를 프로 비전 하거나 다시 구성할 수 있습니다. PowerShell을 사용 하 여 동일한 작업을 수행 하려는 경우 [Azure PowerShell](/powershell/azure/install-az-ps)를 다운로드 하 여 설치 합니다.

### <a name="standard-custom-setup"></a>표준 사용자 지정 설치

표준 사용자 지정 프로그램을 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하려면 다음 단계를 완료 합니다.

1. 사용자 지정 설정 스크립트 및 관련 파일(예: .bat, .cmd, .exe, .dll, .msi, 또는 .ps1 파일)을 준비합니다.

   * 사용자 지정 설치의 진입점인 *main.cmd*라는 스크립트 파일이 있어야 합니다.  
   * 스크립트가 자동으로 실행될 수 있도록 하려면 먼저 로컬 컴퓨터에서 테스트하는 것이 좋습니다.  
   * 다른 도구(예: *msiexec.exe*)에서 생성한 추가 로그가 컨테이너에 업로드되도록 하려면 미리 정의된 `CUSTOM_SETUP_SCRIPT_LOG_DIR` 환경 변수를 스크립트의 로그 폴더로 지정합니다(예: *msiexec /i xxx.msi /quiet /lv %CUSTOM_SETUP_SCRIPT_LOG_DIR%\install.log*).

1. [Azure Storage Explorer](https://storageexplorer.com/)를 다운로드하여 설치하고 엽니다.

   a. **(로컬 및 연결됨)** 아래에서 마우스 오른쪽 단추로 **스토리지 계정**을 클릭한 다음, **Azure Storage에 연결**을 선택합니다.

      ![Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image1.png)

   b. **스토리지 계정 이름 및 키 사용**, **다음**을 차례로 선택합니다.

      ![스토리지 계정 이름 및 키 사용](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image2.png)

   다. Azure 스토리지 계정 이름과 키를 입력하고, **다음**을 선택하고, **연결**을 선택합니다.

      ![스토리지 계정 이름 및 키 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image3.png)

   d. 연결된 Azure 스토리지 계정 아래에서 마우스 오른쪽 단추로 **Blob 컨테이너**를 클릭하고, **Blob 컨테이너 만들기**를 선택하고, 새 컨테이너의 이름을 지정합니다.

      ![Blob 컨테이너 만들기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image4.png)

   e. 새 컨테이너를 선택하고, 사용자 지정 설치 스크립트 및 관련 파일을 업로드합니다. *main.cmd*는 폴더가 아니라 컨테이너의 최상위 수준에 업로드해야 합니다. 또한 컨테이너에 필요한 사용자 지정 설치 파일만 포함되어 있으므로 나중에 Azure-SSIS IR에 다운로드하는 데 시간이 오래 걸리지 않습니다. 사용자 지정 설치의 최대 기간은 현재 45분 이내로 설정되어 있습니다. 여기에는 컨테이너에서 모든 파일을 다운로드하여 Azure-SSIS IR에 설치하는 시간이 포함됩니다. 설치에 더 많은 시간이 필요한 경우 지원 티켓을 제출합니다.

      ![Blob 컨테이너에 파일 업로드](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image5.png)

   f. 마우스 오른쪽 단추로 컨테이너를 클릭한 다음, **공유 액세스 서명 가져오기**를 선택합니다.

      ![컨테이너에 대한 공유 액세스 서명 가져오기](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image6.png)

   g. 충분히 긴 만료 시간 및 읽기/쓰기/목록 권한을 사용하여 컨테이너에 대한 SAS URI를 만듭니다. Azure-SSIS IR 노드가 이미지로 다시 설치되거나 다시 시작할 때마다 사용자 지정 설치 스크립트 및 관련 파일을 다운로드하여 실행하려면 SAS URI가 필요합니다. 설정 실행 로그를 업로드하려면 쓰기 권한이 필요합니다.

      > [!IMPORTANT]
      > 특히 이 기간 동안 정기적으로 Azure-SSIS IR을 중지하고 시작하는 경우 SAS URI가 만료되지 않고 만들기에서 삭제까지의 Azure-SSIS IR의 전체 수명 주기 동안 사용자 지정 설치 리소스가 항상 사용할 수 있는지 확인합니다.

      ![컨테이너에 대한 공유 액세스 서명 생성](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image7.png)

   h. 컨테이너의 SAS URI를 복사하여 저장합니다.

      ![공유 액세스 서명 복사 및 저장](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image8.png)

1. ADF UI에서 Azure-SSIS IR를 프로 비전 하거나 다시 구성할 때 **Integration Runtime 설정** 창의 **고급 설정** 페이지에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자** 지정 확인란을 선택 하 고 **사용자 지정 설치 컨테이너 SAS uri** 상자에 컨테이너의 SAS uri를 입력 합니다.

   ![사용자 지정 설치가 포함된 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)

### <a name="express-custom-setup"></a>빠른 사용자 지정 설치

Express 사용자 지정을 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하려면 다음 단계를 완료 합니다.

1. ADF UI에서 Azure-SSIS IR를 프로 비전 하거나 다시 구성할 때 **Integration Runtime 설정** 창의 **고급 설정** 페이지에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자 지정** 확인란을 선택 합니다. 

1. **새로 만들기** 를 선택 하 여 **빠른 사용자 지정 설정 추가** 창을 열고 **express 사용자 지정 설치 유형** 드롭다운 목록에서 유형을 선택 합니다.

   * **cmdkey 명령 실행** 유형을 선택하는 경우 **/Add**, **/User**, **/Pass** 상자에서 대상 컴퓨터 이름 또는 도메인 이름, 계정 이름 또는 사용자 이름 및 계정 키 또는 암호를 입력하여 Azure-SSIS IR에서 파일 공유 또는 Azure Files 공유에 대한 액세스 자격 증명을 유지할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [cmdkey](https://docs.microsoft.com/windows-server/administration/windows-commands/cmdkey) 명령을 실행하는 것과 비슷합니다.
   
   * **환경 변수 추가** 유형을 선택하는 경우 **변수 이름** 및 **변수 값** 상자에서 환경 변수 이름과 값을 입력하여 Azure-SSIS IR에서 실행되는 패키지에서 사용할 Windows 환경 변수를 추가할 수 있습니다. 이는 로컬 컴퓨터에서 Windows [set](https://docs.microsoft.com/windows-server/administration/windows-commands/set_1) 명령을 실행하는 것과 비슷합니다.

   * **사용 허가된 구성 요소 설치** 유형을 선택하는 경우 **구성 요소 이름** 드롭다운 목록에서 ISV 파트너의 통합 구성 요소를 선택할 수 있습니다.

     * **SentryOne의 Task Factory** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력하여 SentryOne의 [Task Factory](https://www.sentryone.com/products/task-factory/high-performance-ssis-components) 구성 요소 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **2019.4.3**입니다.

     * **oh22의 HEDDA.IO** 구성 요소를 선택하는 경우 서비스를 구매한 후 oh22의 [HEDDA.IO](https://hedda.io/ssis-component/) 데이터 품질/정리 구성 요소를 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **1.0.13**입니다.

     * **oh22의 SQLPhonetics.NET** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구입한 제품 라이선스 키를 입력하여 oh22의 [SQLPhonetics.NET](https://appsource.microsoft.com/product/web-apps/oh22.sqlphonetics-ssis) 데이터 품질/일치 구성 요소를 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **1.0.43**입니다.

     * **KingswaySoft의 SSIS Integration Toolkit** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력하여 KingswaySoft의 CRM/ERP/마케팅/협업 앱(예: Microsoft Dynamics/SharePoint/Project Server, Oracle/Salesforce Marketing Cloud 등)용 [SSIS 통합 도구 키트](https://www.kingswaysoft.com/products/ssis-integration-toolkit-for-microsoft-dynamics-365) 커넥터 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **2019.2**입니다.

     * **KingswaySoft의 SSIS Productivity Pack** 구성 요소를 선택하는 경우 **라이선스 키** 상자에서 구매한 제품 라이선스 키를 입력하여 KingswaySoft의 [SSIS Productivity Pack](https://www.kingswaysoft.com/products/ssis-productivity-pack) 구성 요소 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **10.0**입니다.

     * **Theobald Software의 Xtract IS** 구성 요소를 선택하는 경우 구매한 제품 라이선스 파일을 **라이선스 파일** 상자에 끌어서 놓거나 업로드하여 Theobald Software의 SAP 시스템(ERP, S/4HANA, BW)용 [Xtract IS](https://theobald-software.com/en/xtract-is/) 커넥터 제품군을 Azure-SSIS IR에 설치할 수 있습니다. 현재 통합 버전은 **6.1.1.3**입니다.

추가 된 빠른 사용자 지정 설치가 **고급 설정** 페이지에 표시 됩니다. 저장소를 제거하려면 해당 확인란을 선택한 다음, **삭제**를 선택합니다.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell를 사용 하 여 사용자 지정 설정과 함께 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하려면 다음 단계를 완료 합니다.

1. Azure-SSIS IR 이미 시작 되었거나 실행 중인 경우 먼저 중지 합니다.

1. 그런 다음 `Set-AzDataFactoryV2IntegrationRuntime` Azure-SSIS IR를 시작 하기 전에 cmdlet을 실행 하 여 사용자 지정 프로그램을 추가 하거나 제거할 수 있습니다.

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

1. 표준 사용자 지정 설치를 완료 하 고 Azure-SSIS IR를 시작 하면 컨테이너의 *기본 .cmd .log* 폴더에 있는 *기본 .cmd* 및 기타 실행 로그의 표준 출력을 찾을 수 있습니다.

### <a name="standard-custom-setup-samples"></a>표준 사용자 지정 설치 샘플

표준 사용자 지정의 일부 샘플을 보고 다시 사용 하려면 다음 단계를 완료 합니다.

1. Azure Storage 탐색기를 사용 하 여 공개 미리 보기 컨테이너에 연결 합니다.

   a. **(로컬 및 연결)** 에서 **스토리지 계정**을 마우스 오른쪽 단추로 클릭하고 **Azure Storage에 연결**을 선택한 다음, **연결 문자열 또는 공유 액세스 서명 URI**를 선택하고 **다음**을 선택합니다.

      ![공유 액세스 서명을 사용하여 Azure Storage에 연결](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image9.png)

   b. **SAS URI 사용**을 선택한 다음, **URI** 상자에서 다음 SAS URI를 입력합니다.

      `https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

      ![컨테이너에 공유 액세스 서명 제공](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image10.png)

   다. **다음**, **연결**을 차례로 선택합니다.

   d. 왼쪽 창에서 연결된 **publicpreview** 컨테이너를 선택한 다음, *CustomSetupScript* 폴더를 두 번 클릭합니다. 이 폴더에는 다음 항목이 있습니다.

      * *Sample* 폴더 - 기본 작업을 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치가 포함되어 있습니다. 이 작업은 몇 초 동안 대기하면서 아무 것도 수행하지 않습니다. 또한 폴더에는 *gacutil* 폴더도 포함되어 있습니다. 이 폴더의 전체 콘텐츠(*gacutil.exe*, *gacutil.exe.config* 및 *1033\gacutlrc.dll*)는 있는 그대로 컨테이너에 복사할 수 있습니다.

      * *UserScenarios* 폴더 - 실제 사용자 시나리오의 몇 가지 사용자 지정 설치 샘플이 포함되어 있습니다.

        ![공개 미리 보기 컨테이너의 콘텐츠](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image11.png)

   e. *UserScenarios* 폴더를 두 번 클릭하여 다음 항목을 찾습니다.

      * *.NET FRAMEWORK 3.5* 폴더 - 사용자 지정 구성 요소에 필요할 수 있는 이전 버전의 .NET Framework를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다.

      * *BCP* 폴더 - 대량 복사 프로그램(*bcp*)을 포함하여 SQL Server 명령줄 유틸리티(*MsSqlCmdLnUtils.msi*)를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다.

      * *EXCEL* 폴더 - 스크립트 작업에서 Excel 파일을 동적으로 읽고 쓰는 데 사용할 수 있는 C# 어셈블리 및 라이브러리를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 
      
        먼저 [*ExcelDataReader.dll*](https://www.nuget.org/packages/ExcelDataReader/) 및 [*DocumentFormat.OpenXml.dll*](https://www.nuget.org/packages/DocumentFormat.OpenXml/)을 다운로드한 다음, *main.cmd*와 함께 모두 컨테이너에 업로드합니다. 또는 표준 Excel 연결 관리자, Excel 원본 및 Excel 대상만 사용하려는 경우 필요한 액세스 재배포 가능 구성 요소가 이미 Azure-SSIS IR에 미리 설치되어 있으므로 사용자 지정 설치가 필요하지 않습니다.
      
      * *MYSQL ODBC* 폴더 - MySQL ODBC 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ODBC 연결 관리자, 원본 및 대상을 사용하여 MySQL 서버에 연결할 수 있습니다. 
     
        먼저 [최신 64비트 및 32비트 버전의 MySQL ODBC 드라이버 설치 프로그램(예: *mysql-connector-odbc-8.0.13-winx64.msi* 및 *mysql-connector-odbc-8.0.13-win32.msi*)을 다운로드](https://dev.mysql.com/downloads/connector/odbc/)한 다음, *main.cmd*와 함께 모두 컨테이너에 업로드합니다.

      * *ORACLE ENTERPRISE* 폴더 - Oracle 커넥터 및 OCI 드라이버를 Azure-SSIS IR Enterprise Edition의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*) 및 자동 설치 구성 파일(*client.rsp*)이 포함되어 있습니다. 이 설치를 통해 Oracle 연결 관리자, 원본 및 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=55179)에서 Oracle용 Microsoft Connectors v5.0(*AttunitySSISOraAdaptersSetup.msi* 및 *AttunitySSISOraAdaptersSetup64.msi*)을 다운로드하고, [Orace](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-win64-download-2297732.html)에서 최신 Oracle 클라이언트(예: *winx64_12102_client.zip*)를 다운로드한 다음, *main.cmd* 및 *client.rsp*와 함께 모두 컨테이너에 업로드합니다. TNS를 사용하여 Oracle에 연결하는 경우 설치 중에 Oracle 설치 폴더에 복사할 수 있도록 *tnsnames.ora*도 다운로드하여 편집하고 컨테이너에 업로드해야 합니다.

      * *ORACLE STANDARD ADO.NET* 폴더 - Oracle ODP.NET 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ADO.NET 연결 관리자, 원본 및 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 [최신 Oracle ODP.NET 드라이버(예: *ODP.NET_Managed_ODAC122cR1.zip*)를 다운로드](https://www.oracle.com/technetwork/database/windows/downloads/index-090165.html)한 다음, *main.cmd*와 함께 모두 컨테이너에 업로드합니다.
       
      * *ORACLE STANDARD ODBC* 폴더 - Oracle ODBC 드라이버를 Azure-SSIS IR의 각 노드에 설치하여 DSN(데이터 원본 이름)을 구성하기 위한 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ODBC 연결 관리자, 원본 및 대상 또는 파워 쿼리 연결 관리자 및 ODBC 데이터 원본 유형이 포함된 원본을 사용하여 Oracle 서버에 연결할 수 있습니다. 
      
        먼저 최신 Oracle Instant Client(기본 패키지 또는 기본 Lite 패키지) 및 ODBC 패키지를 다운로드한 다음, *main.cmd*와 함께 모두 컨테이너에 업로드합니다.
        * [64비트 패키지 다운로드](https://www.oracle.com/technetwork/topics/winx64soft-089540.html)(기본 패키지: *instantclient-basic-windows.x64-18.3.0.0.0dbru.zip*, 기본 Lite 패키지: *instantclient-basiclite-windows.x64-18.3.0.0.0dbru.zip*, ODBC 패키지: *instantclient-odbc-windows.x64-18.3.0.0.0dbru.zip*) 
        * [32비트 패키지 다운로드](https://www.oracle.com/technetwork/topics/winsoft-085727.html)(기본 패키지: *instantclient-basic-nt-18.3.0.0.0dbru.zip*, 기본 Lite 패키지: *instantclient-basiclite-nt-18.3.0.0.0dbru.zip*, ODBC 패키지: *instantclient-odbc-nt-18.3.0.0.0dbru.zip*)

      * *ORACLE STANDARD OLEDB* 폴더 - Oracle OLEDB 드라이버를 Azure-SSIS IR의 각 노드에 설치하기 위한 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 OLEDB 연결 관리자, 원본 및 대상을 사용하여 Oracle 서버에 연결할 수 있습니다. 
     
        먼저 [최신 OLEDB 드라이버(예: *ODAC122010Xcopy_x64.zip*)를 다운로드](https://www.oracle.com/partners/campaign/index-090165.html)한 다음, *main.cmd*와 함께 모두 컨테이너에 업로드합니다.

      * *POSTGRESQL ODBC* 폴더 - PostgreSQL ODBC 드라이버를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 ODBC 연결 관리자, 원본 및 대상을 사용하여 PostgreSQL 서버에 연결할 수 있습니다. 
     
        먼저 [최신 64비트 및 32비트 버전의 PostgreSQL ODBC 드라이버 설치 프로그램(예: *psqlodbc_x64.msi* 및 *psqlodbc_x86.msi*)을 다운로드](https://www.postgresql.org/ftp/odbc/versions/msi/)한 다음, *main.cmd*와 함께 모두 컨테이너에 업로드합니다.

      * *SAP BW* 폴더 - SAP .NET 연결 어셈블리(*librfc32.dll*)를 Azure-SSIS IR Enterprise Edition의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 SAP BW(Business Warehouse) 연결 관리자, 원본 및 대상을 사용하여 SAP BW 서버에 연결할 수 있습니다. 
      
        먼저 SAP 설치 폴더의 64비트 또는 32비트 버전의 *librfc32.dll*을 *main.cmd*와 함께 모두 컨테이너에 업로드합니다. 그런 다음, 스크립트에서 설치 중에 SAP 어셈블리를 *%windir%\SysWow64* 또는 *%windir%\System32* 폴더에 복사합니다.

      * *STORAGE* 폴더 - Azure PowerShell을 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다. 이 설치를 통해 [PowerShell 스크립트를 실행하여 Azure 스토리지 계정을 조작](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-use-blobs-powershell)하는 SSIS 패키지를 배포 및 실행할 수 있습니다. 
      
        *main.cmd*, *AzurePowerShell.msi* 샘플(또는 최신 버전 사용) 및 *storage.ps1*을 컨테이너에 복사합니다. *PowerShell.dtsx*를 패키지에 대한 템플릿으로 사용합니다. 패키지 템플릿은 *storage.ps1*을 수정 가능한 PowerShell 스크립트로 다운로드하는 [Azure Blob 다운로드 작업](https://docs.microsoft.com/sql/integration-services/control-flow/azure-blob-download-task)과 각 노드에서 스크립트를 실행하는 [프로세스 실행 작업](https://blogs.msdn.microsoft.com/ssis/2017/01/26/run-powershell-scripts-in-ssis/)을 결합합니다.

      * *TERADATA* 폴더 - 사용자 지정 설치 스크립트(*main.cmd*), 관련 파일(*install.cmd*) 및 설치 관리자 패키지( *.msi*)가 포함되어 있습니다. 이러한 파일은 Teradata 커넥터, TPT(Teradata Parallel Transporter) API 및 ODBC 드라이버를 Azure-SSIS IR Enterprise Edition의 각 노드에 설치합니다. 이 설치를 통해 Teradata 연결 관리자, 원본 및 대상을 사용하여 Teradata 서버에 연결할 수 있습니다. 
      
        먼저 [Teradata 도구 및 Utilities 15.x zip 파일(예: *TeradataToolsAndUtilitiesBase__windows_indep.15.10.22.00.zip*)을 다운로드](http://partnerintelligence.teradata.com)한 다음, 앞에서 언급한 *.cmd* 및 *.msi* 파일과 함께 모두 컨테이너에 업로드합니다.

      * *TLS 1.2* 폴더 - Azure-SSIS IR의 각 노드에서 강력한 암호화/더 안전한 네트워크 프로토콜(TLS 1.2)을 사용하고 이전의 SSL/TLS 버전을 사용하지 않도록 설정하는 사용자 지정 설치 스크립트(*main.cmd*)가 포함되어 있습니다.

      * *ZULU OPENJDK* 폴더 - Zulu OpenJDK를 Azure-SSIS IR의 각 노드에 설치하는 사용자 지정 설치 스크립트(*main.cmd*) 및 PowerShell 파일(*iinstall_openjdk.ps1*)이 포함되어 있습니다. 이 설치를 통해 Azure Data Lake Store 및 Flexible File 커넥터를 사용하여 ORC 및 Parquet 파일을 처리할 수 있습니다. 자세한 내용은 [Integration Services용 Azure 기능 팩](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15#dependency-on-java)을 참조하세요. 
      
        먼저 [최신 Zulu OpenJDK(예: *zulu8.33.0.1-jdk8.0.192-win_x64.zip*)를 다운로드](https://www.azul.com/downloads/zulu/zulu-windows/)한 다음, *main.cmd* 및 *install_openjdk.ps1*과 함께 모두 컨테이너에 업로드합니다.

        ![사용자 시나리오 폴더의 폴더](media/how-to-configure-azure-ssis-ir-custom-setup/custom-setup-image12.png)

   f. 이러한 표준 사용자 지정 설치 샘플을 다시 사용 하려면 선택한 폴더의 내용을 컨테이너에 복사 합니다.

1. ADF UI에서 Azure-SSIS IR를 프로 비전 하거나 다시 구성할 때 **Integration Runtime 설정** 창의 **고급 설정** 페이지에서 **추가 시스템 구성/구성 요소 설치를 사용 하 여 Azure-SSIS Integration Runtime 사용자** 지정 확인란을 선택 하 고 **사용자 지정 설치 컨테이너 SAS uri** 상자에 컨테이너의 SAS uri를 입력 합니다.
   
1. Azure PowerShell를 사용 하 여 Azure-SSIS IR를 프로 비전 하거나 다시 구성 하는 경우, 이미 시작/실행 중이면 중지 하 `Set-AzDataFactoryV2IntegrationRuntime` 고, 컨테이너의 SAS URI를 매개 변수 값으로 사용 하 여 cmdlet을 실행 한 `SetupScriptContainerSasUri` 다음 Azure-SSIS IR을 시작 합니다.

1. 표준 사용자 지정 설치를 완료 하 고 Azure-SSIS IR를 시작 하면 컨테이너의 *기본 .cmd .log* 폴더에 있는 *기본 .cmd* 및 기타 실행 로그의 표준 출력을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS IR Enterprise Edition 설정](how-to-configure-azure-ssis-ir-enterprise-edition.md)
- [Azure-SSIS IR에 대 한 유료 또는 라이선스 구성 요소 개발](how-to-develop-azure-ssis-ir-licensed-components.md)
