---
title: VMware Vm 및 물리적 서버에서 Azure Site Recovery를 사용 하 여 Azure로의 재해 복구에 사용 되는 프로세스 서버 관리 | Microsoft Docs
description: 이 아티클에서 VMware Vm 및 물리적 서버에서 Azure Site Recovery를 사용 하 여 Azure로의 재해 복구를 설정 하는 프로세스 서버를 관리 합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 2c27779719c73adf4d7fc1a61a0c77d03df71815
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925590"
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

이 문서에서는 Site Recovery 프로세스 서버를 관리 하기 위한 일반적인 작업을 설명 합니다.

프로세스 서버는 수신, 최적화 및 Azure로 복제 데이터 전송에 사용 됩니다. 물리적 서버 복제 하려는 VMware Vm에 모바일 서비스의 강제 설치를 수행 하 고 온-프레미스 컴퓨터의 자동 검색을 수행 합니다. 온-프레미스 VMware Vm 또는 물리적 서버를 Azure로 복제 프로세스 서버가 구성 서버 컴퓨터에서 기본적으로 설치 됩니다. 

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- 온-프레미스로 Azure에서 장애 복구에 대해 Azure에서 임시 프로세스 서버를 설정 해야 합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

프로세스 서버에 대해 알아봅니다.


## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

프로세스 서버를 배포 하는 경우 온-프레미스로 장애 복구를 위해 Azure VM에 최신 버전의 프로세스 서버 설치 되어 있습니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다. 다음과 같이 프로세스 서버를 업그레이드할 수 있습니다.

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>프로세스 서버 부하를 분산 하는 Vm 이동

다음과 같이 두 프로세스 서버 간에 Vm을 이동 하 여 부하를 분산 합니다.

1. 자격 증명 모음에서 아래의 **관리** 클릭 **Site Recovery 인프라**합니다. 아래 **VMware 및 물리적 컴퓨터**, 클릭 **구성 서버**합니다.
2. 프로세스 서버는 등록 된 구성 서버를 클릭 합니다.
3. 프로세스 서버 트래픽 부하를 분산 하려면 클릭 합니다.

    ![부하 분산](media/vmware-azure-manage-process-server/LoadBalance.png)

4. 클릭 **부하를 분산**, 컴퓨터를 이동할 대상 프로세스 서버를 선택 합니다. 클릭 **확인**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. 클릭 **컴퓨터 선택**, 현재에서 대상 프로세스 서버로 이동할 컴퓨터를 선택 합니다. 평균 데이터 변경의 세부 정보는 각 가상 머신에 대해 표시됩니다. 그런 후 **OK**를 클릭합니다. 
3. 자격 증명 모음에서에서 작업의 진행률을 모니터링할 **모니터링** > **Site Recovery 작업**합니다.

변경 내용을 포털에 반영 되려면 약 15 분 정도 걸립니다. 더 빠르게 효과 [구성 서버를 새로 고칠](vmware-azure-manage-configuration-server.md#refresh-configuration-server)합니다.

## <a name="switch-an-entire-workload-to-another-process-server"></a>다른 프로세스 서버로 전체 워크 로드를 전환

다음과 같이 다른 프로세스 서버에 프로세스 서버에서 처리 된 전체 워크 로드를 이동 합니다.

1. 자격 증명 모음에서 아래의 **관리** 클릭 **Site Recovery 인프라**합니다. 아래 **VMware 및 물리적 컴퓨터**, 클릭 **구성 서버**합니다.
2. 프로세스 서버는 등록 된 구성 서버를 클릭 합니다.
3. 프로세스 서버는 워크 로드를 전환 하려면 클릭 합니다.
4. 클릭할 **스위치**, 워크 로드를 이동할 대상 프로세스 서버를 선택 합니다. 클릭 **확인**

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

5. 자격 증명 모음에서에서 작업의 진행률을 모니터링할 **모니터링** > **Site Recovery 작업**합니다.

변경 내용을 포털에 반영 되려면 약 15 분 정도 걸립니다. 더 빠르게 효과 [구성 서버를 새로 고칠](vmware-azure-manage-configuration-server.md#refresh-configuration-server)합니다.



## <a name="reregister-a-process-server"></a>프로세스 서버 등록

온-프레미스를 실행 하는 프로세스 서버를 다시 등록 하거나 다음과 같은 구성 서버를 사용 하 여 Azure VM에서:

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

Azure에 연결 하는 프록시를 사용 하는 온-프레미스 프로세스 서버를에 하는 경우 다음과 같이 프록시 설정을 수정할 수 있습니다.

1. 프로세스 서버 컴퓨터에 로그인 합니다. 
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. 폴더를 찾습니다 **%PROGRAMDATA%\ASR\Agent**,이 명령을 실행 합니다.
   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="remove-a-process-server"></a>프로세스 서버 제거

[!INCLUDE [site-recovery-vmware-unregister-process-server](../../includes/site-recovery-vmware-unregister-process-server.md)]

## <a name="exclude-folders-from-anti-virus-software"></a>바이러스 백신 소프트웨어에서 폴더를 제외

스케일 아웃 프로세스 서버 (또는 마스터 대상 서버)에서 바이러스 백신 소프트웨어가 실행 되는 경우 바이러스 백신 작업에서 다음 폴더를 제외 합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 프로세스 서버 설치 디렉터리입니다. 예를 들면 다음과 같습니다. C:\Program Files (x86)\Microsoft Azure Site Recovery