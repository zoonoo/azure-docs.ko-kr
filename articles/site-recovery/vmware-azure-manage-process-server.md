---
title: Azure Site Recovery를 사용한 Azure로의 VMware VM 및 물리적 서버 재해 복구를 위한 프로세스 서버 관리 | Microsoft Docs
description: 이 문서에서는 Azure Site Recovery를 사용하는 Azure로의 VMware VM 및 물리적 서버 재해 복구를 위해 프로세스 서버 설정을 관리하는 방법을 설명합니다.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: ba80c8ce57495eaa46e915cb0c472eb4aabcee57
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57863630"
---
# <a name="manage-process-servers"></a>프로세스 서버 관리

기본적으로 VMware VM 또는 실제 서버를 Azure에 복제할 때 사용되는 프로세스 서버는 온-프레미스 구성 서버 컴퓨터에 설치됩니다. 별도 프로세스 서버를 설정해야 하는 두 가지 인스턴스가 있습니다.

- 대규모 배포의 경우 용량 크기를 조정하기 위해 추가 온-프레미스 프로세스 서버가 필요할 수 있습니다.
- 장애 복구의 경우 Azure에서 임시 프로세스 서버 설정이 필요합니다. 장애 복구가 완료되면 이 VM을 삭제할 수 있습니다. 

이 아티클에서는 이러한 추가 프로세스 서버에 대한 일반적인 관리 작업을 요약합니다.

## <a name="upgrade-a-process-server"></a>프로세스 서버 업그레이드

다음과 같이 온-프레미스 또는 Azure에서 실행 하는 프로세스 서버를 업그레이드합니다(장애 복구 목적).

[!INCLUDE [site-recovery-vmware-upgrade -process-server](../../includes/site-recovery-vmware-upgrade-process-server-internal.md)]

> [!NOTE]
>   일반적으로 장애 복구를 목적으로 Azure 갤러리 이미지를 사용하여 Azure에서 프로세스 서버를 만들 때 사용 가능한 최신 버전을 실행합니다. Site Recovery 팀은 정기적으로 해결책 및 향상된 기능을 릴리스합니다. 따라서 프로세스 서버를 최신 상태로 유지하는 것이 좋습니다.

## <a name="balance-the-load-on-process-server"></a>프로세스 서버에서 부하 분한

두 프로세스 서버 간에 부하를 분산하려면

1. **Recovery Services 자격 증명 모음** > **관리** > **Site Recovery 인프라** > **VMware 및 물리적 머신** > **구성 서버**로 이동합니다.
2. 프로세스 서버를 등록하려는 구성 서버를 클릭합니다.
3. 구성 서버에 등록된 프로세스 서버의 목록을 페이지에서 사용할 수 있습니다.
4. 워크로드를 수정하려는 프로세스 서버를 클릭합니다.

    ![부하 분산](media/vmware-azure-manage-process-server/LoadBalance.png)

5. 요구 사항에 따라 아래 설명된 대로 **부하 분산** 또는 **스위치** 옵션을 사용할 수 있습니다.

### <a name="load-balance"></a>부하 분산

이 옵션을 통해 하나 이상의 가상 머신을 선택하고 다른 프로세스 서버로 전송할 수 있습니다.

1. **부하 분산**을 클릭하고, 드롭다운에서 대상 프로세스 서버를 선택합니다. **확인**

    ![LoadPS](media/vmware-azure-manage-process-server/LoadPS.PNG)

2. **머신 선택**을 클릭하고, 현재 프로세스 서버에서 대상 프로세스 서버로 이동하려는 가상 머신을 선택합니다. 평균 데이터 변경의 세부 정보는 각 가상 머신에 대해 표시됩니다.
3. **확인**을 클릭합니다. **Recovery Services 자격 증명 모음** > **모니터링** > **Site Recovery 작업**에서 작업의 진행률을 모니터링합니다.
4. 이 작업의 성공적인 완료 후에 변경 내용을 반영하거나 즉시 적용을 위해 [구성 서버를 새로 고치](vmware-azure-manage-configuration-server.md#refresh-configuration-server)는 데 15분이 걸립니다.

### <a name="switch"></a>Switch

이 옵션을 통해 프로세스 서버의 보호를 받는 전체 워크로드가 다른 프로세스 서버로 이동됩니다.

1. **스위치**를 클릭하고, 대상 프로세스 서버를 선택하고, **확인**을 클릭합니다.

    ![Switch](media/vmware-azure-manage-process-server/Switch.PNG)

2. **Recovery Services 자격 증명 모음** > **모니터링** > **Site Recovery 작업**에서 작업의 진행률을 모니터링합니다.
3. 이 작업의 성공적인 완료 후에 변경 내용을 반영하거나 즉시 적용을 위해 [구성 서버를 새로 고치](vmware-azure-manage-configuration-server.md#refresh-configuration-server)는 데 15분이 걸립니다.

## <a name="reregister-a-process-server"></a>프로세스 서버 등록

온-프레미스에서 실행하는 프로세스 서버를 등록하거나 Azure의 구성 서버에서 등록해야 하는 경우 다음을 수행합니다.

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

프로세스 서버가 프록시를 사용하여 Azure의 Site Recovery에 연결할 때 기존 프록시 설정을 수정해야 하는 경우 이 절차를 사용합니다.

1. 프로세스 서버 컴퓨터에 로그온합니다. 
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

## <a name="manage-anti-virus-software-on-process-servers"></a>프로세스 서버에서 바이러스 백신 소프트웨어 관리

바이러스 백신 소프트웨어가 독립 실행형 프로세스 서버 또는 마스터 대상 서버에서 활성화되어 있으면 다음 폴더를 바이러스 백신 작업에서 제외합니다.


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- 프로세스 서버 설치 디렉터리, 예제: C:\Program Files (x86)\Microsoft Azure Site Recovery

