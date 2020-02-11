---
title: Azure Portal에서 Azure에 하이브리드 머신 연결
description: 이 문서에서는 Azure Portal에서 서버용 Azure Arc(미리 보기)를 사용하여 에이전트를 설치하고 머신을 Azure에 연결하는 방법을 설명합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 81083a9d94f782201a8eb765ac1f88093c0337c4
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024095"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Azure Portal에서 Azure에 하이브리드 머신 연결

일단의 세트를 수동으로 수행하여 환경에서 하나 이상 적은 수의 Windows 또는 Linux 머신에 대해 서버용 Azure Arc(미리 보기)를 사용하도록 설정할 수 있습니다. 또는 제공된 템플릿 스크립트를 실행하여 자동화된 방법을 사용할 수 있습니다. 이 스크립트는 두 에이전트의 다운로드 및 설치를 자동화합니다.

이 방법을 사용하려면 머신에 대한 관리자 권한으로 에이전트를 설치하고 구성할 수 있어야 합니다. Linux에서는 루트 계정을 사용하여 수행하고, Windows에서는 로컬 관리자 그룹의 멤버로 수행해야 합니다.

시작하려면 먼저 [사전 요구 사항](overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure Portal에서 설치 스크립트 생성

다운로드 및 설치를 자동화하고 Azure Arc와의 연결을 설정하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료하려면 다음을 수행합니다.

1. 브라우저에서 [Azure Portal](https://aka.ms/hybridmachineportal)로 이동합니다.

1. **머신 - Azure Arc** 페이지의 왼쪽 위에서 **추가**를 선택하거나 가운데 창의 아래쪽에서 **머신 만들기 - Azure Arc** 옵션을 선택합니다. 

1. **메서드 선택** 페이지에서 **대화형 스크립트를 사용하여 머신 추가** 타일, **스크립트 생성**을 차례로 선택합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다.

    >[!NOTE]
    >서버용 Azure Arc(미리 보기)는 다음 지역만 지원합니다.
    >- WestUS2
    >- WestEurope
    >- WestAsia
    >

1. **스크립트 생성** 페이지의 **운영 체제** 드롭다운 목록에서 스크립트를 실행할 운영 체제를 선택합니다.

1. 머신에서 프록시 서버를 통해 통신하여 인터넷에 연결하는 경우 **다음: 프록시 서버**를 선택합니다. 
1. **프록시 서버** 탭에서 머신에서 프록시 서버와 통신하는 데 사용할 프록시 서버 IP 주소 또는 이름 및 포트 번호를 지정합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다. 
1. **검토 + 생성**을 선택합니다.

1. **검토 + 생성** 탭에서 요약 정보를 검토한 다음, **다운로드**을 선택합니다. 그래도 변경해야 하는 경우 **이전**을 선택합니다.

## <a name="install-and-validate-the-agent-on-windows"></a>Windows에서 에이전트 설치 및 유효성 검사

### <a name="install-manually"></a>수동 설치
*AzureConnectedMachineAgent.msi* Windows Installer 패키지를 실행하여 Connected Machine 에이전트를 수동으로 설치할 수 있습니다. 

> [!NOTE]
> * 에이전트를 설치하거나 제거하려면 *관리자* 권한이 있어야 합니다.
> * 먼저 설치 관리자 패키지를 다운로드하여 대상 서버의 폴더 또는 공유 네트워크 폴더에 복사해야 합니다. 옵션 없이 설치 관리자 패키지를 실행하면 에이전트를 대화형으로 설치하기 위해 수행할 수 있는 설치 마법사가 시작됩니다.

머신에서 프록시 서버를 통해 서비스와 통신해야 하는 경우 에이전트를 설치한 후에 이 문서의 뒷부분에서 설명하는 명령을 실행해야 합니다. 그러면 `https_proxy` 프록시 서버 시스템 환경 변수가 설정됩니다.

다음 표에는 명령줄에서 에이전트 설정을 통해 지원되는 매개 변수가 강조 표시되어 있습니다.

| 매개 변수 | Description |
|:--|:--|
| /? | 명령줄 옵션의 목록을 반환합니다. |
| /S | 사용자 상호 작용 없이 자동 설치를 수행합니다. |

예를 들어 `/?` 매개 변수를 사용하여 설치 프로그램을 실행하려면 `msiexec.exe /i AzureConnectedMachineAgent.msi /?`를 입력합니다.

Connected Machine 에이전트용 파일은 기본적으로 *C:\Program Files\AzureConnectedMachineAgent*에 설치됩니다. 설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. 로그 디렉터리는 *%Programfiles%\AzureConnectedMachineAgentAgent\logs*입니다.

### <a name="install-with-the-scripted-method"></a>스크립팅된 메서드를 사용하여 설치

1. 서버에 로그인합니다.

1. 관리자 권한 PowerShell 명령 프롬프트를 엽니다.

1. 스크립트를 복사한 폴더 또는 공유로 변경하고, `./OnboardingScript.ps1` 스크립트를 실행하여 서버에서 실행합니다.

### <a name="configure-the-agent-proxy-setting"></a>에이전트 프록시 설정 구성

프록시 서버 환경 변수를 설정하려면 다음 명령을 실행합니다.

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

>[!NOTE]
>이 미리 보기에서는 에이전트에서 프록시 인증 설정을 지원하지 않습니다.
>

### <a name="configure-agent-communication"></a>에이전트 통신 구성

에이전트가 설치되면 다음 명령을 실행하여 에이전트에서 Azure Arc 서비스와 통신하도록 구성해야 합니다.

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Linux에서 에이전트 설치 및 유효성 검사

Linux용 Connected Machine 에이전트는 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 호스팅되는 배포(.RPM 또는 .DEB)에 대한 기본 설정 패키지 형식으로 제공됩니다. [`Install_linux_azcmagent.sh` 셸 스크립트 번들](https://aka.ms/azcmagent)에서 수행하는 작업은 다음과 같습니다.

- packages.microsoft.com에서 에이전트 패키지를 다운로드하도록 호스트 머신을 구성합니다.
- 하이브리드 리소스 공급자 패키지를 설치합니다.

필요에 따라 `--proxy "{proxy-url}:{proxy-port}"` 매개 변수를 포함하여 에이전트를 프록시 정보로 구성할 수 있습니다.

또한 이 스크립트에는 지원되는 배포와 지원되지 않는 배포를 식별하는 논리가 포함되어 있으며 설치를 수행하는 데 필요한 권한을 확인합니다. 

다음 예제에서는 에이전트를 다운로드하여 설치합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

프록시 서버를 통해 통신하도록 에이전트를 구성하는 `--proxy` 매개 변수를 포함하여 에이전트를 다운로드하고 설치하려면 다음 명령을 실행합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-the-agent-communication"></a>에이전트 통신 구성

에이전트가 설치되면 다음 명령을 실행하여 Azure Arc 서비스와 통신하도록 구성합니다.

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc와의 연결 확인

에이전트가 설치되고 서버용 Azure Arc(미리 보기)에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>정리

서버용 Azure Arc(미리 보기)에서 머신의 연결을 끊으려면 다음을 수행합니다.

1. [Azure Portal](https://aka.ms/hybridmachineportal)로 이동하여 서버용 Azure Arc를 엽니다.

1. 목록에서 머신을 선택하고, 줄임표 ( **...** )를 선택한 다음, **삭제**를 선택합니다.

1. 머신에서 Windows 에이전트를 제거하려면 다음을 수행합니다.

    a. 관리자 권한이 있는 계정으로 컴퓨터에 로그인합니다.  
    b. **제어판**에서 **프로그램 및 기능**을 선택합니다.  
    다. **프로그램 및 기능**에서 **Azure Connected Machine 에이전트**,  **제거**, **예**를 차례로 선택합니다.  

    >[!NOTE]
    > **AzureConnectedMachineAgent.msi** 설치 관리자 패키지를 두 번 클릭하여 에이전트 설정 마법사를 실행할 수도 있습니다.

    제거를 스크립팅하려면 다음 예제를 사용할 수 있습니다. 이 예제에서는 Msiexecc.exe 명령줄인 `msiexec /x {Product Code}`를 사용하여 제품 코드를 검색하고 해당 에이전트를 제거합니다. 이렇게 하려면 다음을 수행합니다.  
    
    a. 레지스트리 편집기를 엽니다.  
    b. `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 레지스트리 키 아래에서 제품 코드 GUID를 찾아 복사합니다.  
    다. 그런 다음, Msiexec를 사용하여 에이전트를 제거할 수 있습니다.

    다음 예제에서는 에이전트를 제거하는 방법을 보여 줍니다.

    ```powershell
    Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
    Get-ItemProperty | `
    Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
    ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
    ```

1. Linux 에이전트를 제거하려면 다음 명령을 실행합니다.

      ```bash
      sudo apt purge hybridagent
      ```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [연결된 머신에 정책 할당](../../governance/policy/assign-policy-portal.md)
