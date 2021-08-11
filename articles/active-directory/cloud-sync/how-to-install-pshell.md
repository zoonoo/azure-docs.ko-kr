---
title: CLI(명령줄 인터페이스) 및 PowerShell을 사용하여 Azure AD Connect 클라우드 프로비저닝 에이전트 설치
description: PowerShell cmdlet을 사용하여 Azure AD Connect 클라우드 프로비저닝 에이전트를 설치하는 방법을 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5f560321b13e4c7a47aed262a1d312093e9be56
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108758828"
---
# <a name="install-the-azure-ad-connect-provisioning-agent-using-a-command-line-interface-cli-and-powershell"></a>CLI(명령줄 인터페이스) 및 PowerShell을 사용하여 Azure AD Connect 프로비저닝 에이전트 설치
다음 문서에서는 PowerShell cmdlet을 사용하여 Azure AD Connect 프로비저닝 에이전트를 설치하는 방법을 보여 줍니다.
 
>[!NOTE]
>이 문서에서는 명령줄 인터페이스를 사용하여 프로비저닝 에이전트를 설치하는 방법을 다룹니다.  마법사를 사용하여 Azure AD Connect 프로비저닝 에이전트를 설치하는 방법에 대한 자세한 내용은 [Azure AD Connect 프로비저닝 에이전트 설치](how-to-install.md)를 참조하세요.

## <a name="prerequisite"></a>필수 조건: 


>[!IMPORTANT]
>다음 설치 지침에서는 모든 [사전 요구 사항](how-to-prerequisites.md)을 충족한다고 가정합니다.
>
> PowerShell cmdlet을 사용하여 Azure AD Connect 프로비저닝 에이전트를 설치하기 전에 Windows 서버에 TLS 1.2를 사용하도록 설정해야 합니다. TLS 1.2를 사용하도록 설정하려면 [여기](how-to-prerequisites.md#tls-requirements)에 나와 있는 단계를 사용할 수 있습니다.

 

## <a name="install-the-azure-ad-connect-provisioning-agent-using-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 Azure AD Connect 프로비저닝 에이전트 설치 


 1. Azure Portal에 로그인한 다음, **Azure Active Directory** 로 이동합니다.
 2. 왼쪽 메뉴에서 **Azure AD Connect** 를 선택합니다.
 3. **프로비저닝 관리(미리 보기)**  > **모든 에이전트 검토** 를 선택합니다.
 4. Azure Portal에서 Azure AD Connect 프로비저닝 에이전트를 로컬로 다운로드합니다.  

   ![온-프레미스 에이전트 다운로드](media/how-to-install/install-9.png)</br>
 5. 이러한 지침의 목적에 따라 에이전트는 “C:\ProvisioningSetup” 폴더에 다운로드되었습니다. 
 6. 자동 모드에서 ProvisioningAgent 설치

   ```
   $installerProcess = Start-Process c:\temp\AADConnectProvisioningAgent.Installer.exe /quiet -NoNewWindow -PassThru 
   $installerProcess.WaitForExit()  
   ```
 7. 프로비저닝 에이전트 PS 모듈 가져오기 

   ```
   Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Microsoft.CloudSync.PowerShell.dll" 
   ```
 8. 전역 관리자 자격 증명을 사용하여 AzureAD에 연결합니다. 이 섹션을 사용자 지정하여 보안 저장소에서 암호를 가져올 수 있습니다. 

   ```
   $globalAdminPassword = ConvertTo-SecureString -String "Global admin password" -AsPlainText -Force 

   $globalAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("GlobalAdmin@contoso.onmicrosoft.com", $globalAdminPassword) 
   ```

   Connect-AADCloudSyncAzureAD -자격 증명 $globalAdminCreds 

 9. gMSA 계정을 추가하고 도메인 관리자의 자격 증명을 제공하여 기본 gMSA 계정 만들기 
 
   ```
   $domainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $domainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $domainAdminPassword) 

   Add-AADCloudSyncGMSA -Credential $domainAdminCreds 
   ```
 10. 또는 아래와 같이 위의 cmdlet을 사용하여 미리 만든 gMSA 계정 제공 

 
   ```
   Add-AADCloudSyncGMSA -CustomGMSAName preCreatedGMSAName$ 
   ```
 11. 도메인 추가 

   ```
   $contosoDomainAdminPassword = ConvertTo-SecureString -String "Domain admin password" -AsPlainText -Force 

   $contosoDomainAdminCreds = New-Object System.Management.Automation.PSCredential -ArgumentList ("DomainName\DomainAdminAccountName", $contosoDomainAdminPassword) 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds 
   ```
 12. 또는 아래와 같이 위의 cmdlet을 사용하여 기본 설정 도메인 컨트롤러 구성 

   ```
   $preferredDCs = @("PreferredDC1", "PreferredDC2", "PreferredDC3") 

   Add-AADCloudSyncADDomain -DomainName contoso.com -Credential $contosoDomainAdminCreds -PreferredDomainControllers $preferredDCs 
   ```
 13. 이전 단계를 반복하여 더 많은 도메인을 추가합니다. 해당 도메인의 계정 이름과 도메인 이름을 제공하세요. 
 14. 서비스를 다시 시작합니다. 
   ```
   Restart-Service -Name AADConnectProvisioningAgent  
   ```
 15.  Azure Portal로 이동하여 클라우드 동기화 구성을 만듭니다.

## <a name="provisioning-agent-gmsa-powershell-cmdlets"></a>프로비저닝 에이전트 gMSA PowerShell cmdlet
이제 에이전트를 설치했으므로 gMSA에 더 세부적인 사용 권한을 적용할 수 있습니다.  권한 구성에 대한 자세한 내용 및 단계별 지침은 [Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlet](how-to-gmsa-cmdlets.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 프로비저닝 에이전트 gMSA PowerShell cmdlets](how-to-gmsa-cmdlets.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)