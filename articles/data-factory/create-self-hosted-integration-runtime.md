---
title: 자체 호스팅 Integration Runtime 만들기
description: Azure Data Factory에서 데이터 팩터리에서 개인 네트워크의 데이터 저장소에 액세스할 수 있는 자체 호스팅 통합 런타임을 만드는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/10/2020
ms.openlocfilehash: 6302a7d6ffe7218d339121ec98a624f8e98356f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065583"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 만들기 및 구성

IR(통합 런타임)은 서로 다른 네트워크 환경에서 데이터 통합 기능을 제공하기 위해 Azure Data Factory에서 사용하는 컴퓨팅 인프라입니다. IR에 대한 자세한 내용은 [통합 런타임 개요를](concepts-integration-runtime.md)참조하십시오.

자체 호스팅 통합 런타임은 클라우드 데이터 저장소와 개인 네트워크의 데이터 저장소 간에 복사 활동을 실행할 수 있습니다. 또한 온-프레미스 네트워크 또는 Azure 가상 네트워크의 계산 리소스에 대해 변환 활동을 디스패치할 수도 있습니다. 자체 호스팅 통합 런타임을 설치하려면 온-프레미스 컴퓨터 또는 개인 네트워크 내부의 가상 시스템이 필요합니다.  

이 문서에서는 자체 호스팅 IR을 만들고 구성하는 방법에 대해 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 설정

자체 호스팅 통합 런타임을 만들고 설정하려면 다음 절차를 사용합니다.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Azure PowerShell을 통해 자체 호스팅 IR 만들기

1. 이 작업에 Azure PowerShell을 사용할 수 있습니다. 다음은 예제입니다.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. 로컬 컴퓨터에서 자체 호스팅 통합 런타임을 [다운로드](https://www.microsoft.com/download/details.aspx?id=39717)하여 설치합니다.

3. 인증 키를 검색한 다음 해당 키를 사용하여 자체 호스팅 통합 런타임을 등록합니다. 다음은 PowerShell 예제입니다.

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Azure 데이터 팩터리 UI를 통해 자체 호스팅 IR 만들기

다음 단계를 사용하여 Azure 데이터 팩터리 UI를 사용하여 자체 호스팅IR을 만듭니다.

1. Azure 데이터 팩터리 UI의 **시작** 페이지에서 가장 왼쪽 창에서 **작성자** 탭을 선택합니다.

   ![홈 페이지 작성자 버튼](media/doc-common-process/get-started-page-author-button.png)

1. 가장 왼쪽 창 의 맨 아래에 있는 **연결을** 선택하고 **연결** 창에서 **통합 런타임을** 선택합니다. **+새**.

   ![Integration Runtime 만들기](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. 통합 **런타임 설정** 창에서 **데이터 이동 수행 및 외부 계산으로 의 디스패치 활동**수행을 선택하고 **계속을**선택합니다.

1. IR의 이름을 입력하고 **을**선택합니다.

1. **옵션 1의** 링크를 선택하여 컴퓨터에서 익스프레스 설정을 엽니다. 또는 **옵션 2의** 단계를 수행하여 수동으로 설정합니다. 다음 지침은 수동 설정을 기반으로 합니다.

   ![통합 런타임 설정](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. 인증 키를 복사하여 붙여넣습니다. **통합 런타임 다운로드 및 설치를**선택합니다.

    1. 로컬 Windows 컴퓨터에 자체 호스팅된 통합 런타임을 다운로드합니다. 설치 관리자를 실행합니다.

    1. 통합 **런타임(자체 호스팅)** 페이지에서 이전에 저장한 키를 붙여넣은 다음 **등록을**선택합니다.
    
       ![통합 런타임 등록](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. **새 통합 런타임(자체 호스팅) 노드** 페이지에서 **마침**을 선택합니다.

1. 자체 호스팅 통합 런타임이 성공적으로 등록되면 다음 창이 표시됩니다.

    ![성공적인 등록](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Azure 리소스 관리자 템플릿을 통해 Azure VM에서 자체 호스팅IR 설정

자체 호스트 IR [템플릿 만들기를](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime)사용하여 Azure 가상 컴퓨터에서 자체 호스팅 IR 설정을 자동화할 수 있습니다. 템플릿은 Azure 가상 네트워크 내에서 완벽하게 작동하는 자체 호스팅 IR을 쉽게 가질 수 있는 방법을 제공합니다. IR에는 노드 수를 2 이상으로 설정하는 한 고가용성 및 확장성 기능이 있습니다.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>로컬 PowerShell을 통해 기존 자체 호스팅 IR 설정

명령줄을 사용하여 기존 자체 호스팅 IR을 설정하거나 관리할 수 있습니다. 이러한 사용은 특히 자체 호스팅 IR 노드의 설치 및 등록을 자동화하는 데 도움이 될 수 있습니다.

Dmgcmd.exe는 자체 호스팅 설치 관리자에 포함되어 있습니다. 일반적으로 C:\프로그램 파일\Microsoft 통합 런타임\3.0\Shared\ 폴더에 있습니다. 이 응용 프로그램은 다양한 매개 변수를 지원하며 자동화를 위해 배치 스크립트를 사용하여 명령줄을 통해 호출 할 수 있습니다.

다음과 같이 응용 프로그램을 사용합니다.

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

다음은 응용 프로그램의 매개 변수 및 속성에 대한 세부 정보입니다. 

| 속성                                                    | 설명                                                  | 필수 |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **레지스터뉴노드** "`<AuthenticationKey>`"                     | 지정된 인증 키로 자체 호스팅 통합 런타임 노드를 등록합니다. | 예       |
| **레지스터뉴노드** "`<AuthenticationKey>`" "`<NodeName>`"      | 지정된 인증 키 및 노드 이름으로 자체 호스팅 통합 런타임 노드를 등록합니다. | 예       |
| **인에이블원격액세스** "`<port>`" ["`<thumbprint>`"]            | 현재 노드에서 원격 액세스를 사용하여 고가용성 클러스터를 설정합니다. 또는 Azure 데이터 팩터리를 거치지 않고 자체 호스팅 IR에 대해 직접 자격 증명을 설정할 수 있습니다. 동일한 네트워크의 원격 컴퓨터에서 **New-AzDataFactoryV2LinkedService암호화된 자격 증명** cmdlet을 사용하여 후자를 수행합니다. | 예       |
| **인에이블원격액세스인컨테이너** "`<port>`" ["`<thumbprint>`"] | 노드가 컨테이너에서 실행될 때 현재 노드에 대한 원격 액세스를 활성화합니다. | 예       |
| **사용 안 함원격 액세스**                                         | 현재 노드에 대한 원격 액세스를 비활성화합니다. 멀티노드 설정에는 원격 액세스가 필요합니다. **New-AzDataFactoryV2LinkedService암호화된 자격 증명** powerShell cmdlet은 원격 액세스가 비활성화된 경우에도 여전히 작동합니다. 이 동작은 cmdlet이 자체 호스팅 IR 노드와 동일한 컴퓨터에서 실행되는 한 적용됩니다. | 예       |
| **키 (약)** "`<AuthenticationKey>`"                                 | 이전 인증 키를 덮어쓰거나 업데이트합니다. 이 작업에 주의 하십시오. 키가 새 통합 런타임인 경우 이전 자체 호스팅 IR 노드가 오프라인 상태가 될 수 있습니다. | 예       |
| **백업 파일 생성** "`<filePath>`" "`<password>`"            | 현재 노드에 대한 백업 파일을 생성합니다. 백업 파일에는 노드 키 및 데이터 저장소 자격 증명이 포함됩니다. | 예       |
| **가져오기 백업파일** "`<filePath>`" "`<password>`"              | 백업 파일에서 노드를 복원합니다.                          | 예       |
| **다시 시작**                                                     | 자체 호스팅 통합 런타임 호스트 서비스를 다시 시작합니다.   | 예       |
| **시작**                                                       | 자체 호스팅 통합 런타임 호스트 서비스를 시작합니다.     | 예       |
| **중지**                                                        | 자체 호스팅 통합 런타임 호스트 서비스를 중지합니다.        | 예       |
| **업그레이드 시작서비스**                                         | 자체 호스팅 통합 런타임 업그레이드 서비스를 시작합니다.       | 예       |
| **업그레이드 중지 서비스**                                          | 자체 호스팅 통합 런타임 업그레이드 서비스를 중지합니다.        | 예       |
| **턴온오토업데이트**                                            | 자체 호스팅 통합 런타임 자동 업데이트를 켭니다.        | 예       |
| **턴오프자동업데이트**                                           | 자체 호스팅 통합 런타임 자동 업데이트를 끕니다.       | 예       |
| **스위치 서비스 계정** "`<domain\user>`" ["`<password>`"]           | DIAHostService를 새 계정으로 실행하도록 설정합니다. 시스템 계정 및 가상 계정에 빈 암호 ""를 사용합니다. | 예       |


## <a name="command-flow-and-data-flow"></a>명령 흐름 및 데이터 흐름

온-프레미스와 클라우드 간에 데이터를 이동하는 경우 활동은 자체 호스팅 통합 런타임을 사용하여 온-프레미스 데이터 원본과 클라우드 간에 데이터를 전송합니다.

다음은 자체 호스팅 IR을 사용하여 복사하기 위한 데이터 흐름 단계에 대한 상위 수준 요약입니다.

![데이터 흐름에 대한 높은 수준의 개요](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. 데이터 개발자는 PowerShell cmdlet을 사용하여 Azure 데이터 팩터리 내에서 자체 호스팅된 통합 런타임을 만듭니다. 현재 Azure 포털에서는 이 기능을 지원하지 않습니다.
1. 데이터 개발자는 온-프레미스 데이터 저장소에 대해 연결된 서비스를 만듭니다. 개발자는 서비스가 데이터 저장소에 연결하는 데 사용해야 하는 자체 호스팅 통합 런타임 인스턴스를 지정합니다.
1. 자체 호스팅 통합 런타임 노드가 Windows DPAPI(데이터 보호 응용 프로그래밍 인터페이스)를 사용하여 자격 증명을 암호화하고 로컬에 저장합니다. 고가용성을 위해 여러 노드가 설정된 경우 자격 증명이 다른 노드 간에 동기화됩니다. 각 노드는 DPAPI를 사용하여 자격 증명을 암호화하고 로컬에 저장합니다. 자격 증명 동기화는 데이터 개발자에게는 표시되지 않으며, 자체 호스팅 IR에서 처리됩니다.
1. Azure Data Factory는 자체 호스팅 통합 런타임과 통신하여 작업을 예약하고 관리합니다. 통신은 공유 Azure 서비스 버스 [릴레이](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) 연결을 사용하는 제어 채널을 통해 전달됩니다. 활동 작업을 실행해야 하는 경우 Data Factory는 자격 증명 정보와 함께 요청을 큐에 큐에 대기합니다. 자격 증명이 자체 호스팅 통합 런타임에 아직 저장되지 않은 경우 그렇게 합니다. 자체 호스팅 통합 런타임은 큐를 폴러한 후 작업을 시작합니다.
1. 자체 호스팅 통합 런타임은 온-프레미스 저장소와 클라우드 저장소 간에 데이터를 복사합니다. 복사 방향은 데이터 파이프라인에서 복사 활동을 구성하는 방법에 따라 달라집니다. 이 단계에서 자체 호스팅 된 통합 런타임은 보안 HTTPS 채널을 통해 Azure Blob 저장소와 같은 클라우드 기반 저장소 서비스와 직접 통신합니다.

## <a name="considerations-for-using-a-self-hosted-ir"></a>자체 호스팅 IR 사용을 위한 고려 사항

- 여러 온-프레미스 데이터 원본에 대해 단일 자체 호스팅 통합 런타임을 사용할 수 있습니다. 동일한 Azure Active Directory(Azure AD) 테넌트 내에서 다른 데이터 팩터리와 공유할 수도 있습니다. 자세한 내용은 [자체 호스팅 통합 런타임 공유](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)를 참조하세요.
- 단일 컴퓨터에 자체 호스팅 통합 런타임의 인스턴스를 하나만 설치할 수 있습니다. 온-프레미스 데이터 원본에 액세스해야 하는 두 개의 데이터 팩터가 있는 경우 [자체 호스팅 IR 공유 기능을](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) 사용하여 자체 호스팅 IR을 공유하거나 각 데이터 팩터리마다 하나씩 두 대의 온-프레미스 컴퓨터에 자체 호스팅 IR을 설치합니다.  
- 자체 호스팅 통합 런타임이 데이터 원본과 동일한 컴퓨터에 있을 필요는 없습니다. 그러나 자체 호스팅 통합 런타임을 데이터 원본에 가깝게 두면 자체 호스팅 통합 런타임이 데이터 원본에 연결하는 시간이 줄어듭니다. 온-프레미스 데이터 원본을 호스팅하는 것과 다른 컴퓨터에 자체 호스팅 통합 런타임을 설치하는 것이 좋습니다. 자체 호스팅 통합 런타임 및 데이터 원본이 다른 컴퓨터에 있는 경우 자체 호스팅 통합 런타임은 리소스에 대한 데이터 원본과 경쟁하지 않습니다.
- 서로 다른 컴퓨터의 여러 자체 호스팅 통합 런타임이 동일한 온-프레미스 데이터 원본에 연결할 수 있습니다. 예를 들어 두 개의 데이터 팩터리를 제공하는 두 개의 자체 호스팅 통합 런타임이 있는 경우 동일한 온-프레미스 데이터 원본을 두 데이터 팩터리에 등록할 수 있습니다.
- Power BI 시나리오를 제공하기 위해 컴퓨터에 게이트웨이가 이미 설치되어 있는 경우 다른 컴퓨터에 Data Factory용 별도의 자체 호스팅 통합 런타임을 설치합니다.
- 자체 호스팅 통합 런타임을 사용하여 Azure 가상 네트워크 내의 데이터 통합을 지원합니다.
- Azure ExpressRoute를 사용하더라도 데이터 원본은 방화벽으로 보호되는 온-프레미스 데이터 원본으로 취급해야 합니다. 자체 호스팅 통합 런타임을 사용하여 서비스를 데이터 원본에 연결합니다.
- 데이터 저장소가 서비스(IaaS) 가상 컴퓨터로서 Azure 인프라의 클라우드에 있는 경우에도 자체 호스팅 통합 런타임을 사용합니다.
- FIPS 호환 암호화가 활성화된 Windows 서버에 설치한 자체 호스팅 통합 런타임에서 작업이 실패할 수 있습니다. 이 문제를 해결하려면 서버에서 FIPS 규격 암호화를 사용하지 않도록 설정합니다. FIPS 호환 암호화를 사용하지 않도록 설정하려면 다음 레지스트리 하위 키 값을 1(사용 `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`가능)에서 0(사용 안 함)으로 변경합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 지원되는 Windows 버전은 다음과 같습니다.
  + Windows 7 서비스 팩 1
  + Windows 8.1
  + 윈도우 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   도메인 컨트롤러에서 자체 호스팅된 통합 런타임설치는 지원되지 않습니다.
- .NET Framework 4.6.1 이상이 필요합니다. Windows 7 컴퓨터에 자체 호스팅 통합 런타임을 설치하는 경우 .NET Framework 4.6.1 이상을 설치합니다. 자세한 내용은 [.NET Framework 시스템 요구 사항](/dotnet/framework/get-started/system-requirements)을 참조하세요.
- 자체 호스팅 통합 런타임 머신의 권장 최소 구성은 4개의 코어, 8GB의 RAM 및 80GB의 사용 가능한 하드 드라이브 공간이 있는 2GHz 프로세서입니다.
- 호스트 컴퓨터가 최대 절전 모드인 경우 자체 호스팅 통합 런타임은 데이터 요청에 응답하지 않습니다. 따라서 자체 호스팅 통합 런타임을 설치하기 전에 컴퓨터에서 전원 관리 옵션을 적절하게 구성하세요. 컴퓨터가 최대 절전 모드로 구성된 경우 자체 호스팅 통합 런타임 설치 관리자는 메시지를 표시합니다.
- 자체 호스팅 통합 런타임을 성공적으로 설치하고 구성하려면 컴퓨터의 관리자여야 합니다.
- 복사 활동 실행은 특정 빈도로 발생합니다. 기기의 프로세서 및 RAM 사용량은 피크 및 유휴 시간에 동일한 패턴을 따릅니다. 리소스 사용량은 이동되는 데이터의 양에 크게 좌우됩니다. 여러 복사 작업이 진행 중인 경우 사용량이 많은 시간 동안 리소스 사용량이 증가하는 것을 볼 수 있습니다.
- 마루, ORC 또는 Avro 형식의 데이터를 추출하는 동안 작업이 실패할 수 있습니다. 마루에 대한 자세한 내용은 [Azure 데이터 팩터리의 마루 형식을](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime)참조하십시오. 파일 생성은 자체 호스팅 통합 컴퓨터에서 실행됩니다. 예상대로 작동하려면 파일 생성에는 다음과 같은 필수 구성 조건이 필요합니다.
    - [비주얼 C++ 2010 재배포 가능](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) 패키지 (x64)
    - [OpenJDK](https://adoptopenjdk.net/)채택과 같은 JRE 공급자의 Java 런타임(JRE) 버전 8. 환경 변수가 `JAVA_HOME` 설정되어 있는지 확인합니다.

## <a name="installation-best-practices"></a>설치 모범 사례

[Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=39717)관리되는 ID 설정 패키지를 다운로드하여 자체 호스팅 통합 런타임을 설치할 수 있습니다. 단계별 지침은 [온-프레미스와 클라우드 간에 데이터를 이동하는](tutorial-hybrid-copy-powershell.md) 문서를 참조하십시오.

- 컴퓨터가 최대 절전 모드로 들어가지 않도록 자체 호스팅 통합 런타임에 대해 호스트 컴퓨터에서 전원 관리 계획을 구성합니다. 호스트 컴퓨터가 최대 절전 모드로 설정되면 자체 호스팅 통합 런타임도 오프라인 상태가 됩니다.
- 자체 호스팅 통합 런타임과 관련된 자격 증명을 정기적으로 백업합니다.
- 자체 호스팅 IR 설정 작업을 자동화하려면 [PowerShell을 통해 기존 자체 호스팅 IR 설정을](#setting-up-a-self-hosted-integration-runtime)참조하십시오.  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Microsoft 다운로드 센터에서 자체 호스팅 IR 설치 및 등록

1. [Microsoft 통합 런타임 다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=39717)로 이동합니다.
1. **다운로드를**선택하고 64비트 버전을 선택하고 **다음**을 선택합니다. 32비트 버전은 지원되지 않습니다.
1. 관리되는 ID 파일을 직접 실행하거나 하드 드라이브에 저장하여 실행합니다.
1. **시작** 창에서 언어를 선택하고 **다음**을 선택합니다.
1. Microsoft 소프트웨어 사용 조건에 동의하고 **다음**을 선택합니다.
1. 자체 호스팅 통합 런타임을 설치할 **폴더**를 선택하고 **다음**을 선택합니다.
1. 설치 **준비** 페이지에서 **설치를**선택합니다.
1. 설치를 완료하려면 **완료를** 선택합니다.
1. PowerShell을 사용하여 인증 키를 가져옵니다. 인증 키 검색을 위한 PowerShell 예제는 다음과 같습니다.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. 컴퓨터에서 실행되는 Microsoft 통합 런타임 구성 관리자의 **통합 런타임(자체 호스팅)** 창에서 다음 단계를 수행합니다.

    1. 텍스트 영역에 인증 키를 붙여넣습니다.

    1. 필요에 따라 **인증 키 표시**를 선택하여 키 텍스트를 확인합니다.

    1. **등록을**선택합니다.

## <a name="high-availability-and-scalability"></a>고가용성 및 확장성

자체 호스팅 통합 런타임을 Azure의 여러 온-프레미스 컴퓨터 또는 가상 컴퓨터와 연결할 수 있습니다. 이러한 컴퓨터를 노드라고 합니다. 최대 4개의 노드를 자체 호스팅 통합 런타임에 연결할 수 있습니다. 논리 게이트웨이에 대해 게이트웨이가 설치된 온-프레미스 컴퓨터에 여러 노드가 있는 이점은 다음과 같습니다.

* 자체 호스팅 통합 런타임의 가용성이 높아지므로 빅 데이터 솔루션또는 데이터 팩터리와의 클라우드 데이터 통합에서 더 이상 단일 실패 지점이 아닙니다. 이 가용성은 최대 4개의 노드를 사용할 때 연속성을 보장하는 데 도움이 됩니다.
* 온-프레미스 및 클라우드 데이터 저장소 간의 데이터 이동 성능 및 처리량을 향상시킵니다. 자세한 내용은 [성능 비교](copy-activity-performance.md)를 참조하세요.

다운로드 센터에서 자체 호스팅 통합 런타임 소프트웨어를 설치하여 여러 노드를 연결할 수 [있습니다.](https://www.microsoft.com/download/details.aspx?id=39717) 그런 다음 [자습서에](tutorial-hybrid-copy-powershell.md)설명된 대로 **New-AzDataFactoryV2IntegrationRuntimekey** 에서 얻은 인증 키 중 하나를 사용하여 등록합니다.

> [!NOTE]
> 각 노드를 연결하기 위해 새 자체 호스팅 통합 런타임을 만들 필요가 없습니다. 자체 호스팅 통합 런타임을 다른 컴퓨터에서 설치하고 동일한 인증 키를 사용하여 등록할 수 있습니다.

> [!NOTE]
> 고가용성 및 확장성을 위해 다른 노드를 추가하기 전에 첫 번째 노드에서 **인트라넷에 대한 원격 액세스가** 활성화되어 있는지 확인합니다. 이렇게 하려면 **Microsoft 통합 런타임 구성 관리자** > **설정** > **인트라넷에 대한 원격 액세스를**선택합니다.

### <a name="scale-considerations"></a>크기 조정 고려 사항

#### <a name="scale-out"></a>확장

자체 호스팅 IR에서 프로세서 사용량이 높고 사용 가능한 메모리가 부족하면 새 노드를 추가하여 컴퓨터 간에 부하를 확장할 수 있습니다. 활동이 시간 부족또는 자체 호스팅 IR 노드가 오프라인으로 인해 실패하면 게이트웨이에 노드를 추가하는 데 도움이 됩니다.

#### <a name="scale-up"></a>강화

프로세서와 사용 가능한 RAM이 잘 활용되지 않지만 동시 작업의 실행이 노드의 한계에 도달하면 노드가 실행할 수 있는 동시 작업 수를 늘려 확장합니다. 자체 호스팅 IR이 오버로드되어 활동이 시간 초과될 때 확장할 수도 있습니다. 다음 이미지와 같이 노드의 최대 용량을 늘릴 수 있습니다.  

![노드에서 실행할 수 있는 동시 작업 수 늘리기](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>TLS/SSL 인증서 요구 사항

통합 런타임 노드 간의 통신을 보호하는 데 사용하는 TLS/SSL 인증서에 대한 요구 사항은 다음과 같습니다.

- 인증서는 공개적으로 신뢰할 수 있는 X509 v3 인증서여야 합니다. CA(공용 파트너 인증 기관)에서 발급한 인증서를 사용하는 것이 좋습니다.
- 각 통합 런타임 노드는 이 인증서를 신뢰해야 합니다.
- 마지막 SAN 항목만 사용되므로 SAN(주체 대체 이름) 인증서는 사용하지 않는 것이 좋습니다. 다른 모든 SAN 항목은 무시됩니다. 예를 들어, SAN이 **node1.domain.contoso.com** **node2.domain.contoso.com**SAN 인증서가 있는 경우 이 인증서는 완전 인증된 도메인 이름(FQDN)이 **node2.domain.contoso.com**컴퓨터에서만 사용할 수 있습니다.
- 인증서는 TLS/SSL 인증서에 Windows Server 2012 R2에서 지원하는 모든 키 크기를 사용할 수 있습니다.
- CNG 키를 사용하는 인증서는 지원되지 않습니다.  

> [!NOTE]
> 이 인증서는 다음과 같은 데 사용됩니다.
>
> - 자체 호스팅 IR 노드의 포트를 암호화합니다.
> - 노드 간 연결된 서비스의 자격 증명 동기화를 포함하는 상태 동기화를 위한 노드 간 통신의 경우
> - PowerShell cmdlet이 로컬 네트워크 내에서 연결된 서비스 자격 증명 설정에 사용되는 경우
>
> 개인 네트워크 환경이 안전하지 않거나 개인 네트워크 내의 노드 간의 통신을 보호하려는 경우 이 인증서를 사용하는 것이 좋습니다.
>
> 자체 호스팅 IR에서 다른 데이터 저장소로 전송되는 데이터 이동은 이 인증서가 설정되어 있는지 여부에 관계없이 항상 암호화된 채널 내에서 발생합니다.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Azure 데이터 팩터리에서 공유 자체 호스팅 통합 런타임 만들기

데이터 팩터리에 이미 설치한 기존의 자체 호스팅 통합 런타임 인프라를 재사용할 수 있습니다. 이 재사용을 사용하면 기존 공유 자체 호스팅 IR을 참조하여 다른 데이터 팩터리에서 연결된 자체 호스팅 통합 런타임을 만들 수 있습니다.

이 기능에 대한 소개및 데모를 보려면 다음 12분 짜리 비디오를 시청하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>용어

- **공유 IR**: 물리적 인프라에서 실행되는 원래 자체 호스팅 IR입니다.  
- **연결된 IR**: 다른 공유 IR을 참조하는 IR입니다. 연결된 IR은 논리적 IR이며 다른 공유 자체 호스팅 IR의 인프라를 사용합니다.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임을 공유하는 메서드

여러 데이터 팩터리와 자체 호스팅 통합 런타임을 공유하려면 자세한 내용은 [공유 자체 호스팅 통합 런타임 만들기를](create-shared-self-hosted-integration-runtime-powershell.md) 참조하십시오.

### <a name="monitoring"></a>모니터링

#### <a name="shared-ir"></a>공유 IR

![공유 통합 런타임을 찾기 위한 선택](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![공유 통합 런타임 모니터링](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>연결된 IR

![연결된 통합 런타임을 찾기 위한 선택](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![연결된 통합 런타임 모니터링](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>자체 호스팅 IR 공유에 대해 알려진 제한 사항

* 연결된 IR이 생성되는 데이터 팩터리에는 [관리되는 ID가](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)있어야 합니다. 기본적으로 Azure 포털 또는 PowerShell cmdlets에서 만든 데이터 팩토리에는 암시적으로 생성된 관리되는 ID가 있습니다. 그러나 Azure 리소스 관리자 템플릿 또는 SDK를 통해 데이터 팩터리를 만들 때 **Id** 속성을 명시적으로 설정해야 합니다. 이 설정을 사용하면 Resource Manager에서 관리되는 ID가 포함된 데이터 팩터리를 만듭니다.

* 이 기능을 지원하는 데이터 팩터리 .NET SDK는 버전 1.1.0 이상이어야 합니다.

* 권한을 부여하려면 공유 IR이 있는 데이터 팩터리에서 소유자 역할 또는 상속된 소유자 역할이 필요합니다.

* 공유 기능은 동일한 Azure AD 테넌트 내의 데이터 팩터리에서만 작동합니다.

* Azure AD [게스트 사용자의](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)경우 검색 키워드를 사용하여 모든 데이터 팩터리를 나열하는 UI의 검색 기능이 작동하지 [않습니다.](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) 그러나 게스트 사용자가 데이터 팩터리의 소유자인 한 검색 기능 없이 IR을 공유할 수 있습니다. IR을 공유해야 하는 데이터 팩터리의 관리되는 ID의 경우 **권한 할당** 상자에 해당 관리 ID를 입력하고 데이터 팩터리 UI에 **추가를** 선택합니다.

  > [!NOTE]
  > 이 기능은 데이터 팩터리 V2에서만 사용할 수 있습니다.

## <a name="notification-area-icons-and-notifications"></a>알림 영역 아이콘 및 알림

알림 영역에서 아이콘이나 메시지 위로 커서를 이동하면 자체 호스팅 통합 런타임의 상태에 대한 세부 정보를 볼 수 있습니다.

![알림 영역의 알림](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>포트 및 방화벽

고려해야 할 두 가지 방화벽이 있습니다.

- 조직의 중앙 라우터에서 실행되는 *회사 방화벽*
- 자체 호스팅 통합 런타임이 설치된 로컬 컴퓨터에서 데몬으로 구성된 *Windows 방화벽*

![방화벽](media/create-self-hosted-integration-runtime/firewall.png)

회사 방화벽 수준에서는 다음 도메인 및 아웃바운드 포트를 구성해야 합니다.

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

Windows 방화벽 수준 또는 컴퓨터 수준에서 이러한 아웃바운드 포트는 일반적으로 활성화됩니다. 그렇지 않은 경우 자체 호스팅 통합 런타임 컴퓨터에서 도메인 및 포트를 구성할 수 있습니다.

> [!NOTE]
> 원본 및 싱크에 따라 회사 방화벽 또는 Windows 방화벽에서 추가 도메인 및 아웃바운드 포트를 허용해야 할 수 있습니다.
>
> Azure SQL Database 및 Azure Data Lake와 같은 일부 클라우드 데이터베이스의 경우 방화벽 구성에서 자체 호스팅 통합 런타임 컴퓨터의 IP 주소를 허용해야 할 수 있습니다.

### <a name="copy-data-from-a-source-to-a-sink"></a>원본에서 싱크로 데이터 복사

회사 방화벽, 자체 호스팅 통합 런타임 컴퓨터의 Windows 방화벽 및 데이터 저장소 자체에서 방화벽 규칙을 제대로 사용하도록 설정해야 합니다. 이러한 규칙을 사용하면 자체 호스팅 통합 런타임이 원본과 싱크 모두에 성공적으로 연결할 수 있습니다. 복사 작업과 관련된 각 데이터 저장소에 대해 규칙을 사용하도록 설정합니다.

예를 들어 온-프레미스 데이터 저장소에서 SQL Database 싱크 또는 Azure SQL 데이터 웨어하우스 싱크로 복사하려면 다음 단계를 수행합니다.

1. Windows 방화벽과 회사 방화벽 모두에 대해 포트 1433에서 아웃바운드 TCP 통신을 허용합니다.
1. SQL Database의 방화벽 설정을 구성하여 자체 호스팅 통합 런타임 컴퓨터의 IP 주소를 허용된 IP 주소 목록에 추가합니다.

> [!NOTE]
> 방화벽에서 아웃바운드 포트 1433을 허용하지 않는 경우 자체 호스팅 통합 런타임이 SQL 데이터베이스에 직접 액세스할 수 없습니다. 이 경우 [준비된 복사본을](copy-activity-performance.md) SQL 데이터베이스 및 SQL 데이터 웨어하우스에 사용할 수 있습니다. 이 시나리오에서는 데이터 이동에 대 한 HTTPS (포트 443)만 필요 합니다.

## <a name="proxy-server-considerations"></a>프록시 서버 고려 사항

회사 네트워크 환경에서 프록시 서버를 사용하여 인터넷에 액세스하는 경우 자체 호스팅 통합 런타임이 적절한 프록시 설정을 사용하도록 구성합니다. 초기 등록 단계에서 프록시를 설정할 수 있습니다.

![프록시 지정](media/create-self-hosted-integration-runtime/specify-proxy.png)

구성될 때 자체 호스팅 통합 런타임은 프록시 서버를 사용하여 HTTP 또는 HTTPS 프로토콜을 사용하는 클라우드 서비스의 원본 및 대상에 연결합니다. 따라서 초기 설정 중에 **링크 변경을** 선택합니다.

![프록시 설정](media/create-self-hosted-integration-runtime/set-http-proxy.png)

이 대화 상자에는 세 가지 구성 옵션이 있습니다.

- **프록시 사용 안:** 자체 호스팅 통합 런타임은 프록시를 명시적으로 사용하여 클라우드 서비스에 연결하지 않습니다.
- **시스템 프록시 사용**: 자체 호스팅 통합 런타임은 diahost.exe.config 및 diawp.exe.config에서 구성된 프록시 설정을 사용합니다. 이러한 파일이 프록시 구성을 지정하지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결됩니다.
- **사용자 지정 프록시 사용**: diahost.exe.config 및 diawp.exe.config에서 구성을 사용하는 대신 자체 호스팅 통합 런타임에 사용하도록 HTTP 프록시 설정을 구성합니다. **주소** 및 **포트** 값이 필요합니다. **사용자 이름** 및 **암호** 값은 프록시의 인증 설정에 따라 선택 사항입니다. 모든 설정은 자체 호스팅 통합 런타임d에서 Windows DPAPI를 사용하여 암호화되며 컴퓨터에 로컬로 저장됩니다.

업데이트된 프록시 설정을 저장한 후 통합 런타임 호스트 서비스가 자동으로 다시 시작됩니다.

자체 호스팅 통합 런타임을 등록한 후 프록시 설정을 보거나 업데이트하려면 Microsoft 통합 런타임 구성 관리자를 사용합니다.

1. **Microsoft 통합 런타임 구성 관리자**를 엽니다.
1. **설정** 탭을 선택합니다.
1. **HTTP 프록시에서** **변경** 링크를 선택하여 HTTP **프록시 설정** 대화 상자를 엽니다.
1. **다음**을 선택합니다. 그런 다음 프록시 설정을 저장하고 통합 런타임 호스트 서비스를 다시 시작할 수 있는 권한을 요청하는 경고가 표시됩니다.

구성 관리자 도구를 사용하여 HTTP 프록시를 보고 업데이트할 수 있습니다.

![프록시 보기 및 업데이트](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> NTLM 인증을 통해 프록시 서버를 설정하면 통합 런타임 호스트 서비스가 도메인 계정에서 실행됩니다. 나중에 도메인 계정의 암호를 변경하는 경우 서비스의 구성 설정을 업데이트하고 서비스를 다시 시작해야 합니다. 이 요구 사항 때문에 암호를 자주 업데이트할 필요가 없는 전용 도메인 계정을 사용하여 프록시 서버에 액세스하는 것이 좋습니다.

### <a name="configure-proxy-server-settings"></a>프록시 서버 설정 구성

HTTP 프록시에 대한 **시스템 프록시 사용** 옵션을 선택하면 자체 호스팅 통합 런타임은 diahost.exe.config 및 diawp.exe.config의 프록시 설정을 사용합니다. 이러한 파일이 프록시를 지정하지 않으면 자체 호스팅 통합 런타임은 프록시를 거치지 않고 클라우드 서비스에 직접 연결됩니다. 다음 절차에서는 diahost.exe.config 파일을 업데이트하는 지침을 제공합니다.

1. 파일 탐색기에서 C:\프로그램 파일\Microsoft 통합 런타임\3.0\shared\diahost.exe.config를 원본 파일의 백업으로 안전하게 복사합니다.
1. 관리자로 실행되는 메모장을 엽니다.
1. 메모원에서 텍스트 파일 C:\프로그램 파일\Microsoft 통합 런타임\3.0\shared\diahost.exe.config를 엽니다.
1. 다음 코드와 같이 기본 **system.net** 태그를 찾습니다.

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    그러면 다음 예제와 같이 프록시 서버 세부 정보를 추가할 수 있습니다.

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    프록시 태그를 사용하면 추가 속성에서 `scriptLocation`와 같은 필수 설정을 지정할 수 있습니다. 구문은 [ \<프록시\> 요소(네트워크 설정)를](https://msdn.microsoft.com/library/sa91de1e.aspx) 참조하십시오.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. 구성 파일을 원래 위치에 저장합니다. 그런 다음 변경 내용을 선택하는 자체 호스팅 통합 런타임 호스트 서비스를 다시 시작합니다.

   서비스를 다시 시작하려면 제어판에서 서비스 애플릿을 사용합니다. 또는 통합 런타임 구성 관리자에서 **서비스 중지** 단추를 선택한 후 **서비스 시작**을 선택합니다.

   서비스가 시작되지 않으면 편집한 응용 프로그램 구성 파일에 잘못된 XML 태그 구문이 추가된 것일 수 있습니다.

> [!IMPORTANT]
> diahost.exe.config 및 diawp.exe.config를 둘 다 업데이트해야 합니다.

또한 Microsoft Azure가 회사의 허용 목록에 있는지 확인해야 합니다. [Microsoft 다운로드 센터에서](https://www.microsoft.com/download/details.aspx?id=41653)유효한 Azure IP 주소 목록을 다운로드할 수 있습니다.

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>방화벽 및 프록시 서버와 관련된 문제에 대한 가능한 증상

다음과 같은 오류 메시지가 표시되면 방화벽 또는 프록시 서버의 부적절한 구성일 수 있습니다. 이러한 구성은 자체 호스팅 통합 런타임이 데이터 팩터리에 연결하여 자체 인증하는 것을 방지합니다. 이전 섹션을 참조하여 방화벽 및 프록시 서버가 올바르게 구성되었는지 확인합니다.

* 자체 호스팅 통합 런타임을 등록하려고 하면 다음과 같은 오류 메시지가 나타납니다. 인증 키가 유효하고 통합 서비스 호스트 서비스가 이 컴퓨터에서 실행 중이면 확인합니다."
* 통합 런타임 구성 관리자를 열 때 상태가 **연결 끊김** 또는 **연결 중**으로 표시됩니다. Windows 이벤트 로그를 볼 때 **이벤트 뷰어** > **응용 프로그램 및 서비스 로그** > **Microsoft 통합 런타임에서**다음과 같은 오류 메시지가 표시됩니다.

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>인트라넷에서 원격 액세스 사용

PowerShell을 사용하여 자체 호스팅 통합 런타임을 설치한 위치 이외의 네트워크 컴퓨터에서 자격 증명을 암호화하는 경우 **인트라넷에서 원격 액세스를** 사용하도록 설정할 수 있습니다. PowerShell을 실행하여 자체 호스팅 통합 런타임을 설치한 컴퓨터에서 자격 증명을 암호화하는 경우 **인트라넷에서 원격 액세스를**사용하도록 설정할 수 없습니다.

고가용성과 확장성을 위해 다른 노드를 추가하기 전에 **인트라넷에서 원격 액세스를** 활성화합니다.  

자체 호스팅 통합 런타임 설정 버전 3.3 이상을 실행하는 경우 기본적으로 자체 호스팅 통합 런타임 설치 프로그램은 자체 호스팅 통합 런타임 컴퓨터에서 **인트라넷에서 원격 액세스를** 사용하지 않도록 설정합니다.

파트너 또는 다른 사람의 방화벽을 사용하는 경우 포트 8060 또는 사용자가 구성한 포트를 수동으로 열 수 있습니다. 자체 호스팅 통합 런타임을 설정하는 동안 방화벽 문제가 있는 경우 다음 명령을 사용하여 방화벽을 구성하지 않고 자체 호스팅 통합 런타임을 설치합니다.

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

자체 호스팅 통합 런타임 컴퓨터에서 포트 8060을 열지 않도록 선택한 경우 자격 증명 설정 응용 프로그램 이외의 메커니즘을 사용하여 데이터 저장소 자격 증명을 구성합니다. 예를 들어 **New-AzDataFactoryV2LinkedService암호화된 자격 증명** 파워쉘 cmdlet을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

단계별 지침은 [자습서: 온-프레미스 데이터를 클라우드에 복사합니다.](tutorial-hybrid-copy-powershell.md)
