---
title: " Azure Site Recovery에서 확장 프로세스 서버 관리 | Microsoft Docs"
description: 이 문서에서는 Azure Site Recovery에서 확장 프로세스 서버를 설정하고 관리하는 방법을 설명합니다.
services: site-recovery
documentationcenter: ''
author: AnoopVasudavan
manager: gauravd
editor: ''
ms.assetid: ''
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: b2c2f8c6a10ca5098956de2402925bd9422212f8
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2018
ms.locfileid: "29767758"
---
# <a name="manage-a-scale-out-process-server"></a>확장 프로세스 서버 관리

확장 프로세스 서버는 Site Recovery 서비스와 온-프레미스 인프라 간의 데이터 전송에 대한 코디네이터로 작동합니다. 이 문서에서는 확장 프로세스 서버를 설정, 구성 및 관리하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
다음은 확장 프로세스 서버를 설정하는 데 필요한 권장되는 하드웨어, 소프트웨어 및 네트워크 구성입니다.

> [!NOTE]
> [용량 계획](site-recovery-capacity-planner.md)은 로드 요구 사항에 맞는 구성으로 확장 프로세스 서버를 배포하는지 확인하는 중요한 단계입니다. [확장 프로세스 서버에 대한 특성 크기 조정](#sizing-requirements-for-a-configuration-server)에 대해 자세히 알아봅니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-scale-out-process-server-software"></a>확장 프로세스 서버 소프트웨어 다운로드
1. Azure Portal에 로그온하고 Recovery Services 자격 증명 모음으로 이동합니다.
2. **Site Recovery 인프라** > **구성 서버**(VMware 및 물리적 컴퓨터용 아래)로 이동합니다.
3. 구성 서버를 선택하여 구성 서버의 세부 정보 페이지로 드릴다운합니다.
4. **+ 프로세스 서버** 단추를 클릭합니다.
5. **프로세스 서버 추가** 페이지의 **프로세스 서버를 배포할 위치 선택** 드롭다운에서 **온-프레미스로 확장 프로세스 서버 배포** 옵션을 선택합니다.

  ![서버 페이지 추가](./media/site-recovery-vmware-to-azure-manage-scaleout-process-server/add-process-server.png)
6. **Microsoft Azure Site Recovery 통합 설치 다운로드** 링크를 클릭하여 확장 프로세스 서버 설치의 최신 버전을 다운로드합니다.

  > [!WARNING]
  확장 프로세서 서버의 버전은 사용자 환경에서 실행 중인 구성 서버 버전과 동일하거나 낮아야 합니다. 버전 호환성을 보장하는 간단한 방법은 구성 서버를 설치/업데이트하는 데 최근에 사용한 동일한 설치 관리자 비트를 사용하는 것입니다.

## <a name="installing-and-registering-a-scale-out-process-server-from-gui"></a>GUI에서 확장 프로세스 서버 설치 및 등록
200대가 넘는 원본 컴퓨터로 배포 규모를 확장해야 하고 총 이탈률이 2TB를 초과하는 경우 트래픽 볼륨을 처리할 추가 프로세스가 필요합니다.

[프로세스 서버에 대한 크기 권장 사항](#size-recommendations-for-the-process-server)을 확인한 후 다음 지침에 따라 프로세스 서버를 설정합니다. 서버를 설정한 후 이를 사용하도록 원본 컴퓨터를 마이그레이션할 수 있습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-add-process-server.md)]


## <a name="installing-and-registering-a-scale-out-process-server-using-command-line"></a>명령줄을 사용하여 확장 프로세스 서버 설치 및 등록

```
UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
```

### <a name="sample-usage"></a>샘플 사용
```
MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
cd C:\Temp\Extracted
UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "PS" /InstallLocation "D:\" /EnvType "VMWare" /CSIP "10.150.24.119" /PassphraseFilePath "C:\Users\Administrator\Desktop\Passphrase.txt" /DataTransferSecurePort 443
```

### <a name="scale-out-process-server-installer-command-line-arguments"></a>확장 프로세스 서버 설치 관리자 명령줄 인수
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-proxy-settings-configuration-file"></a>프록시 설정 구성 파일 만들기
ProxySettingsFilePath 매개 변수는 입력으로 파일을 사용합니다. 다음 형식을 사용하여 파일을 만들고 입력 ProxySettingsFilePath 매개 변수로 전달합니다.
```
* [ProxySettings]
* ProxyAuthentication = "Yes/No"
* Proxy IP = "IP Address"
* ProxyPort = "Port"
* ProxyUserName="UserName"
* ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-scale-out-process-server"></a>확장 프로세스 서버에 대한 프록시 설정 수정
1. 확장 프로세스 서버에 로그인합니다.
2. 관리자 PowerShell 명령 창을 엽니다.
3. 다음 명령을 실행합니다.
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```
4. 그런 다음 디렉터리 **%PROGRAMDATA%\ASR\Agent**로 이동하고 다음 명령을 실행합니다.
  ```
  cmd
  cdpcli.exe --registermt

  net stop obengine

  net start obengine

  exit
  ```

## <a name="re-registering-a-scale-out-process-server"></a>확장 프로세스 서버 다시 등록
[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

* 그런 다음 관리자 명령 프롬프트를 엽니다.
* **%PROGRAMDATA%\ASR\Agent** 디렉터리로 이동하고 다음 명령을 실행합니다.

```
cdpcli.exe --registermt

net stop obengine

net start obengine
```

## <a name="upgrading-a-scale-out-process-server"></a>확장 프로세스 서버 업그레이드
[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

## <a name="decommissioning-a-scale-out-process-server"></a>확장 프로세스 서버 서비스 해제
1. 다음 사항을 확인합니다.
  - Azure Portal에서 구성 서버의 연결 상태가 **Connected**로 표시됨
  - 프로세스 서버는 여전히 구성 서버와 통신할 수 있습니다.
2. 관리자 권한으로 프로세스 서버에 로그인
3. 제어판 > 프로그램 > 프로그램 제거 열기
4. 다음 순서로 프로그램을 제거합니다.
  * Microsoft Azure Site Recovery 구성 서버/프로세스 서버
  * Microsoft Azure Site Recovery 구성 서버 종속성
  * Microsoft Azure Recovery Services 에이전트

프로세스 서버 삭제가 Azure Portal에 반영되는 데 최대 15분이 걸릴 수 있습니다.

  > [!NOTE]
  프로세스 서버가 구성 서버와 통신할 수 없는 경우(포털의 연결 상태가 Disconnected) 다음 단계를 따라 구성 서버에서 제거해야 합니다.

## <a name="unregistering-a-disconnected-scale-out-process-server-from-a-configuration-server"></a>구성 서버에서 연결이 끊긴 확장 프로세스 서버 등록 취소

[!INCLUDE [site-recovery-vmware-upgrade-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="sizing-requirements-for-a-scale-out-process-server"></a>확장 프로세스 서버에 대한 크기 조정 요구 사항

| **추가 프로세스 서버** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터** |
| --- | --- | --- | --- |
|4개 vCPU(2개 소켓 * 2코어 @ 2.5GHz), 8GB 메모리 |300GB |250GB 이하 |85대 이하의 컴퓨터를 복제합니다. |
|8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz), 12GB 메모리 |600GB |250GB ~ 1TB |85-150대 컴퓨터를 복제합니다. |
|12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz), 24GB 메모리 |1TB |1TB ~ 2TB |150-225대 컴퓨터를 복제합니다. |
