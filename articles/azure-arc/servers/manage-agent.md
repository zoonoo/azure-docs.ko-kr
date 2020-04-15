---
title: 서버용 Azure Arc 관리(미리 보기) 에이전트
description: 이 문서에서는 연결된 컴퓨터 에이전트에 대한 Azure Arc의 수명 주기 동안 일반적으로 수행할 다양한 관리 작업에 대해 설명합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308966"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>연결된 컴퓨터 에이전트 관리 및 유지 관리

Windows 또는 Linux용 Azure Arc를 처음 배포한 후 Windows 또는 Linux용 연결된 컴퓨터 에이전트를 다시 구성하거나 업그레이드하거나 수명 주기의 폐기 단계에 도달하면 컴퓨터에서 제거해야 할 수 있습니다. 이러한 일상적인 유지 관리 작업을 수동으로 또는 자동화를 통해 간단히 관리하여 조작 오류와 비용을 모두 줄일 수 있습니다.

## <a name="upgrading-agent"></a>업그레이드 에이전트

Windows 및 Linux용 Azure Connected Machine 에이전트는 요구 사항에 따라 수동으로 또는 자동으로 최신 릴리스로 업그레이드할 수 있습니다. 다음 표는 에이전트 업그레이드를 수행하기 위해 지원되는 메서드에 대해 설명합니다.

| 운영 체제 | 업그레이드 방법 |
|------------------|----------------|
| Windows | 수동<br> Windows 업데이트 |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [지퍼 ()지퍼 ()와 새끼](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| 레드 햇 엔터프라이즈, 아마존, 센트로스 리눅스 | [냠](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows 에이전트

Windows 컴퓨터의 에이전트를 최신 버전으로 업데이트하려면 Microsoft Update에서 에이전트를 사용할 수 있으며 기존 소프트웨어 업데이트 관리 프로세스를 사용하여 배포할 수 있습니다. 명령 프롬프트, 스크립트 또는 기타 자동화 솔루션 또는 실행하여 UI 마법사에서 수동으로 실행할 `AzureConnectedMachine.msi`수도 있습니다. 

> [!NOTE]
> * 에이전트를 업그레이드하려면 *관리자* 권한이 있어야 합니다.
> * 수동으로 업그레이드하려면 먼저 Installer 패키지를 다운로드하여 대상 서버의 폴더 또는 공유 네트워크 폴더에 복사해야 합니다. 

Windows Installer 패키지의 명령줄 옵션에 익숙하지 않은 경우 [Msiexec 표준 명령줄 옵션](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) 및 [Msiexec 명령줄 옵션을](https://docs.microsoft.com/windows/win32/msi/command-line-options)검토하십시오.

#### <a name="to-upgrade-using-the-setup-wizard"></a>설치 마법사를 사용하여 업그레이드하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. **AzureConnectedMachineAgent.msi를** 실행하여 설치 마법사를 시작합니다.

설치 마법사는 이전 버전이 있는지 검색한 다음 에이전트의 업그레이드를 자동으로 수행합니다. 업그레이드가 완료되면 설치 마법사가 자동으로 닫힙니까.

#### <a name="to-upgrade-from-the-command-line"></a>명령줄에서 업그레이드하려면

1. 관리 권한이 있는 계정으로 컴퓨터에 로그인합니다.

2. 에이전트를 자동으로 업그레이드하고 `C:\Support\Logs` 폴더에 설치 로그 파일을 만들려면 다음 명령을 실행합니다.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux 에이전트

Linux 컴퓨터의 에이전트를 최신 버전으로 업데이트하려면 두 가지 명령이 포함됩니다. 하나의 명령은 리포지토리에서 사용 가능한 최신 패키지 목록으로 로컬 패키지 인덱스를 업데이트하고 다른 명령은 로컬 패키지를 업그레이드합니다. 

> [!NOTE]
> 에이전트를 업그레이드하려면 *루트* 액세스 권한이 있거나 Sudo를 사용하여 권한이 높은 계정이 있어야 합니다.

#### <a name="upgrade-ubuntu"></a>업그레이드 우분투

1. 리포지토리에서 변경한 최신 변경 내용으로 로컬 패키지 인덱스를 업데이트하려면 다음 명령을 실행합니다.

    ```bash
    apt update
    ```

2. 시스템을 업그레이드하려면 다음 명령을 실행합니다.

    ```bash
    apt upgrade
    ```

패키지 설치 및 제거와 같은 [apt](https://help.ubuntu.com/lts/serverguide/apt.html) 명령의 작업은 `/var/log/dpkg.log` 로그 파일에 기록됩니다.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>업그레이드 레드 햇 / 센트OS / 아마존 리눅스

1. 리포지토리에서 변경한 최신 변경 내용으로 로컬 패키지 인덱스를 업데이트하려면 다음 명령을 실행합니다.

    ```bash
    yum check-update
    ```

2. 시스템을 업그레이드하려면 다음 명령을 실행합니다.

    ```bash
    yum update
    ```

패키지 설치 및 제거와 같은 [yum](https://access.redhat.com/articles/yum-cheat-sheet) 명령의 작업은 `/var/log/yum.log` 로그 파일에 기록됩니다. 

#### <a name="upgrade-suse-linux-enterprise"></a>업그레이드 수세 리눅스 엔터프라이즈

1. 리포지토리에서 변경한 최신 변경 내용으로 로컬 패키지 인덱스를 업데이트하려면 다음 명령을 실행합니다.

    ```bash
    zypper refresh
    ```

2. 시스템을 업그레이드하려면 다음 명령을 실행합니다.

    ```bash
    zypper update
    ```

패키지 설치 및 제거와 같은 [zypper](https://en.opensuse.org/Portal:Zypper) 명령의 작업은 `/var/log/zypper.log` 로그 파일에 기록됩니다. 

## <a name="about-the-azcmagent-tool"></a>아즈cmagent 도구 소개

Azcmagent 도구(Azcmagent.exe)는 설치 하는 동안 연결된 컴퓨터 에이전트에 대 한 Azure Arc를 구성 하거나 설치 후 에이전트의 초기 구성을 수정 하는 데 사용 됩니다. Azcmagent.exe는 에이전트를 사용자 지정하고 상태를 볼 수 있는 명령줄 매개 변수를 제공합니다.

* **연결** - 컴퓨터를 Azure Arc에 연결하려면

* **연결 끊기** - Azure Arc에서 컴퓨터 연결을 끊습니다.

* **다시 연결** - 연결이 끊긴 컴퓨터를 Azure Arc에 다시 연결하려면

* **Show** Show - 에이전트 문제 해결 시 도움이 되는 에이전트 상태 및 구성 속성 보기(리소스 그룹 이름, 멤버쉽 ID, 버전 등).

* **-h 또는 --help** - 사용 가능한 명령줄 매개 변수 표시

    예를 들어 **다시 연결** 매개 변수에 대한 `azcmagent reconnect -h`자세한 도움말을 보려면 을 입력합니다. 

* **-v 또는 --자세한** 내용 - 자세한 로깅 사용

대화식으로 로그온하는 동안 **연결,** **연결 끊기**및 **다시 연결을** 수행하거나 여러 에이전트를 온보딩하는 데 사용한 것과 동일한 서비스 주체를 사용하거나 Microsoft ID 플랫폼 [액세스 토큰을](../../active-directory/develop/access-tokens.md)사용하여 자동화할 수 있습니다. 서비스 주체를 사용하여 서버용 Azure Arc에 컴퓨터를 등록하지 않은 경우(미리 보기) 다음 [문서를](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) 참조하여 서비스 주체를 만듭니다.

### <a name="connect"></a>연결

이 매개 변수는 Azure에서 만든 컴퓨터를 나타내는 Azure 리소스 관리자의 리소스를 지정합니다. 리소스가 지정된 구독 및 리소스 그룹에 있으며 컴퓨터에 대한 데이터는 `--location` 설정에서 지정한 Azure 지역에 저장됩니다. 기본 리소스 이름은 지정하지 않은 경우 이 컴퓨터의 호스트 이름입니다.

그런 다음 시스템의 시스템에서 할당된 ID에 해당하는 인증서를 다운로드하여 로컬로 저장합니다. 이 단계가 완료되면 Azure 연결된 컴퓨터 메타데이터 서비스 및 게스트 구성 에이전트가 서버에 대한 Azure Arc와 동기화를 시작합니다(미리 보기).

서비스 주체를 사용하여 연결하려면 다음 명령을 실행합니다.

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

액세스 토큰을 사용하여 연결하려면 다음 명령을 실행합니다.

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

높은 로그온 자격 증명(대화형)과 연결하려면 다음 명령을 실행합니다.

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>연결 끊기

이 매개 변수는 컴퓨터를 나타내는 Azure 리소스 관리자의 리소스가 Azure에서 삭제됨을 지정합니다. 그것은 컴퓨터에서 에이전트를 삭제하지 않습니다,이 별도의 단계로 수행해야합니다. 컴퓨터의 연결이 끊어진 후 서버용 Azure Arc(미리 보기)에 다시 `azcmagent connect` 등록하려면 Azure에서 새 리소스를 만들 수 있도록 사용합니다.

서비스 주체를 사용하여 연결을 끊어지려면 다음 명령을 실행합니다.

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

액세스 토큰을 사용하여 연결을 끊려면 다음 명령을 실행합니다.

`azcmagent disconnect --access-token <accessToken>`

높은 로그온 자격 증명(대화형)과 연결을 끊려면 다음 명령을 실행합니다.

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>다시 연결

이 매개 변수는 이미 등록된 또는 연결된 컴퓨터를 서버용 Azure Arc(미리 보기)와 다시 연결합니다. 인증서가 만료되려면 최소 45일 동안 기기를 꺼놓은 경우 이 시스템이 필요할 수 있습니다. 이 매개 변수는 제공된 인증 옵션을 사용하여 이 컴퓨터를 나타내는 Azure Resource Manager 리소스에 해당하는 새 자격 증명을 검색합니다.

이 명령은 Azure 연결된 [컴퓨터 온보딩](overview.md#required-permissions) 역할보다 높은 권한이 필요합니다.

서비스 주체를 사용하여 다시 연결하려면 다음 명령을 실행합니다.

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

액세스 토큰을 사용하여 다시 연결하려면 다음 명령을 실행합니다.

`azcmagent reconnect --access-token <accessToken>`

높은 로그온 자격 증명(대화형)으로 다시 연결하려면 다음 명령을 실행합니다.

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>에이전트 제거

다음 방법 중 하나를 수행하여 컴퓨터에서 Windows 또는 Linux 연결된 컴퓨터 에이전트를 제거합니다. 에이전트를 제거해도 서버용 Arc(미리 보기)로 컴퓨터를 등록 취소하지 않으므로 Azure에서 컴퓨터를 더 이상 관리할 필요가 없을 때 수행하는 별도의 프로세스입니다.

### <a name="windows-agent"></a>Windows 에이전트

다음 두 방법 모두 에이전트를 제거하지만 컴퓨터에서 *C:\프로그램 파일\AzureConnectedMachineAgent* 폴더를 제거하지는 않습니다.

#### <a name="uninstall-from-control-panel"></a>제어판에서 제거

1. 머신에서 Windows 에이전트를 제거하려면 다음을 수행합니다.

    a. 관리자 권한이 있는 계정으로 컴퓨터에 로그인합니다.  
    b. **제어판**에서 **프로그램 및 기능**을 선택합니다.  
    다. **프로그램 및 기능**에서 **Azure Connected Machine 에이전트**, ** 제거**, **예**를 차례로 선택합니다.  

    >[!NOTE]
    > **AzureConnectedMachineAgent.msi** 설치 관리자 패키지를 두 번 클릭하여 에이전트 설정 마법사를 실행할 수도 있습니다.

#### <a name="uninstall-from-the-command-line"></a>명령줄에서 제거

명령 프롬프트에서 에이전트를 수동으로 제거하거나 스크립트와 같은 자동화된 메서드를 사용하려면 다음 예제를 사용할 수 있습니다. 먼저 운영 체제에서 응용 프로그램 패키지의 주 식별자인 GUID인 제품 코드를 검색해야 합니다. 제거는 Msiexec.exe 명령줄을 사용하여 수행됩니다. `msiexec /x {Product Code}`
    
1. 레지스트리 편집기를 엽니다.

2. `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` 레지스트리 키 아래에서 제품 코드 GUID를 찾아 복사합니다.

3. 그런 다음 다음 예제를 사용하여 Msiexec을 사용하여 에이전트를 제거할 수 있습니다.

   * 명령줄 유형에서:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * PowerShell을 사용하여 동일한 단계를 수행할 수 있습니다.

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux 에이전트

> [!NOTE]
> 에이전트를 제거하려면 *루트* 액세스 권한이 있거나 Sudo를 사용하여 권한이 높은 계정이 있어야 합니다.

Linux 에이전트를 제거하려면 사용할 명령은 Linux 운영 체제에 따라 다릅니다.

- 우분투의 경우 다음 명령을 실행합니다.

    ```bash
    sudo apt purge azcmagent
    ```

- RHEL, CentOS 및 Amazon Linux의 경우 다음 명령을 실행합니다.

    ```bash
    sudo yum remove azcmagent
    ```

- SLES의 경우 다음 명령을 실행합니다.

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>컴퓨터 등록 취소

Azure에서 지원 서비스를 사용하여 컴퓨터 관리를 중지하려는 경우 다음 단계를 수행하여 서버에 대한 Arc for server(미리 보기)를 사용하여 컴퓨터를 등록 취소합니다. 컴퓨터에서 연결된 컴퓨터 에이전트를 제거하기 전이나 후에 이러한 단계를 수행할 수 있습니다.

1. [Azure Portal](https://aka.ms/hybridmachineportal)로 이동하여 서버용 Azure Arc를 엽니다.

2. 목록에서 머신을 선택하고, 줄임표 (**...**)를 선택한 다음, **삭제**를 선택합니다.
