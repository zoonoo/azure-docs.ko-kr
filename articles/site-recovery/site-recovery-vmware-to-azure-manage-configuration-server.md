---
title: " Azure Site Recovery에서 구성 서버 관리 | Microsoft Doc"
description: "이 문서에서는 구성 서버를 설정 및 관리하는 방법을 설명합니다."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 10/06/2017
ms.author: anoopkv
ms.openlocfilehash: e4740c96383468713976e5a98881bec13b0c1921
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="manage-a-configuration-server"></a>구성 서버 관리

구성 서버는 Site Recovery 서비스와 온-프레미스 인프라 간의 코디네이터로 작동합니다. 이 문서에서는 구성 서버를 설정, 구성 및 관리하는 방법을 설명합니다.

> [!NOTE]
> [용량 계획](site-recovery-capacity-planner.md)은 로드 요구 사항에 맞는 구성으로 구성 서버를 배포하는지 확인하는 중요한 단계입니다. [구성 서버에 대한 크기 조정 요구 사항](#sizing-requirements-for-a-configuration-server)에 대해 자세히 읽어보세요.


## <a name="prerequisites"></a>필수 조건
다음은 구성 서버를 설정하는 데 필요한 최소 하드웨어, 소프트웨어 및 네트워크 구성입니다.
> [!IMPORTANT]
> VMware 가상 컴퓨터를 보호하기 위해 구성 서버를 배포하는 경우 **HA(고가용성)** 가상 컴퓨터로 배포하는 것이 좋습니다.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>구성 서버 소프트웨어 다운로드

1. Azure Portal에 로그온하고 Recovery Services 자격 증명 모음으로 이동합니다.
2. **Site Recovery 인프라** > **구성 서버**(VMware 및 물리적 컴퓨터용 아래)로 이동합니다.

  ![서버 페이지 추가](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. **+ 서버** 단추를 클릭합니다.
4. **서버 추가** 페이지에서 다운로드 단추를 클릭하여 등록 키를 다운로드합니다. 구성 서버 설치 동안 Azure Site Recovery 서비스에 등록하기 위해 이 키가 필요합니다.
5. **Microsoft Azure Site Recovery 통합 설치 다운로드** 링크를 클릭하여 구성 서버의 최신 버전을 다운로드합니다.

  ![다운로드 페이지](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  최신 버전의 구성 서버를 [Microsoft 다운로드 센터 다운로드 페이지](http://aka.ms/unifiedsetup)에서 직접 다운로드할 수 있습니다.

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>GUI에서 구성 서버 설치 및 등록
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>명령줄에서 구성 서버 설치 및 등록

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>샘플 사용
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>구성 서버 설치 관리자 명령줄 인수
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>MySql 자격 증명 파일 만들기
MySQLCredsFilePath 매개 변수는 입력으로 파일을 사용합니다. 다음 형식을 사용하여 파일을 만들고 입력 MySQLCredsFilePath 매개 변수로 전달합니다.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>프록시 설정 구성 파일 만들기
ProxySettingsFilePath 매개 변수는 입력으로 파일을 사용합니다. 다음 형식을 사용하여 파일을 만들고 입력 ProxySettingsFilePath 매개 변수로 전달합니다.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>구성 서버의 프록시 설정 수정
1. 구성 서버에 로그인 합니다.
2. 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
3. **자격 증명 모음 등록** 탭을 클릭합니다.
4. 포털에서 새 자격 증명 모음 등록 파일을 다운로드하고 도구에 대한 입력으로 제공합니다.

  ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. 새 프록시 서버 세부 정보를 제공하고 **등록** 단추를 클릭합니다.
6. 관리자 PowerShell 명령 창을 엽니다.
7. 다음 명령을 실행합니다.
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  이 구성 서버에 확장 프로세스 서버가 연결된 경우 배포의 [모든 확장 프로세스 서버에서 프록시 설정을 수정](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server)해야 합니다.

## <a name="modify-user-accounts-and-passwords"></a>사용자 계정 및 암호를 수정합니다.

CSPSConfigTool.exe를 사용하여 **VMware 가상 컴퓨터의 자동 검색**에 사용되는 사용자 계정을 관리하고 **보호된 컴퓨터에 모바일 서비스 강제 설치를 수행합니다. 

1. 구성 서버에 로그인합니다.
2. 바탕 화면에서 사용할 수 있는 바로 가기를 클릭하여 CSPSConfigtool.exe를 시작합니다.
3. **계정 관리** 탭을 클릭합니다.
4. 암호를 수정해야 하는 계정을 선택하고 **편집** 단추를 클릭합니다.
5. 새 암호를 입력하고 **확인**을 클릭합니다.


## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>동일한 Recovery Services 자격 증명 모음을 사용하여 구성 서버 등록
  1. 구성 서버에 로그인 합니다.
  2. 데스크톱에서 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
  3. **자격 증명 모음 등록** 탭을 클릭합니다.
  4. 포털에서 새 등록 파일을 다운로드하고 도구에 대한 입력으로 제공합니다.
        ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
  5. 프록시 서버 세부 정보를 제공하고 **등록** 단추를 클릭합니다.  
  6. 관리자 PowerShell 명령 창을 엽니다.
  7. 다음 명령을 실행합니다.

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  확장 프로세스 서버가 이 구성 서버에 연결되어 있는 경우 배포에서 [모든 확장 프로세스 서버를 다시 등록](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server)해야 합니다.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>다른 Recovery Services 자격 증명 모음에 구성 서버 등록

> [!WARNING]
> 다음 단계에서는 현재 자격 증명 모음으로부터 구성을 분리하고 구성 서버에서 보호된 모든 가상 컴퓨터의 복제를 중지합니다.

1. 구성 서버에 로그인 합니다.
2. 관리자 명령 프롬프트에서 명령을 실행합니다.

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. 바로 가기를 사용하여 cspsconfigtool.exe를 시작합니다.
4. **자격 증명 모음 등록** 탭을 클릭합니다.
5. 포털에서 새 등록 파일을 다운로드하고 도구에 대한 입력으로 제공합니다.

    ![register-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. 프록시 서버 세부 정보를 제공하고 **등록** 단추를 클릭합니다.  
7. 관리자 PowerShell 명령 창을 엽니다.
8. 다음 명령을 실행합니다.
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrading-a-configuration-server"></a>구성 서버 업그레이드

> [!WARNING]
> 업데이트는 N-4 대 버전까지만 지원됩니다. 예를 들어 시장의 최신 버전이 9.11인 경우 버전 9.10, 9.9, 9.8 또는 9.7을 9.11로 직접 업데이트할 수 있습니다. 그러나 9.6 이하 버전의 경우 최소 9.7로 먼저 업데이트해야 구성 서버에 최신 버전을 적용할 수 있습니다. 이전 버전의 다운로드 링크는 [Azure Site Recovery 서비스 업데이트](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx) 아래에서 찾을 수 있습니다.

1. 업데이트 설치 관리자를 구성 서버에 다운로드합니다.
2. 설치 관리자를 두 번 클릭하여 설치 관리자를 시작합니다.
3. 설치 관리자가 컴퓨터에 존재하는 Site Recovery 구성 요소의 버전을 검색하고 확인 메시지를 표시합니다. 
4. 확인 단추를 클릭하여 확인하 업그레이드를 계속합니다.


## <a name="delete-or-unregister-a-configuration-server"></a>구성 서버 삭제 또는 등록 취소

> [!WARNING]
> 구성 서버의 서비스 해제를 시작하기 전에 다음 사항을 확인하세요.
> 1. 이 구성 서버 아래의 모든 가상 컴퓨터에 대한 [보호를 사용하지 않도록 설정](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure)합니다.
> 2. 구성 서버에서 모든 복제 정책을 [연결 해제](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) 및 [삭제](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy)합니다.
> 3. 구성 서버에 연결된 모든 vCenter 서버/vSphere 호스트를 [삭제](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery)합니다.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Azure Portal에서 구성 서버 삭제
1. Azure Portal의 자격 증명 모음 메뉴에서 **Site Recovery 인프라** > **구성 서버**로 이동합니다.
2. 서비스를 해제하려는 구성 서버를 클릭합니다.
3. 구성 서버의 세부 정보 페이지에서 삭제 단추를 클릭합니다.

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. **예**를 클릭하여 서버 삭제를 확인합니다.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>구성 서버 소프트웨어 및 해당 종속성 제거
  > [!TIP]
  Azure Site Recovery에서 구성 서버를 다시 사용하려는 경우에는 4단계로 직접 건너뛸 수 있습니다.

1. 관리자 권한으로 구성 서버에 로그온합니다.
2. 제어판 > 프로그램 > 프로그램 제거 열기
3. 다음 순서로 프로그램을 제거합니다.
  * Microsoft Azure Recovery Services 에이전트
  * Microsoft Azure Site Recovery 모바일 서비스/마스터 대상 서버
  * Microsoft Azure Site Recovery 공급자
  * Microsoft Azure Site Recovery 구성 서버/프로세스 서버
  * Microsoft Azure Site Recovery 구성 서버 종속성
  * MySQL Server 5.5
4. 관리자 명령 프롬프트에서 다음 명령을 실행합니다.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>구성 서버 삭제 또는 등록 취소(PowerShell)

1. Azure PowerShell 모듈 [설치](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0)
2. 다음 명령을 사용하여 Azure 계정에 로그인
    
    `Login-AzureRmAccount`
3. 자격 증명 모음이 있는 구독 선택

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  자격 증명 모음 컨텍스트 설정
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. 구성 서버 선택

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. 구성 서버 삭제

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Remove-AzureRmSiteRecoveryFabric의 **-Force** 옵션은 구성 서버를 강제로 제거/삭제하는 데 사용할 수 있습니다.

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>구성 서버 SSL(Secure Socket Layer) 인증서 갱신
구성 서버에는 기본 제공 웹 서버가 있습니다. 이 서버는 구성 서버에 연결된 모바일 서비스, 프로세스 서버 및 마스터 대상 서버의 작업을 오케스트레이션합니다. 구성 서버의 웹 서버는 SSL 인증서를 사용하여 해당 클라이언트를 인증합니다. 이 인증서의 만료 기간은 3년이며 다음 방법을 사용하여 언제든지 갱신할 수 있습니다.

> [!WARNING]
인증서 만료는 버전 9.4.XXXX.X 이상에서만 수행할 수 있습니다. 인증서 갱신 워크플로를 시작하기 전에 모든 Azure Site Recovery 구성 요소(구성 서버, 프로세스 서버, 마스터 대상 서버, 모바일 서비스)를 업그레이드합니다.

1. Azure Portal에서 자격 증명 모음 > Site Recovery 인프라 > 구성 서버로 이동합니다.
2. SSL 인증서를 갱신해야 하는 구성 서버를 클릭합니다.
3. 구성 서버 상태에서 SSL 인증서에 대한 만료 날짜를 볼 수 있습니다.
4. 다음 그림과 같이 **인증서 갱신** 작업을 클릭하여 인증서를 갱신합니다.

  ![delete-configuration-server](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>보안 소켓 계층 인증서 만료 경고

> [!NOTE]
2016년 5월 이전에 발생한 모든 설치에 대한 SSL 인증서의 유효 기간은 1년으로 설정되었습니다. Azure Portal에 인증서 만료 알림이 표시되기 시작했을 것입니다.

1. 구성 서버의 SSL 인증서가 향후 2개월 내에 만료 예정인 경우 Azure Portal 및 전자 메일을 통해 사용자에게 알림이 제공되기 시작합니다(Azure Site Recovery 알림을 구독해야 함). 자격 증명 모음 리소스 페이지에 알림 배너가 표시되기 시작합니다.

  ![certificate-notification](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. 이 배너를 클릭하면 인증서 만료에 대한 추가 정보를 얻을 수 있습니다.

  ![certificate-details](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  **지금 갱신** 단추 대신 **지금 업그레이드** 단추가 표시됩니다. 지금 업그레이드 단추는 작업 환경의 일부 구성 요소가 아직 9.4.xxxx.x 이상 버전으로 업그레이드되지 않았음을 의미합니다.

## <a name="revive-a-configuration-server-if-the-secure-socket-layer-ssl-certificate-expired"></a>SSL(Secure Socket Layer) 인증서가 만료된 경우 구성 서버를 갱신합니다.

1. 구성 서버를 [최신 버전](http://aka.ms/unifiedinstaller)으로 업데이트합니다.
2. 확장 프로세스 서버, 장애 복구 마스터 대상 서버, 장애 복구 프로세스 서버가 있는 경우 최신 버전으로 업데이트합니다.
3. 보호된 모든 가상 컴퓨터에서 모바일 서비스를 최신 버전으로 업데이트합니다.
4. 구성 서버에 로그인하고 관리자 권한으로 명령 프롬프트를 엽니다.
5. %ProgramData%\ASR\home\svsystems\bin 폴더로 이동합니다.
6. RenewCerts.exe를 실행하여 구성 서버에서 SSL 인증서를 갱신합니다.
7. 프로세스에 성공하면 "인증서 갱신에 성공했습니다."라는 메시지가 표시됩니다.


## <a name="sizing-requirements-for-a-configuration-server"></a>구성 서버에 대한 크기 조정 요구 사항

| **CPU** | **메모리** | **캐시 디스크 크기** | **데이터 변경률** | **보호된 컴퓨터** |
| --- | --- | --- | --- | --- |
| 8개 vCPU(2개 소켓 * 4코어 @ 2.5GHz) |16GB |300GB |500GB 이하 |100대 미만의 컴퓨터를 복제합니다. |
| 12개 vCPU(2개 소켓 * 6코어 @ 2.5GHz) |18GB |600GB |500GB ~ 1TB |100-150대 컴퓨터를 복제합니다. |
| 16개 vCPU(2개 소켓 * 8코어 @ 2.5GHz) |32GB |1TB |1TB ~ 2TB |150-200대 컴퓨터를 복제합니다. |

  >[!TIP]
  일별 데이터 변동이 2TB를 초과하거나 200개 이상의 가상 컴퓨터를 복제하려는 경우 추가 프로세스 서버를 배포하여 복제 트래픽 부하를 분산하는 것이 좋습니다. 확장 프로세스 서버를 배포하는 방법에 대해 자세히 알아봅니다.


## <a name="common-issues"></a>일반적인 문제
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]
