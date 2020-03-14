---
title: Azure Site Recovery에서 VMware Vm/물리적 서버 재해 복구를 위한 프로세스 서버 관리
description: 이 문서에서는 Azure Site Recovery를 사용 하 여 VMware v m/물리적 서버의 재해 복구를 위해 프로세스 서버를 관리 하는 방법을 설명 합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79257252"
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

이 문서에서는 Site Recovery 프로세스 서버를 관리 하는 일반적인 작업에 대해 설명 합니다.

프로세스 서버는 복제 데이터를 수신 하 고, 최적화 하 고, Azure로 전송 하는 데 사용 됩니다. 또한 복제 하려는 VMware Vm 및 물리적 서버에서 모바일 서비스의 푸시 설치를 수행 하 고 온-프레미스 컴퓨터의 자동 검색을 수행 합니다. 온-프레미스 VMware Vm 또는 물리적 서버를 Azure에 복제 하는 경우 프로세스 서버는 기본적으로 구성 서버 컴퓨터에 설치 됩니다. 

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- Azure에서 온-프레미스로 장애 복구 (failback) 하려면 Azure에서 임시 프로세스 서버를 설정 해야 합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

프로세스 서버에 대해 자세히 알아보세요.


## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

장애 복구 (failback)를 위해 프로세스 서버를 온-프레미스로 배포 하거나 Azure VM으로 배포 하는 경우 프로세스 서버의 최신 버전이 설치 됩니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다. 다음과 같이 프로세스 서버를 업그레이드할 수 있습니다.

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>Vm을 이동 하 여 프로세스 서버 부하 분산

다음과 같이 두 프로세스 서버 간에 Vm을 이동 하 여 부하를 분산 합니다.

1. 자격 증명 모음에서 **관리** 아래 **Site Recovery 인프라**를 클릭 합니다. **VMware & 물리적 컴퓨터의**경우에서 **구성 서버**를 클릭 합니다.
2. 프로세스 서버가 등록 된 구성 서버를 클릭 합니다.
3. 트래픽 부하를 분산 하려는 프로세스 서버를 클릭 합니다.

    ![부하 분산](media/vmware-azure-manage-process-server/LoadBalance.png)

4. **부하 분산**을 클릭 하 고 컴퓨터를 이동 하려는 대상 프로세스 서버를 선택 합니다. 그런 다음 **확인을** 클릭 합니다.

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **컴퓨터 선택**을 클릭 하 고 현재에서 대상 프로세스 서버로 이동 하려는 컴퓨터를 선택 합니다. 평균 데이터 변경의 세부 정보는 각 가상 머신에 대해 표시됩니다. 그런 후 **OK**를 클릭합니다. 
3. 자격 증명 모음에서 > **Site Recovery 작업**을 **모니터링** 하는 작업의 진행률을 모니터링 합니다.

변경 내용이 포털에 반영 되는 데 약 15 분이 소요 됩니다. 더 빠른 효과를 위해 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="switch-an-entire-workload-to-another-process-server"></a>전체 워크 로드를 다른 프로세스 서버로 전환

프로세스 서버에서 처리 하는 전체 작업을 다음과 같이 다른 프로세스 서버로 이동 합니다.

1. 자격 증명 모음에서 **관리** 아래 **Site Recovery 인프라**를 클릭 합니다. **VMware & 물리적 컴퓨터의**경우에서 **구성 서버**를 클릭 합니다.
2. 프로세스 서버가 등록 된 구성 서버를 클릭 합니다.
3. 작업을 전환 하려는 프로세스 서버를 클릭 합니다.
4. **스위치**를 클릭 하 고 작업을 이동 하려는 대상 프로세스 서버를 선택 합니다. 그런 다음 **확인을** 클릭 합니다.

    ![스위치](media/vmware-azure-manage-process-server/Switch.PNG)

5. 자격 증명 모음에서 > **Site Recovery 작업**을 **모니터링** 하는 작업의 진행률을 모니터링 합니다.

변경 내용이 포털에 반영 되는 데 약 15 분이 소요 됩니다. 더 빠른 효과를 위해 [구성 서버를 새로 고칩니다](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="register-a-master-target-server"></a>마스터 대상 서버 등록

마스터 대상 서버는 구성 서버 및 스케일 아웃 프로세스 서버에 상주 합니다. 구성 서버에 등록 해야 합니다. 이 등록에 오류가 발생 하는 경우 보호 된 항목의 상태에 영향을 줄 수 있습니다. 구성 서버에 마스터 대상 서버를 등록 하려면 등록이 필요한 특정 구성 서버/확장 프로세스 서버에 로그인 합니다. **%PROGRAMDATA%\ASR\Agent**폴더로 이동 하 고 관리자 명령 프롬프트에서 다음을 실행 합니다.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>프로세스 서버 등록

다음과 같이 구성 서버를 사용 하 여 온-프레미스 또는 Azure VM에서 실행 되는 프로세스 서버를 등록 합니다.

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

온-프레미스 프로세스 서버에서 프록시를 사용 하 여 Azure에 연결 하는 경우 프록시 설정을 다음과 같이 수정할 수 있습니다.

1. 프로세스 서버 컴퓨터에 로그인 합니다. 
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. **%PROGRAMDATA%\ASR\Agent**폴더로 이동 하 고 다음 명령을 실행 합니다.
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

확장 프로세스 서버 또는 마스터 대상 서버에서 바이러스 백신 소프트웨어가 실행 되 고 있는 경우 바이러스 백신 작업에서 다음 폴더를 제외 합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 프로세스 서버 설치 디렉터리입니다. 예: C:\Program Files (x86) \Microsoft Azure Site Recovery