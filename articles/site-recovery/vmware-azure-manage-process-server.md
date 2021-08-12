---
title: Azure Site Recovery에서 VMware VM/물리적 서버 재해 복구를 위한 프로세스 서버 관리
description: 이 문서에서는 Azure Site Recovery를 사용하여 VMware VM/물리적 서버를 재해 복구하기 위한 프로세스 서버를 관리하는 방법을 설명합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: a547a874c42d06d8453b154847561d8b5f0dabb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019198"
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

이 문서에서는 Site Recovery 프로세스 서버를 관리하기 위한 일반적인 작업을 설명합니다.

프로세스 서버는 복제 데이터를 수신, 최적화하고, Azure로 전송하는 데 사용됩니다. 또한 복제하려는 VMware VM 및 물리적 서버에서 Mobility Service의 밀어 넣기 설치를 수행하고 온-프레미스 머신의 자동 검색을 수행합니다. 온-프레미스 VMware VM 또는 물리적 서버를 Azure에 복제하기 위해 프로세스 서버는 기본적으로 구성 서버 머신에 설치됩니다. 

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- Azure에서 온-프레미스로 장애 복구하려면 Azure에서 임시 프로세스 서버를 설정해야 합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

프로세스 서버에 대해 자세히 알아보세요.


## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

프로세스 서버를 온-프레미스로 배포하거나 장애 복구용 Azure VM으로 배포하는 경우 최신 버전의 프로세스 서버가 설치됩니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다. 다음과 같이 프로세스 서버를 업그레이드할 수 있습니다.

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>VM을 이동하여 프로세스 서버 부하 분산

다음과 같이 두 프로세스 서버 간에 VM을 이동하여 부하를 분산합니다.

1. 자격 증명 모음의 **관리** 에서 **Site Recovery 인프라** 를 클릭합니다. **VMware 및 물리적 머신용** 에서 **구성 서버** 를 클릭합니다.
2. 프로세스 서버가 등록된 구성 서버를 클릭합니다.
3. 트래픽 부하를 분산하려는 프로세스 서버를 클릭합니다.

    ![트래픽 부하를 분산할 수 있는 프로세스 서버를 보여 주는 스크린샷](media/vmware-azure-manage-process-server/LoadBalance.png)

4. **부하 분산** 을 클릭하고 머신을 이동하려는 대상 프로세스 서버를 선택합니다. 그런 다음, **확인** 을 클릭합니다.

    ![대상 프로세스 서버가 선택된 부하 분산 창을 보여 주는 스크린샷](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **머신 선택** 을 클릭하고 현재 프로세스 서버에서 대상 프로세스 서버로 이동하려는 머신을 선택합니다. 평균 데이터 변경의 세부 정보는 각 가상 머신에 대해 표시됩니다. 그런 후 **OK** 를 클릭합니다. 
3. 자격 증명 모음의 **모니터링** > **Site Recovery 작업** 에서 작업 진행률을 모니터링합니다.

변경 내용이 포털에 반영되는 데 약 15분이 소요됩니다. 더 빠르게 적용하려면 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>전체 워크로드를 또 다른 프로세스 서버로 전환

다음과 같이 프로세스 서버에서 처리되는 전체 워크로드를 다른 프로세스 서버로 이동합니다.

1. 자격 증명 모음의 **관리** 에서 **Site Recovery 인프라** 를 클릭합니다. **VMware 및 물리적 머신용** 에서 **구성 서버** 를 클릭합니다.
2. 프로세스 서버가 등록된 구성 서버를 클릭합니다.
3. 워크로드를 전환하려는 원본 프로세스 서버를 클릭합니다.
4. **전환** 을 클릭하고 워크로드를 이동하려는 대상 프로세스 서버를 선택합니다. 그런 다음, **확인** 을 클릭합니다.

    ![대상 프로세스 서버 선택 창을 보여 주는 스크린샷](media/vmware-azure-manage-process-server/Switch.PNG)

5. 자격 증명 모음의 **모니터링** > **Site Recovery 작업** 에서 작업 진행률을 모니터링합니다.

변경 내용이 포털에 반영되는 데 약 15분이 소요됩니다. 더 빠르게 적용하려면 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>마스터 대상 서버 등록

마스터 대상 서버는 구성 서버 및 스케일 아웃 프로세스 서버에 있습니다. 구성 서버에 등록되어야 합니다. 이 등록에 오류가 있는 경우 보호된 항목의 상태에 영향을 줄 수 있습니다. 마스터 대상 서버를 구성 서버에 등록하려면 등록이 필요한 특정 구성 서버/스케일 아웃 프로세스 서버에 로그인합니다. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 관리자 명령 프롬프트에서 다음을 실행합니다.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>프로세스 서버 등록

다음과 같이 온-프레미스 또는 Azure VM에서 실행되는 프로세스 서버를 구성 서버에 다시 등록합니다.

[!INCLUDE [site-recovery-vmware-register-process-server](../../includes/site-recovery-vmware-register-process-server.md)]

설정을 저장한 후에 다음을 수행합니다.

1. 프로세스 서버에서 관리자 명령 프롬프트를 엽니다.
2. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 다음 명령을 실행합니다.

    ```
    cdpcli.exe --registermt
    net stop obengine
    net start obengine
    ```

## <a name="modify-proxy-settings-for-an-on-premises-process-server"></a>온-프레미스 프로세스 서버에 대한 프록시 설정 수정

온-프레미스 프로세스 서버가 프록시를 사용하여 Azure에 연결하는 경우 다음과 같이 프록시 설정을 수정할 수 있습니다.

1. 프로세스 서버 머신에 로그인합니다. 
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. **%PROGRAMDATA%\ASR\Agent** 폴더로 이동하고 다음 명령을 실행합니다.
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>프로세스 서버 제거

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>바이러스 백신 소프트웨어에서 폴더 제외

바이러스 백신 소프트웨어가 스케일 아웃 프로세스 서버(또는 마스터 대상 서버)에서 실행되는 경우 바이러스 백신 작업에서 다음 폴더를 제외합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 프로세스 서버 설치 디렉터리. 예: C:\Program Files (x86)\Microsoft Azure Site Recovery