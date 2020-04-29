---
title: 서버에 대 한 Azure Arc (미리 보기) 에이전트 관리
description: 이 문서에서는 서버에 연결 된 컴퓨터 에이전트에 대 한 Azure Arc의 수명 주기 동안 일반적으로 수행 하는 다양 한 관리 작업에 대해 설명 합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81308966"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>연결 된 컴퓨터 에이전트 관리 및 유지 관리

서버 (미리 보기)에 연결 된 Windows 또는 Linux 용 Azure Arc의 초기 배포 후에 에이전트를 다시 구성 하거나, 업그레이드 하거나, 해당 수명 주기의 사용 중지 단계에 도달한 경우 컴퓨터에서 제거 해야 할 수 있습니다. 이러한 일상적인 유지 관리 작업을 수동으로 또는 자동화를 통해 간단히 관리하여 조작 오류와 비용을 모두 줄일 수 있습니다.

## <a name="upgrading-agent"></a>에이전트 업그레이드

Windows 및 Linux용 Azure Connected Machine 에이전트는 요구 사항에 따라 수동 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 다음 표에서는 에이전트 업그레이드를 수행 하는 데 지원 되는 방법에 대해 설명 합니다.

| 운영 체제 | 업그레이드 방법 |
|------------------|----------------|
| Windows | 수동<br> Windows 업데이트 |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows 에이전트

Windows 컴퓨터의 에이전트를 최신 버전으로 업데이트 하려면 Microsoft 업데이트에서 에이전트를 사용할 수 있으며 기존 소프트웨어 업데이트 관리 프로세스를 사용 하 여 배포할 수 있습니다. 명령 프롬프트, 스크립트나 기타 자동화 솔루션 또는를 실행 `AzureConnectedMachine.msi`하 여 UI 마법사에서 수동으로 실행할 수도 있습니다. 

> [!NOTE]
> * 에이전트를 업그레이드 하려면 *관리자* 권한이 있어야 합니다.
> * 수동으로 업그레이드 하려면 먼저 설치 관리자 패키지를 대상 서버의 폴더 또는 공유 네트워크 폴더에 다운로드 하 여 복사 해야 합니다. 

Windows Installer 패키지에 대 한 명령줄 옵션에 익숙하지 않은 경우 [msiexec 표준 명령줄 옵션](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) 및 [msiexec 명령줄 옵션](https://docs.microsoft.com/windows/win32/msi/command-line-options)을 검토 하십시오.

#### <a name="to-upgrade-using-the-setup-wizard"></a>설치 마법사를 사용 하 여 업그레이드 하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **AzureConnectedMachineAgent** 를 실행 하 여 설치 마법사를 시작 합니다.

설치 마법사는 이전 버전이 있는지 검색 한 다음 자동으로 에이전트 업그레이드를 수행 합니다. 업그레이드가 완료 되 면 설치 마법사가 자동으로 닫힙니다.

#### <a name="to-upgrade-from-the-command-line"></a>명령줄에서 업그레이드 하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 에이전트를 자동으로 업그레이드 하 고 `C:\Support\Logs` 폴더에 설치 로그 파일을 만들려면 다음 명령을 실행 합니다.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 에이전트

Linux 컴퓨터의 에이전트를 최신 버전으로 업데이트 하기 위해 두 개의 명령이 필요 합니다. 저장소에서 사용 가능한 최신 패키지 목록으로 로컬 패키지 인덱스를 업데이트 하는 명령과 로컬 패키지를 업그레이드 하는 명령 하나가 있습니다. 

> [!NOTE]
> 에이전트를 업그레이드 하려면 *루트* 액세스 권한이 있거나 Sudo를 사용 하는 승격 된 권한이 있는 계정을 사용 해야 합니다.

#### <a name="upgrade-ubuntu"></a>Ubuntu 업그레이드

1. 리포지토리의 최신 변경 내용으로 로컬 패키지 인덱스를 업데이트 하려면 다음 명령을 실행 합니다.

    ```bash
    apt update
    ```

2. 시스템을 업그레이드 하려면 다음 명령을 실행 합니다.

    ```bash
    apt upgrade
    ```

패키지의 설치 및 제거와 같은 [apt](https://help.ubuntu.com/lts/serverguide/apt.html) 명령의 작업은 `/var/log/dpkg.log` 로그 파일에 기록 됩니다.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Red Hat/CentOS/Amazon Linux 업그레이드

1. 리포지토리의 최신 변경 내용으로 로컬 패키지 인덱스를 업데이트 하려면 다음 명령을 실행 합니다.

    ```bash
    yum check-update
    ```

2. 시스템을 업그레이드 하려면 다음 명령을 실행 합니다.

    ```bash
    yum update
    ```

패키지의 설치 및 제거와 같은 [yum](https://access.redhat.com/articles/yum-cheat-sheet) 명령의 동작은 `/var/log/yum.log` 로그 파일에 기록 됩니다. 

#### <a name="upgrade-suse-linux-enterprise"></a>SUSE Linux Enterprise 업그레이드

1. 리포지토리의 최신 변경 내용으로 로컬 패키지 인덱스를 업데이트 하려면 다음 명령을 실행 합니다.

    ```bash
    zypper refresh
    ```

2. 시스템을 업그레이드 하려면 다음 명령을 실행 합니다.

    ```bash
    zypper update
    ```

패키지의 설치 및 제거와 같은 [zypper](https://en.opensuse.org/Portal:Zypper) 명령의 작업은 `/var/log/zypper.log` 로그 파일에 기록 됩니다. 

## <a name="about-the-azcmagent-tool"></a>Azcmagent 도구 정보

Azcmagent 도구 (Azcmagent)는 설치 중에 서버 (미리 보기)에 연결 된 컴퓨터 에이전트에 대 한 Azure Arc를 구성 하거나 설치 후 에이전트의 초기 구성을 수정 하는 데 사용 됩니다. Azcmagent는 에이전트를 사용자 지정 하 고 해당 상태를 볼 수 있는 명령줄 매개 변수를 제공 합니다.

* **연결** -Azure Arc에 컴퓨터를 연결 하려면

* **연결 끊기** -Azure Arc에서 컴퓨터 연결을 끊으려면

* **다시 연결** -연결 되지 않은 컴퓨터를 Azure Arc에 다시 연결 하려면

* **표시** -에이전트와 관련 된 문제를 해결 하는 데 도움이 될 수 있는 에이전트 상태 및 해당 구성 속성 (리소스 그룹 이름, 구독 ID, 버전 등)을 표시 합니다.

* **-h 또는--help** -사용 가능한 명령줄 매개 변수를 표시 합니다.

    예를 들어 **다시 연결** 매개 변수에 대 한 자세한 도움말을 보려면 `azcmagent reconnect -h`을 입력 합니다. 

* **-v 또는--verbose** -자세한 정보 로깅 사용

대화형으로 로그온 하는 동안 수동으로 **연결**하거나 연결을 **끊고** **다시 연결** 하거나, 여러 에이전트를 등록 하는 데 사용한 것과 동일한 서비스 주체를 사용 하거나 Microsoft id 플랫폼 [액세스 토큰](../../active-directory/develop/access-tokens.md)을 사용 하 여 자동화할 수 있습니다. 서비스 주체를 사용 하 여 서버 (미리 보기) 용 Azure Arc에 컴퓨터를 등록 하지 않은 경우 서비스 주체를 만들려면 다음 [문서](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 를 참조 하세요.

### <a name="connect"></a>연결

이 매개 변수는 Azure에서 생성 되는 컴퓨터를 나타내는 Azure Resource Manager의 리소스를 지정 합니다. 리소스는 지정 된 구독 및 리소스 그룹에 있으며, 컴퓨터에 대 한 데이터는 `--location` 설정에 지정 된 Azure 지역에 저장 됩니다. 지정 하지 않으면 기본 리소스 이름은이 컴퓨터의 호스트 이름입니다.

그런 다음 컴퓨터의 시스템 할당 id에 해당 하는 인증서를 다운로드 하 여 로컬로 저장 합니다. 이 단계가 완료 되 면 Azure 연결 된 컴퓨터 Metadata Service 및 게스트 구성 에이전트가 서버 (미리 보기)에 대 한 Azure Arc와 동기화를 시작 합니다.

서비스 주체를 사용 하 여 연결 하려면 다음 명령을 실행 합니다.

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

액세스 토큰을 사용 하 여 연결 하려면 다음 명령을 실행 합니다.

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

관리자 권한으로 로그온 한 자격 증명 (대화형)을 사용 하 여 연결 하려면 다음 명령을 실행 합니다.

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>연결 끊기

이 매개 변수는 Azure에서 컴퓨터가 삭제 됨을 나타내는 Azure Resource Manager의 리소스를 지정 합니다. 컴퓨터에서 에이전트를 삭제 하지 않습니다. 별도의 단계로 수행 해야 합니다. 컴퓨터의 연결이 끊어진 후 서버에 대 한 Azure Arc (미리 보기)에 다시 등록 하려면를 사용 `azcmagent connect` 하 여 azure에서 새 리소스를 만듭니다.

서비스 주체를 사용 하 여 연결을 끊으려면 다음 명령을 실행 합니다.

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

액세스 토큰을 사용 하 여 연결을 끊으려면 다음 명령을 실행 합니다.

`azcmagent disconnect --access-token <accessToken>`

관리자 권한으로 로그온 한 자격 증명 (대화형)과의 연결을 끊으려면 다음 명령을 실행 합니다.

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>다시 연결

이 매개 변수는 이미 등록 된 컴퓨터 또는 연결 된 컴퓨터를 서버 (미리 보기)에 대 한 Azure Arc와 다시 연결 합니다. 인증서가 만료 될 때까지 컴퓨터가 45 일 이상 꺼져 있는 경우이 작업이 필요할 수 있습니다. 이 매개 변수는 제공 된 인증 옵션을 사용 하 여이 컴퓨터를 나타내는 Azure Resource Manager 리소스에 해당 하는 새 자격 증명을 검색 합니다.

이 명령에는 [Azure 연결 된 컴퓨터 온 보 딩](overview.md#required-permissions) 역할 보다 높은 권한이 필요 합니다.

서비스 주체를 사용 하 여 다시 연결 하려면 다음 명령을 실행 합니다.

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

액세스 토큰을 사용 하 여 다시 연결 하려면 다음 명령을 실행 합니다.

`azcmagent reconnect --access-token <accessToken>`

관리자 권한으로 로그온 한 자격 증명 (대화형)으로 다시 연결 하려면 다음 명령을 실행 합니다.

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>에이전트 제거

다음 방법 중 하나를 수행 하 여 컴퓨터에서 Windows 또는 Linux 연결 된 컴퓨터 에이전트를 제거 합니다. 에이전트를 제거 해도 서버 (미리 보기)에 대 한 Arc로 컴퓨터의 등록을 취소 하지 않습니다 .이는 Azure에서 컴퓨터를 더 이상 관리할 필요가 없는 경우 수행 하는 별도의 프로세스입니다.

### <a name="windows-agent"></a>Windows 에이전트

다음 메서드는 모두 에이전트를 제거 하지만 컴퓨터에서 *C:\Program Files\AzureConnectedMachineAgent* 폴더를 제거 하지는 않습니다.

#### <a name="uninstall-from-control-panel"></a>제어판에서 제거

1. 머신에서 Windows 에이전트를 제거하려면 다음을 수행합니다.

    a. 관리자 권한이 있는 계정으로 컴퓨터에 로그인합니다.  
    b. **제어판**에서 **프로그램 및 기능**을 선택합니다.  
    다. **프로그램 및 기능**에서 **Azure Connected Machine 에이전트**, ** 제거**, **예**를 차례로 선택합니다.  

    >[!NOTE]
    > **AzureConnectedMachineAgent.msi** 설치 관리자 패키지를 두 번 클릭하여 에이전트 설정 마법사를 실행할 수도 있습니다.

#### <a name="uninstall-from-the-command-line"></a>명령줄에서 제거

명령 프롬프트에서 에이전트를 수동으로 제거 하거나 스크립트와 같은 자동화 된 방법을 사용 하려면 다음 예제를 사용할 수 있습니다. 먼저 운영 체제에서 응용 프로그램 패키지의 주 식별자 인 GUID 인 제품 코드를 검색 해야 합니다. 제거는 Msiexec.exe 명령줄- `msiexec /x {Product Code}`를 사용 하 여 수행 됩니다.
    
1. 레지스트리 편집기를 엽니다.

2. `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 레지스트리 키 아래에서 제품 코드 GUID를 찾아 복사합니다.

3. 그런 다음 다음 예제를 사용 하 여 Msiexec.exe를 사용 하 여 에이전트를 제거할 수 있습니다.

   * 명령줄 형식에서 다음을 수행 합니다.

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * PowerShell을 사용 하 여 동일한 단계를 수행할 수 있습니다.

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 에이전트

> [!NOTE]
> 에이전트를 제거 하려면 Sudo를 사용 하 여 *루트* 액세스 권한이 나 승격 된 권한이 있는 계정을 사용 해야 합니다.

Linux 에이전트를 제거 하려면 사용할 명령은 Linux 운영 체제에 따라 달라 집니다.

- Ubuntu의 경우 다음 명령을 실행 합니다.

    ```bash
    sudo apt purge azcmagent
    ```

- RHEL, CentOS 및 Amazon Linux의 경우 다음 명령을 실행 합니다.

    ```bash
    sudo yum remove azcmagent
    ```

- SLES의 경우 다음 명령을 실행 합니다.

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>컴퓨터 등록 취소

Azure에서 지원 서비스를 사용 하 여 컴퓨터를 관리 하지 않으려는 경우 다음 단계를 수행 하 여 서버에 대 한 Arc (미리 보기)로 컴퓨터의 등록을 취소 합니다. 컴퓨터에서 연결 된 컴퓨터 에이전트를 제거 하기 전이나 후에 이러한 단계를 수행할 수 있습니다.

1. [Azure Portal](https://aka.ms/hybridmachineportal)로 이동하여 서버용 Azure Arc를 엽니다.

2. 목록에서 머신을 선택하고, 줄임표 (**...**)를 선택한 다음, **삭제**를 선택합니다.
