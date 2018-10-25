---
title: Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버 관리 | Microsoft Docs
description: 이 아티클에서는 Azure Site Recovery를 사용하여 Azure에 대한 VMware 재해 복구를 위해 기존 구성 서버를 관리하는 방법을 설명합니다.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 10/10/2018
ms.author: raynew
ms.openlocfilehash: 35cce4e9e0b722e8ee1b2ea42a79f18a987033f0
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49078648"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware VM에 대해 구성 서버 관리

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 설정합니다. 구성 서버는 온-프레미스 VMware 및 Azure 간의 통신을 조정하고 데이터 복제를 관리합니다. 이 문서에서는 배포된 후에 구성 서버를 관리하기 위한 일반 태스크를 요약합니다.

## <a name="access-configuration-server"></a>구성 서버 액세스

다음과 같이 구성 서버에 액세스할 수 있습니다.

* 배포된 VM에 로그인하고, 바탕 화면 바로 가기에서 **Azure Site Recovery Configuration Manager**를 시작합니다.
* 또는 https://*ConfigurationServerName*/:44315/에서 구성 서버에 원격으로 액세스할 수 있습니다. 관리자 자격 증명을 사용하여 로그인합니다.

## <a name="modify-vmware-server-settings"></a>VMware 서버 설정 수정

1. 다른 VMware 서버를 구성 서버에 연결하려면 [로그인](#access-configuration-server)한 후에 **vCenter 서버/vSphere ESXi 서버 추가**를 선택합니다.
2. 세부 정보를 입력한 다음, **확인**을 선택합니다.

## <a name="modify-credentials-for-automatic-discovery"></a>자동 검색에 대한 자격 증명 수정

1. VMware VM의 자동 검색을 위해 VMware 서버에 연결하는 데 사용되는 자격 증명을 업데이트하려면 [로그인](#access-configuration-server)한 후에 **편집**을 선택합니다.
2. 새 자격 증명을 입력하고 **확인**을 선택합니다.

    ![VMware 수정](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

CSPSConfigtool.exe를 통해 자격 증명을 수정할 수도 있습니다.

1. 구성 서버에 로그인하고 CSPSConfigtool.exe 시작
2. 수정할 계정을 선택하고 **편집**을 클릭합니다.
3. 수정된 자격 증명을 입력하고 **확인**을 클릭하세요.

## <a name="modify-credentials-for-mobility-service-installation"></a>Mobility Service 설치에 대한 자격 증명 수정

복제에 대해 사용되는 VMware VM에 Mobility Service를 자동으로 설치하는 데 사용되는 자격 증명을 수정합니다.

1. [로그인](#access-configuration-server) 후에 **가상 머신 자격 증명 관리** 선택
2. 수정할 계정을 선택하고 **편집**을 클릭합니다.
3. 새 자격 증명을 입력하고 **확인**을 선택합니다.

    ![Mobility Service 자격 증명 수정](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

CSPSConfigtool.exe를 통해 자격 증명을 수정할 수도 있습니다.

1. 구성 서버에 로그인하고 CSPSConfigtool.exe 시작
2. 수정할 계정을 선택하고 **편집**을 클릭합니다.
3. 새 자격 증명을 입력하고 **확인**을 클릭합니다.

## <a name="add-credentials-for-mobility-service-installation"></a>Mobility 서비스 설치를 위해 자격 증명 추가

구성 서버의 OVF 배치 중 자격 증명을 누락한 경우에는 다음을 수행하세요.

1. [로그인](#access-configuration-server) 후에 **가상 머신 자격 증명 관리**를 선택합니다.
2. **가상 머신 자격 증명 추가**를 클릭하세요.
    ![add-mobility-credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. 새 자격 증명을 입력하고 **추가**를 클릭하세요.

CSPSConfigtool.exe를 통해 자격 증명을 추가할 수도 있습니다.

1. 구성 서버에 로그인하고 CSPSConfigtool.exe 시작
2. **추가**를 클릭하고 새 자격 증명을 입력한 다음, **확인**을 클릭하세요.

## <a name="modify-proxy-settings"></a>프록시 설정 수정

Azure에 대한 인터넷 액세스를 위해 구성 서버 컴퓨터에서 사용하는 프록시 설정을 수정합니다. 구성 서버 컴퓨터에서 실행 중인 기본 프로세스 서버 외에도, 프로세스 서버 컴퓨터가 있는 경우 두 컴퓨터의 설정을 수정합니다.

1. 구성 서버에 [로그인](#access-configuration-server)한 후, **연결 관리**를 선택하세요.
2. 프록시 값을 업데이트합니다. 그런 후 **저장**을 선택하여 설정을 업데이트합니다.

## <a name="add-a-network-adapter"></a>네트워크 어댑터 추가

OVF(Open Virtualization Format) 템플릿은 단일 네트워크 어댑터를 사용하여 구성 서버 VM을 배포합니다.

- [VM에 추가 어댑터를 추가](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)할 수 있지만 자격 증명 모음에 구성 서버를 등록하기 전에 추가해야 합니다.
- 구성 서버를 자격 증명 모음에 등록한 후 어댑터를 추가하려면 VM 속성에서 어댑터를 추가합니다. 그런 다음, 자격 증명 모음에 서버를 [다시 등록](#reregister-a-configuration-server-in-the-same-vault)해야 합니다.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>동일한 자격 증명 모음에 구성 서버 다시 등록

필요한 경우 동일한 자격 증명 모음에 구성 서버를 다시 등록할 수 있습니다. 구성 서버 컴퓨터에서 실행 중인 기본 프로세스 서버 외에도, 추가 프로세스 서버 컴퓨터가 있는 경우 두 컴퓨터를 다시 등록합니다.


  1. 자격 증명 모음에서 **관리** > **Site Recovery 인프라** > **구성 서버**를 엽니다.
  2. **서버**에서 **등록 키 다운로드**를 선택하여 자격 증명 모음 자격 증명 파일을 다운로드합니다.
  3. 구성 서버 컴퓨터에 로그인합니다.
  4. **%ProgramData%\ASR\home\svsystems\bin**에서 **cspsconfigtool.exe**를 엽니다.
  5. **자격 증명 모음 등록** 탭에서 **찾아보기**를 선택하고 다운로드한 자격 증명 모음 자격 증명 파일을 찾습니다.
  6. 필요한 경우 프록시 서버 세부 정보를 제공합니다. 그런 다음 **등록**을 선택합니다.
  7. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.
   ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

      >[!NOTE]
      >구성 서버에서 스케일 아웃 프로세스 서버로 **최신 인증서를 끌어오려면** *“<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe>” --registermt* 명령을 실행합니다.

  8. 마지막으로 다음 명령을 실행하여 obengine을 다시 시작합니다.
  ```
          net stop obengine
          net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>다른 자격 증명 모음에 구성 서버 등록

> [!WARNING]
> 다음 단계에서는 현재 자격 증명 모음으로부터 구성 서버를 분리하고 구성 서버에서 보호된 모든 가상 머신의 복제를 중지합니다.

1. 구성 서버에 로그인합니다.
2. 관리자 PowerShell 명령 창을 열고 다음 명령을 실행합니다.

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. 바탕 화면의 단축키를 사용하여 구성 서버 어플라이언스 브라우저 포털을 시작합니다.
4. 새 구성 서버 [등록](vmware-azure-tutorial.md#register-the-configuration-server)과 유사한 등록 단계를 수행하세요.

## <a name="upgrade-the-configuration-server"></a>구성 서버 업그레이드

구성 서버를 업데이트하려면 업데이트 롤업을 실행합니다. 업데이트는 N-4 버전까지 적용할 수 있습니다. 예: 

- 9.7, 9.8, 9.9 또는 9.10을 실행 중인 경우 9.11로 바로 업그레이드할 수 있습니다.
- 9.6 이하를 실행 중이고 9.11로 업그레이드하려는 경우 먼저 9.7 버전으로 업그레이드한 후 9.11로 업그레이드해야 합니다.

모든 버전의 구성 서버로 업그레이드할 수 있는 업데이트 롤업 링크가 [wiki 업데이트 페이지](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)에 제공됩니다.

다음과 같이 서버를 업그레이드합니다.

1. 자격 증명 모음에서 **관리** > **Site Recovery 인프라** > **구성 서버**로 이동합니다.
2. 업데이트를 사용할 수 있는 경우 **에이전트 버전** > 열에 링크가 표시됩니다.
    ![업데이트](./media/vmware-azure-manage-configuration-server/update2.png)
3. 업데이트 설치 관리자 파일을 구성 서버에 다운로드합니다.

    ![주 지역에서](./media/vmware-azure-manage-configuration-server/update1.png)

4. 두 번 클릭하여 설치 관리자를 실행합니다.
5. 설치 관리자는 컴퓨터에서 실행 중인 현재 버전을 검색합니다. **예**를 클릭하여 업그레이드를 시작합니다.
6. 업그레이드가 완료되면 서버 구성의 유효성을 검사합니다.

    ![주 지역에서](./media/vmware-azure-manage-configuration-server/update3.png)

7. **마침**을 클릭하여 설치 관리자를 닫습니다.

## <a name="delete-or-unregister-a-configuration-server"></a>구성 서버 삭제 또는 등록 취소

1. 구성 서버의 모든 VM에 대해 [보호를 사용하지 않습니다](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure).
2. 구성 서버에서 모든 복제 정책을 [연결 해제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) 및 [삭제](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)합니다.
3. 구성 서버에 연결된 모든 vCenter 서버/vSphere 호스트를 [삭제](vmware-azure-manage-vcenter.md#delete-a-vcenter-server)합니다.
4. 자격 증명 모음에서 **Site Recovery 인프라** > **구성 서버**를 엽니다.
5. 제거하려는 구성 서버를 선택합니다. 그런 다음, **세부 정보** 페이지에서 **삭제**를 선택합니다.

    ![구성 서버 삭제](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Powershell로 관리

필요에 따라 PowerShell을 사용하여 구성 서버를 삭제할 수 있습니다.

1. Azure PowerShell 모듈을 [설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0)합니다.
2. 다음 명령을 사용하여 Azure 계정에 로그인합니다.

    `Connect-AzureRmAccount`
3. 자격 증명 모음 구독을 선택합니다.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  자격 증명 모음 컨텍스트를 설정합니다.

    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 구성 서버를 검색합니다.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 구성 서버를 삭제합니다.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> 구성 서버의 강제 삭제를 위해서는 Remove-AzureRmSiteRecoveryFabric에 **-Force** 옵션을 사용할 수 있습니다.

## <a name="generate-configuration-server-passphrase"></a>구성 서버 암호 생성

1. 구성 서버에 로그인한 후 관리자로 명령 프롬프트 창을 엽니다.
2. 디렉터리를 bin 폴더로 변경하려면 **cd %ProgramData%\ASR\home\svsystems\bin** 명령을 실행합니다.
3. 암호 파일을 생성하려면 **genpassphrase.exe -v > MobSvc.passphrase**를 실행합니다.
4. 암호는 **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**에 있는 파일에 저장됩니다.

## <a name="renew-ssl-certificates"></a>SSL 인증서 갱신

구성 서버에는 기본 제공 웹 서버가 있습니다. 이 서버는 Mobility Service, 프로세스 서버 및 마스터 대상 서버의 작업을 오케스트레이션합니다. 웹 서버는 SSL 인증서를 사용하여 클라이언트를 인증합니다. 인증서는 3년 후에 만료되며 언제든지 갱신할 수 있습니다.

### <a name="check-expiry"></a>만료 확인

2016년 5월 이전의 구성 서버 배포의 경우, 인증서 만료가 1년으로 설정되었습니다. 인증서가 곧 만료될 예정인 경우 다음이 발생합니다.

- 만료 날짜가 2개월 이내로 남은 경우 서비스는 포털을 통해 및 전자 메일로 알림을 전송하기 시작합니다(Site Recovery 알림을 구독한 경우).
- 알림 배너가 자격 증명 모음 리소스 페이지에 나타납니다. 자세한 내용은 배너를 선택합니다.
- **지금 업그레이드** 단추가 표시되면 작업 환경의 일부 구성 요소가 아직 9.4.xxxx.x 이상 버전으로 업그레이드되지 않았음을 의미합니다. 인증서를 갱신하기 전에 구성 요소를 업그레이드합니다. 이전 버전에서는 갱신할 수 없습니다.

### <a name="renew-the-certificate"></a>인증서 갱신

1. 자격 증명 모음에서 **Site Recovery 인프라** > **구성 서버**를 엽니다. 원하는 구성 서버를 선택합니다.
2. 만료 날짜가 **구성 서버 상태** 아래에 표시됩니다.
3. **인증서 갱신**을 선택합니다.

## <a name="update-windows-licence"></a>Windows 라이선스 업데이트

OVF 템플릿에 제공되는 라이선스는 180일 동안 유효한 평가 라이선스입니다. 중단 없이 사용하려면 제공된 라이선스를 사용하여 Windows를 정품 인증해야 합니다.

## <a name="failback-requirements"></a>장애 복구 요구 사항

다시 보호 및 장애 복구 중에 온-프레미스 구성 서버는 실행 중이며 연결된 상태여야 합니다. 성공적인 장애 복구(failback) 동안 장애 복구되는 가상 머신이 구성 서버 데이터베이스에 있어야 합니다.

정기적으로 예정된 구성 서버 백업을 수행해야 합니다. 재해가 발생하고 구성 서버가 손실되면 먼저 백업 복사본에서 구성 서버를 복원하고 복원된 구성 서버가 자격 증명 모음에 등록할 때 사용한 동일한 IP 주소를 갖는지 확인해야 합니다. 복원된 구성 서버에 대해 다른 IP 주소가 사용될 경우 장애 복구(Failback)가 작동하지 않습니다.

## <a name="next-steps"></a>다음 단계

Azure에 대한 [VMware VM](vmware-azure-tutorial.md)의 재해 복구를 설정하기 위한 자습서를 검토하세요.
