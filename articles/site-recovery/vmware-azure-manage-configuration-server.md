---
title: Azure Site Recovery를 사용하여 VMware 재해 복구를 위한 구성 서버 관리 | Microsoft Docs
description: 이 아티클에서는 Azure Site Recovery를 사용하여 Azure에 대한 VMware 재해 복구를 위해 기존 구성 서버를 관리하는 방법을 설명합니다.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346123"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>VMware VM에 대해 구성 서버 관리

Azure에 대한 VMware VM과 물리적 서버 재해 복구를 위해 [Azure Site Recovery](site-recovery-overview.md)를 사용할 경우 온-프레미스 구성 서버를 설정합니다. 구성 서버는 온-프레미스 VMware 및 Azure 간의 통신을 조정하고 데이터 복제를 관리합니다. 이 문서에서는 배포된 후에 구성 서버를 관리하기 위한 일반 태스크를 요약합니다.



## <a name="modify-vmware-settings"></a>VMware 설정 수정

다음과 같이 구성 서버에 액세스할 수 있습니다.
    - 배포된 VM에 로그인하고, 바탕 화면 바로 가기에서 Azure Site Recovery 구성 관리자를 시작합니다.
    - 또는 **https://*ConfigurationServerName*/:44315/** 에서 구성 서버에 원격으로 액세스할 수 있습니다. 관리자 자격 증명을 사용하여 로그인합니다.
   
### <a name="modify-vmware-server-settings"></a>VMware 서버 설정 수정

1. 다른 VMware 서버를 구성 서버에 연결하려면 로그인한 후에 **vCenter 서버/vSphere ESXi 서버 추가**를 선택합니다.
2. 세부 정보를 입력한 다음, **확인**을 선택합니다.


### <a name="modify-credentials-for-automatic-discovery"></a>자동 검색에 대한 자격 증명 수정

1. VMware VM의 자동 검색을 위해 VMware 서버에 연결하는 데 사용되는 자격 증명을 업데이트하려면 로그인한 후에 **편집**을 선택합니다.
2. 새 자격 증명을 입력하고 **확인**을 선택합니다.

    ![VMware 수정](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Mobility Service 설치에 대한 자격 증명 수정

복제에 대해 사용되는 VMware VM에 Mobility Service를 자동으로 설치하는 데 사용되는 자격 증명을 수정합니다.

1. 로그인한 후에 **가상 머신 자격 증명 관리**를 선택합니다.
2. 새 자격 증명을 입력하고 **확인**을 선택합니다.

    ![Mobility Service 자격 증명 수정](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>프록시 설정 수정

Azure에 대한 인터넷 액세스를 위해 구성 서버 컴퓨터에서 사용하는 프록시 설정을 수정합니다. 구성 서버 컴퓨터에서 실행 중인 기본 프로세스 서버 외에도, 프로세스 서버 컴퓨터가 있는 경우 두 컴퓨터의 설정을 수정합니다.

1. 구성 서버에 로그인한 후에 **연결 관리**를 선택합니다.
2. 프록시 값을 업데이트합니다. 그런 후 **저장**을 선택하여 설정을 업데이트합니다.

## <a name="add-a-network-adapter"></a>네트워크 어댑터 추가

OVF(Open Virtualization Format) 템플릿은 단일 네트워크 어댑터를 사용하여 구성 서버 VM을 배포합니다.

- [VM에 추가 어댑터를 추가](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)할 수 있지만 자격 증명 모음에 구성 서버를 등록하기 전에 추가해야 합니다.
- 구성 서버를 자격 증명 모음에 등록한 후 어댑터를 추가하려면 VM 속성에서 어댑터를 추가합니다. 그런 다음, 자격 증명 모음에 서버를 다시 등록해야 합니다.


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

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault> Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
