---
title: SSIS의 프록시로 자체 호스팅 통합 런타임 구성
description: Azure-SSIS 통합 런타임에 대한 프록시로 자체 호스팅 통합 런타임을 구성하는 방법을 알아봅니다.
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
ms.date: 04/15/2020
ms.openlocfilehash: 4cb5b84f3889dcf4e0f28d525afb42cfeac5b54c
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605504"
---
# <a name="configure-a-self-hosted-ir-as-a-proxy-for-an-azure-ssis-ir-in-azure-data-factory"></a>Azure 데이터 팩터리에서 Azure-SSIS IR에 대한 프록시로 자체 호스팅 IR 구성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure-SSIS 통합 런타임(Azure-SSIS IR)에서 프록시로 구성된 자체 호스팅 통합 런타임(자체 호스팅 IR)을 사용하여 Azure-SSIS 통합 런타임(Azure-SSIS IR)에서 SQL Server 통합 서비스(SSIS) 패키지를 실행하는 방법을 설명합니다. 

이 기능을 사용하면 [Azure-SSIS IR을 가상 네트워크에 조인하지](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network)않고도 온-프레미스 데이터에 액세스할 수 있습니다. 이 기능은 회사 네트워크에 구성이 너무 복잡하거나 Azure-SSIS IR을 삽입하기에 너무 제한적인 정책이 있는 경우에 유용합니다.

이 기능은 온-프레미스 데이터 원본이 있는 모든 SSIS 데이터 흐름 태스크를 두 가지 스테이징 태스크로 분할합니다. 
* 자체 호스팅 IR에서 실행되는 첫 번째 작업은 먼저 온-프레미스 데이터 원본에서 Azure Blob 저장소의 스테이징 영역으로 데이터를 이동합니다.
* Azure-SSIS IR에서 실행되는 두 번째 작업은 준비 영역에서 의도한 데이터 대상으로 데이터를 이동합니다.

예를 들어 이 기능의 다른 이점과 기능을 사용하면 Azure-SSIS IR에서 아직 지원되지 않는 지역에서 자체 호스팅 IR을 설정하고 데이터 원본의 방화벽에서 자체 호스팅 IR의 공개 정적 IP 주소를 허용할 수 있습니다.

## <a name="prepare-the-self-hosted-ir"></a>자체 호스팅 IR 준비

이 기능을 사용하려면 먼저 데이터 팩터리를 만들고 Azure-SSIS IR을 설정합니다. 아직 수행하지 않은 경우 [Azure-SSIS IR 설정의](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure)지침을 따릅니다.

그런 다음 Azure-SSIS IR이 설정된 동일한 데이터 팩터리에서 자체 호스팅 IR을 설정합니다. 이렇게 하려면 [자체 호스팅 IR 만들기를](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)참조하십시오.

마지막으로, 다음과 같이 온-프레미스 컴퓨터 또는 Azure 가상 컴퓨터(VM)에서 자체 호스팅 IR의 최신 버전과 추가 드라이버 및 런타임을 다운로드하여 설치합니다.
- [자체 호스팅 IR의](https://www.microsoft.com/download/details.aspx?id=39717)최신 버전을 다운로드하여 설치합니다.
- 패키지에 개체 연결 및 포함 데이터베이스(OLEDB) 커넥터를 사용하는 경우 자체 호스팅 IR이 설치된 동일한 컴퓨터에 관련 OLEDB 드라이버를 다운로드하여 설치합니다.  

  SQL Server(SQL Server 네이티브 클라이언트 [SQLNCLI])에 OLEDB 드라이버의 이전 버전을 사용하는 경우 [64비트 버전을 다운로드합니다.](https://www.microsoft.com/download/details.aspx?id=50402)  

  SQL Server(MSOLEDBSQL)에 대한 최신 버전의 OLEDB 드라이버를 사용하는 경우 [64비트 버전을 다운로드합니다.](https://www.microsoft.com/download/details.aspx?id=56730)  
  
  PostgreSQL, MySQL, Oracle 등과 같은 다른 데이터베이스 시스템에 OLEDB 드라이버를 사용하는 경우 웹 사이트에서 64비트 버전을 다운로드할 수 있습니다.
- 아직 다운로드하지 않은 경우 자체 호스팅 IR이 설치된 동일한 컴퓨터에 [64비트 버전의 Visual C++ (VC) 런타임을 다운로드하여 설치합니다.](https://www.microsoft.com/download/details.aspx?id=40784)

## <a name="prepare-the-azure-blob-storage-linked-service-for-staging"></a>스테이징을 위해 Azure Blob 저장소 연결 서비스 준비

아직 설정하지 않은 경우 Azure-SSIS IR이 설정된 동일한 데이터 팩터리에서 Azure Blob 저장소 연결 서비스를 만듭니다. 이렇게 하려면 [Azure 데이터 팩터리 연결 서비스 만들기를](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)참조하십시오. 다음을 수행해야 합니다.
- **데이터 저장소의**경우 **Azure Blob 저장소를**선택합니다.  
- **통합 런타임을 통해 연결하려면**Azure Blob Storage에 대한 액세스 자격 증명을 가져오기 위해 기본 Azure IR을 사용하기 때문에 **자동 해결 통합 런타임(Azure-SSIS** IR이나 자체 호스팅 IR이 아님)을 선택합니다.
- **인증 방법의**경우 **계정 키,** **SAS URI**또는 서비스 **주체를 선택합니다.**  

    >[!TIP]
    >**서비스 주체** 방법을 선택하는 경우 서비스 주체에게 최소한 *저장소 Blob 데이터 기여자* 역할을 부여합니다. 자세한 내용은 Azure [Blob 저장소 커넥터를](connector-azure-blob-storage.md#linked-service-properties)참조하십시오.

![스테이징을 위해 Azure Blob 저장소 연결 서비스 준비](media/self-hosted-integration-runtime-proxy-ssis/shir-azure-blob-storage-linked-service.png)

## <a name="configure-an-azure-ssis-ir-with-your-self-hosted-ir-as-a-proxy"></a>자체 호스팅 IR을 프록시로 Azure-SSIS IR으로 구성

스테이징을 위해 자체 호스팅 IR 및 Azure Blob 저장소 연결 서비스를 준비한 후 이제 데이터 팩터리 포털 또는 앱의 프록시로 자체 호스팅IR을 사용하여 새 또는 기존 Azure-SSIS IR을 구성할 수 있습니다. 그러나 이렇게 하기 전에 기존 Azure-SSIS IR이 이미 실행 중인 경우 중지한 다음 다시 시작합니다.

1. 통합 **런타임 설정** 창에서 **다음**을 선택하여 **일반 설정** 및 SQL **설정** 섹션을 건너뛰고. 

1. 고급 설정 섹션에서 다음을 **수행합니다.**

   1. **Azure-SSIS 통합 런타임 실행 시간 설정 확인란에 대한 프록시로 자체 호스팅 통합 런타임 설정** 확인란을 선택합니다. 

   1. 자체 **호스팅 통합 런타임** 드롭다운 목록에서 Azure-SSIS IR에 대한 프록시로 기존 자체 호스팅 IR을 선택합니다.

   1. **스테이징 저장소 연결 서비스** 드롭다운 목록에서 기존 Azure Blob 저장소 연결 서비스를 선택하거나 스테이징을 위해 새 서비스를 만듭니다.

   1. 준비 **경로** 상자에서 선택한 Azure Blob 저장소 계정에 Blob 컨테이너를 지정하거나 스테이징에 기본 컨테이너를 사용하도록 비워 둡니다.

   1. **계속**을 선택합니다.

   ![자체 호스팅 IR을 갖춘 고급 설정](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

PowerShell을 사용하여 자체 호스팅 IR을 프록시로 사용하여 새 또는 기존 Azure-SSIS IR을 프록시로 구성할 수도 있습니다.

```powershell
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$AzureSSISName = "[your Azure-SSIS IR name]"
# Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force
```

## <a name="enable-ssis-packages-to-connect-by-proxy"></a>SSIS 패키지가 프록시로 연결하도록 설정

Visual Studio용 SSIS 프로젝트 확장프로그램이 있는 최신 SSDT 또는 독립 실행형 `ConnectByProxy` 설치 관리자를 사용하면 OLEDB 또는 플랫 파일 연결 관리자에 추가된 새 속성을 찾을 수 있습니다.
* [비주얼 스튜디오를 위한 SSIS 프로젝트 확장 기능으로 SSDT 다운로드](https://marketplace.visualstudio.com/items?itemName=SSIS.SqlServerIntegrationServicesProjects)
* [독립 실행형 설치 관리자 다운로드](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017#ssdt-for-vs-2017-standalone-installer)   

온-프레미스에서 데이터베이스 또는 파일에 액세스할 수 있는 OLEDB 또는 플랫 파일 원본이 있는 데이터 흐름 태스크가 포함된 새 패키지를 디자인할 때 관련 연결 관리자의 **속성** 창에서 *True로* 설정하여 이 속성을 활성화할 수 있습니다.

![ConnectByProxy 속성 사용](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-manager-properties.png)

기존 패키지를 하나씩 수동으로 변경할 필요 없이 기존 패키지를 실행할 때 이 속성을 사용하도록 설정할 수도 있습니다.  옵션에는
- **옵션 A:** Azure-SSIS IR에서 실행할 최신 SSDT가 포함된 패키지를 열고 다시 빌드하고 다시 배포합니다. 그런 다음 관련 연결 관리자에 대해 *True로* 설정하여 속성을 활성화할 수 있습니다. SSMS에서 패키지를 실행하는 경우 이러한 연결 관리자는 **패키지 실행** 팝업 창의 **연결 관리자** 탭에 나타납니다.

  ![ConnectByProxy 속성 사용2](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssms.png)

  또한 Data Factory 파이프라인에서 패키지를 실행할 때 [SSIS 패키지 작업 실행의](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) **연결 관리자** 탭에 나타나는 관련 연결 관리자에 대해 *True로* 설정하여 속성을 활성화할 수도 있습니다.
  
  ![ConnectByProxy 속성 사용3](media/self-hosted-integration-runtime-proxy-ssis/shir-connection-managers-tab-ssis-activity.png)

- **옵션 B:** SSIS IR에서 실행되도록 해당 패키지가 포함된 프로젝트를 다시 배포합니다. 그런 다음 SSMS에서 패키지를 실행할 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`때 속성 경로를 제공하고 속성 재정의패키지 **실행** 팝업 창의 **고급** 탭에서 *True로* 설정하여 속성을 활성화할 수 있습니다.

  ![ConnectByProxy 속성 사용4](media/self-hosted-integration-runtime-proxy-ssis/shir-advanced-tab-ssms.png)

  또한 속성 `\Package.Connections[YourConnectionManagerName].Properties[ConnectByProxy]`경로를 제공하고 Data Factory 파이프라인에서 패키지를 실행할 때 속성 재정의 [SSIS 패키지 활동](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity) 탭에서 속성 재정의 탭에서 *속성* **재정의로** 설정하여 속성을 활성화할 수도 있습니다.
  
  ![ConnectByProxy 속성 사용5](media/self-hosted-integration-runtime-proxy-ssis/shir-property-overrides-tab-ssis-activity.png)

## <a name="debug-the-first-and-second-staging-tasks"></a>첫 번째 및 두 번째 스테이징 작업 디버깅

자체 호스팅 IR에서 *C:\ProgramData\SSISTeleTelemetry* 폴더에서 런타임 로그와 *C:\ProgramData\SSISTelemetry\ExecutionLog* 폴더에서 첫 번째 스테이징 작업의 실행 로그를 찾을 수 있습니다.  SSISDB 또는 파일 시스템, 파일 공유 또는 Azure 파일에 패키지를 저장하는지 여부에 따라 SSISDB 또는 지정된 로깅 경로에서 두 번째 스테이징 작업의 실행 로그를 찾을 수 있습니다. 두 번째 스테이징 작업의 실행 로그에서 첫 번째 스테이징 작업의 고유한 아이디를 찾을 수도 있습니다. 

![첫 번째 스테이징 작업의 고유 ID](media/self-hosted-integration-runtime-proxy-ssis/shir-first-staging-task-guid.png)

## <a name="use-windows-authentication-in-staging-tasks"></a>스테이징 작업에서 Windows 인증 사용

자체 호스팅 IR의 스테이징 작업에 Windows 인증이 필요한 경우 [SSIS 패키지가 동일한 Windows 인증을 사용하도록 구성합니다.](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth?view=sql-server-ver15) 

스테이징 작업은 자체 호스팅 IR 서비스*계정(NT SERVICE\DIAHostService*, 기본적으로)으로 호출되며 데이터 저장소는 Windows 인증 계정으로 액세스됩니다. 두 계정 모두 특정 보안 정책을 할당해야 합니다. 자체 호스팅 IR 컴퓨터에서 로컬 **보안 정책** > **로컬 정책** > **사용자 권한 할당으로**이동한 다음 다음을 수행합니다.

1. *프로세스에 대해 메모리 조정 할당량을* 할당하고 프로세스 수준 토큰 정책을 자체 호스팅 IR 서비스 계정으로 *바꿉니다.* 이 문제는 기본 서비스 계정으로 자체 호스팅 IR을 설치할 때 자동으로 발생합니다. 그렇지 않은 경우 해당 정책을 수동으로 할당합니다. 다른 서비스 계정을 사용하는 경우 동일한 정책을 할당합니다.

1. *로온을* Windows 인증 계정에 서비스 정책으로 할당합니다.

## <a name="billing-for-the-first-and-second-staging-tasks"></a>첫 번째 및 두 번째 스테이징 작업에 대한 청구

자체 호스팅 IR에서 실행되는 첫 번째 스테이징 작업은 자체 호스팅 IR에서 실행되는 모든 데이터 이동 활동이 청구되는 것처럼 별도로 청구됩니다. Azure [Data Factory 데이터 파이프라인 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) 문서에 지정 되어 있습니다.

Azure-SSIS IR에서 실행되는 두 번째 스테이징 작업은 별도로 청구되지 않지만 실행 중인 Azure-SSIS IR은 [Azure-SSIS IR 가격 책정](https://azure.microsoft.com/pricing/details/data-factory/ssis/) 문서에 지정된 대로 청구됩니다.

## <a name="enabling-tls-12"></a>TLS 1.2 사용 설정

강력한 암호화/보다 안전한 네트워크 프로토콜(TLS 1.2)을 사용하고 자체 호스팅 IR에서 이전 SSL/TLS 버전을 사용하지 않도록 설정해야 하는 경우 공개 미리 보기 컨테이너의 *CustomSetupScript/UserScenario/TLS 1.2* 폴더에서 찾을 수 있는 *main.cmd* 스크립트를 다운로드하여 실행할 수 있습니다.  [Azure 저장소 탐색기를](https://storageexplorer.com/)사용 하 여 다음 SAS URI를 입력 하 여 공용 미리 보기 컨테이너에 연결할 수 있습니다.

`https://ssisazurefileshare.blob.core.windows.net/publicpreview?sp=rl&st=2020-03-25T04:00:00Z&se=2025-03-25T04:00:00Z&sv=2019-02-02&sr=c&sig=WAD3DATezJjhBCO3ezrQ7TUZ8syEUxZZtGIhhP6Pt4I%3D`

## <a name="current-limitations"></a>현재 제한 사항

- 현재 개방형 데이터베이스 연결(ODBC)/OLEDB/플랫 파일 소스 또는 OLEDB 대상이 있는 데이터 흐름 작업만 지원됩니다. 
- *계정 키,* *SAS(공유 액세스 서명) URI*또는 *서비스 주체* 인증으로 구성된 Azure Blob 저장소 연결 서비스만 현재 지원됩니다.
- OLEDB 소스의 *매개 변수 매핑은* 아직 지원되지 않습니다. 해결 방법으로 *변수에서 SQL 명령을* *AccessMode로* 사용하고 *표현식을* 사용하여 SQL 명령에 변수/매개 변수를 삽입하십시오. 그림에서는 공개 미리 보기 컨테이너의 *SelfHostedIRProxy/제한* 폴더에서 찾을 수 있는 *ParameterMappingSample.dtsx* 패키지를 참조하십시오. Azure 저장소 탐색기를 사용 하 여 위의 SAS URI를 입력 하 여 공용 미리 보기 컨테이너에 연결할 수 있습니다.

## <a name="next-steps"></a>다음 단계

자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성한 후 패키지를 배포하고 실행하여 데이터 팩터리 파이프라인에서 SSIS 패키지 활동 실행으로 온-프레미스 데이터에 액세스할 수 있습니다. 방법에 대해 알아보려면 [데이터 팩터리 파이프라인에서 SSIS 패키지 실행 활동으로 SSIS 패키지 실행을](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity)참조하십시오.
