---
title: Azure 사이트 복구에서 VM웨어 VM/물리적 서버 재해 복구를 위한 프로세스 서버 관리
description: 이 문서에서는 Azure 사이트 복구를 사용하여 VMware VM/물리적 서버의 재해 복구를 위한 프로세스 서버관리에 대해 설명합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: ef16e3b75ca8e051b1b7abb1a92843279884c697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257252"
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

이 문서에서는 사이트 복구 프로세스 서버를 관리하기 위한 일반적인 작업에 대해 설명합니다.

프로세스 서버는 복제 데이터를 수신, 최적화 및 Azure로 보내는 데 사용됩니다. 또한 복제하려는 VMware VM 및 물리적 서버에서 Mobility 서비스의 푸시 설치를 수행하고 온-프레미스 시스템의 자동 검색을 수행합니다. 온-프레미스 VM웨어 VM 또는 물리적 서버를 Azure로 복제하는 경우 프로세스 서버는 기본적으로 구성 서버 컴퓨터에 설치됩니다. 

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- Azure에서 온-프레미스로 장애 복구를 위해 Azure에서 임시 프로세스 서버를 설정해야 합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

프로세스 서버에 대해 자세히 알아보세요.


## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

프로세스 서버를 온-프레미스 또는 장애 복구용 Azure VM으로 배포할 때 프로세스 서버의 최신 버전이 설치됩니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다. 다음과 같이 프로세스 서버를 업그레이드할 수 있습니다.

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]


## <a name="move-vms-to-balance-the-process-server-load"></a>VM을 이동하여 프로세스 서버 로드의 균형을 조정합니다.

다음과 같이 두 프로세스 서버 간에 VM을 이동하여 부하균형을 조정합니다.

1. 볼트에서 클릭 사이트 **복구 인프라** **관리** 아래에서 . **VMware & 물리적 컴퓨터의 경우** **구성 서버를 클릭합니다.**
2. 프로세스 서버가 등록된 구성 서버를 클릭합니다.
3. 분산 트래픽을 로드하려는 프로세스 서버를 클릭합니다.

    ![부하 분산](media/vmware-azure-manage-process-server/LoadBalance.png)

4. **로드 밸런스를**클릭하고 컴퓨터를 이동할 대상 프로세스 서버를 선택합니다. 그런 다음 **확인을** 클릭합니다.

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **컴퓨터 선택을**클릭하고 현재에서 대상 프로세스 서버로 이동할 컴퓨터를 선택합니다. 평균 데이터 변경의 세부 정보는 각 가상 머신에 대해 표시됩니다. 그런 다음 **확인**을 클릭합니다. 
3. 볼트에서**사이트 복구** **작업 모니터링** > 에서 작업의 진행 상황을 모니터링합니다.

변경 내용이 포털에 반영되려면 약 15분이 소요됩니다. 더 빠른 효과를 위해 [구성 서버를 새로 고칩니다.](vmware-azure-manage-configuration-server.md#refresh-configuration-server)

## <a name="switch-an-entire-workload-to-another-process-server"></a>전체 워크로드를 다른 프로세스 서버로 전환

다음과 같이 프로세스 서버에서 처리하는 전체 워크로드를 다른 프로세스 서버로 이동합니다.

1. 볼트에서 클릭 사이트 **복구 인프라** **관리** 아래에서 . **VMware & 물리적 컴퓨터의 경우** **구성 서버를 클릭합니다.**
2. 프로세스 서버가 등록된 구성 서버를 클릭합니다.
3. 워크로드를 전환할 프로세스 서버를 클릭합니다.
4. **스위치를**클릭하고 워크로드를 이동할 대상 프로세스 서버를 선택합니다. 그런 다음 **확인을** 클릭합니다.

    ![스위치](media/vmware-azure-manage-process-server/Switch.PNG)

5. 볼트에서**사이트 복구** **작업 모니터링** > 에서 작업의 진행 상황을 모니터링합니다.

변경 내용이 포털에 반영되려면 약 15분이 소요됩니다. 더 빠른 효과를 위해 [구성 서버를 새로 고칩니다.](vmware-azure-manage-configuration-server.md#refresh-configuration-server)

## <a name="register-a-master-target-server"></a>마스터 대상 서버 등록

마스터 대상 서버는 구성 서버 및 스케일 아웃 프로세스 서버에 상주합니다. 구성 서버에 등록해야 합니다. 이 등록에 오류가 있는 경우 보호된 항목의 상태관리에 영향을 미칠 수 있습니다. 마스터 대상 서버를 구성 서버로 등록하려면 등록이 필요한 특정 구성 서버/확장 프로세스 서버에 로그인합니다. **폴더 %PROGRAMDATA%\ASR\에이전트로**이동하 고 관리자 명령 프롬프트에서 다음을 실행 합니다.

   ```
   cmd
   cdpcli.exe --registermt

   net stop obengine

   net start obengine

   exit
   ```

## <a name="reregister-a-process-server"></a>프로세스 서버 등록

다음과 같이 구성 서버를 통해 온-프레미스 또는 Azure VM에서 실행 중인 프로세스 서버를 다시 등록합니다.

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

1. 프로세스 서버 컴퓨터에 로그인합니다. 
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
   ```powershell
   $pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
   net stop obengine
   net start obengine
   ```
2. **폴더 %PROGRAMDATA%\ASR\에이전트로**검색하고 이 명령을 실행합니다.
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

바이러스 백신 소프트웨어가 확장 프로세스 서버(또는 마스터 대상 서버)에서 실행 중인 경우 바이러스 백신 작업에서 다음 폴더를 제외합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 서버 설치 디렉토리를 처리합니다. 예: C:\프로그램 파일(x86)\Microsoft Azure 사이트 복구