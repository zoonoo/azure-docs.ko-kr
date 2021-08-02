---
title: Azure Virtual Desktop AD FS Single Sign-On 구성 - Azure
description: Azure Virtual Desktop 환경에 AD FS Single Sign-On을 구성하는 방법
services: virtual-desktop
author: Heidilohr
manager: lizross
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/28/2021
ms.author: helohr
ms.openlocfilehash: c85186d8338918dbcf2af56abd959f5cbff6ad56
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967287"
---
# <a name="configure-ad-fs-single-sign-on-for-azure-virtual-desktop"></a>Azure Virtual Desktop에 대한 AD FS Single Sign-On 구성

> [!IMPORTANT]
> AD FS Single Sign-On은 현재 퍼블릭 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 Azure Virtual Desktop용 AD DS(Active Directory Federation Services) SSO(Single Sign-On)를 구성하는 과정을 안내합니다.

> [!NOTE]
> Azure Virtual Desktop(클래식)은 이 기능을 지원하지 않습니다.

## <a name="requirements"></a>요구 사항

> [!IMPORTANT]
> 퍼블릭 미리 보기 중에는 호스트 풀이 [유효성 검사 환경](create-validation-host-pool.md)에 있도록 구성해야 합니다.

AD FS Single Sign-On를 구성하기 전에 사용자 환경에서 다음 설정을 실행해야 합니다.

* **Active Directory CA(인증서 서비스)** 역할을 배포해야 합니다. 이 역할을 실행하는 모든 서버는 도메인에 가입되어 있고, 최신 Windows 업데이트가 설치되어 있고, [엔터프라이즈 인증 기관](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731183%28v%3dws.10%29)으로 구성되어야 합니다.
* **AD FS(Active Directory Federation Services)** 역할을 배포해야 합니다. 이 역할을 실행하는 모든 서버는 도메인에 가입되어 있고, 최신 Windows 업데이트가 설치되어 있고, Windows Server 2016 이상에서 실행되고 있어야 합니다. 이 역할 설정을 시작하려면 [페더레이션 자습서](../active-directory/hybrid/tutorial-federation.md)를 참조하세요.
* AD FS 서버에 대한 작업 환경의 연결을 보호하려면 **웹 애플리케이션 프록시** 역할을 설정하는 것이 좋습니다. 이 역할을 실행하는 모든 서버는 최신 Windows 업데이트가 설치되어 있고, Windows Server 2016 이상에서 실행되고 있어야 합니다. 이 역할 설정을 시작하려면 이 [웹 애플리케이션 프록시 가이드](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383662(v=ws.11))를 참조하세요.
* 사용자를 Azure AD와 동기화하려면 **Azure AD Connect** 를 배포해야 합니다. Azure AD Connect는 [페더레이션 모드](../active-directory/connect/active-directory-aadconnect-get-started-custom.md)로 구성해야 합니다.
* AD FS 서버에서 Azure Virtual Desktop에 대한 [PowerShell 환경을 설정](powershell-module.md)합니다.
* Windows 10 20H1 또는 20H2를 사용하여 Azure Virtual Desktop에 연결하는 경우 Single Sign-On이 제대로 작동하기 위해 **2021-04 Windows 10용 누적 업데이트(KB5001330)** 이상을 설치해야 합니다.

> [!NOTE]
> 이 솔루션은 Azure AD Domain Services에서는 지원되지 않습니다. Active Directory 도메인 컨트롤러를 사용해야 합니다.

## <a name="supported-clients"></a>지원되는 클라이언트

다음 Azure Virtual Desktop 클라이언트가 이 기능을 지원합니다.

* [Windows 데스크톱 클라이언트](connect-windows-7-10.md)
* [웹 클라이언트](connect-web.md)

## <a name="configure-the-certificate-authority-to-issue-certificates"></a>인증서를 발급하도록 인증 기관 구성

AD FS에서 SSO를 사용할 수 있도록 하려면 다음 인증서 템플릿을 올바르게 만들어야 합니다.

* 먼저 **Exchange 등록 에이전트(오프라인 요청)** 인증서 템플릿을 만들어야 합니다. AD FS는 Exchange 등록 에이전트 인증서 템플릿을 사용하여 사용자 대신 인증서를 요청합니다.
* 또한 AD FS가 로그인 인증서를 만드는 데 사용할 **스마트 카드 로그온** 인증서 템플릿을 만들어야 합니다.

이러한 인증서 템플릿을 만든 후에는 AD FS에서 요청할 수 있도록 인증 기관에서 템플릿을 사용하도록 설정해야 합니다.

> [!NOTE]
> 이 솔루션은 사용자가 많은 경우 시간이 지나면서 인증 기관 데이터베이스를 채우게 되는 모든 사용자 로그온에 대해 새로운 단기 인증서를 생성합니다. [비영구 인증서 처리를 위해 CA를 설정](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ff934598(v=ws.10))하여 이 작업을 방지할 수 있습니다.

### <a name="create-the-enrollment-agent-certificate-template"></a>등록 에이전트 인증서 템플릿 만들기

사용자 환경에 따라 비즈니스용 Windows Hello, 로그온 인증서 또는 VPN 인증서 등의 다른 용도로 등록 에이전트 인증서 템플릿을 이미 구성했을 수 있습니다. 이 경우 SSO를 지원하도록 수정해야 합니다. 그렇지 않은 경우 새 템플릿을 만들 수 있습니다.

등록 에이전트 인증서 템플릿을 이미 사용하고 있는지 확인하려면 AD FS 서버에서 다음 PowerShell 명령을 실행하고 값이 반환되는지 확인합니다. 비어 있는 경우 새 등록 에이전트 인증서 템플릿을 만듭니다. 그렇지 않으면 이름을 저장한 후 기존 등록 에이전트 인증서 템플릿을 업데이트합니다.

```powershell
Import-Module adfs
(Get-AdfsCertificateAuthority).EnrollmentAgentCertificateTemplateName
```

새 등록 에이전트 인증서 템플릿을 만들려면 다음을 수행합니다.

1. 인증 기관의 시작 메뉴에서 **mmc.exe** 를 실행하여 **Microsoft Management Console** 을 시작합니다.
2. **파일...**  > **스냅인 추가/제거...**  > **인증서 템플릿** > **추가 >**  > **확인** 을 선택하여 인증서 템플릿 목록을 확인합니다.
3. **인증서 템플릿** 을 확장하고 **Exchange 등록 에이전트(오프라인 요청)** 를 마우스 오른쪽 단추로 클릭한 후 **템플릿 복제** 를 선택합니다.
4. **일반** 탭을 선택한 다음, **템플릿 표시 이름** 필드에 "ADFS 등록 에이전트"를 입력합니다. 그러면 템플릿 이름이 자동으로 "ADFSEnrollmentAgent"로 설정됩니다.
5. **보안** 탭을 선택한 다음, **추가...** 를 선택합니다.
6. 다음으로 **개체 유형...** , **서비스 계정** 및 **확인** 을 차례로 선택합니다.
7. AD FS의 서비스 계정 이름을 입력하고 **확인** 을 선택합니다.
   * 격리된 AD FS 설정에서 서비스 계정의 이름은 "adfssvc$"로 지정됩니다.
   * Azure AD Connect를 사용하여 AD FS를 설정하는 경우 서비스 계정의 이름은 "aadcsvc$"로 지정됩니다.
8. 서비스 계정이 추가되고 **보안** 탭에 표시되면 **그룹 또는 사용자 이름** 창에서 해당 계정을 선택하고 **AD FS 서비스 계정에 대한 권한** 창에서 "등록" 및 "자동 등록" 둘 다에 대해 **허용** 을 선택한 다음, **확인** 을 선택하여 저장합니다.

   :::image type="content" source="media/adfs-enrollment-properties-security.png" alt-text="등록 에이전트 인증서 템플릿이 제대로 구성된 이후의 보안 탭을 보여 주는 스크린샷":::

기존 등록 에이전트 인증서 템플릿을 업데이트하려면

1. 인증 기관의 시작 메뉴에서 **mmc.exe** 를 실행하여 **Microsoft Management Console** 을 시작합니다.
2. **파일...**  > **스냅인 추가/제거...**  > **인증서 템플릿** > **추가 >**  > **확인** 을 선택하여 인증서 템플릿 목록을 확인합니다.
3. **인증서 템플릿** 을 확장하고 AD FS 서버에 구성된 템플릿에 해당하는 템플릿을 두 번 클릭합니다. **일반** 탭에서 템플릿 이름은 위에서 찾은 이름과 일치해야 합니다.
4. **보안** 탭을 선택한 다음, **추가...** 를 선택합니다.
5. 다음으로 **개체 유형...** , **서비스 계정** 및 **확인** 을 차례로 선택합니다.
6. AD FS의 서비스 계정 이름을 입력하고 **확인** 을 선택합니다.
   * 격리된 AD FS 설정에서 서비스 계정의 이름은 "adfssvc$"로 지정됩니다.
   * Azure AD Connect를 사용하여 AD FS를 설정하는 경우 서비스 계정의 이름은 "aadcsvc$"로 지정됩니다.
7. 서비스 계정이 추가되고 **보안** 탭에 표시되면 **그룹 또는 사용자 이름** 창에서 해당 계정을 선택하고 **AD FS 서비스 계정에 대한 권한** 창에서 "등록" 및 "자동 등록" 둘 다에 대해 **허용** 을 선택한 다음, **확인** 을 선택하여 저장합니다.

### <a name="create-the-smartcard-logon-certificate-template"></a>스마트 카드 로그온 인증서 템플릿 만들기

스마트 카드 로그온 인증서 템플릿을 만들려면

1. 인증 기관의 시작 메뉴에서 **mmc.exe** 를 실행하여 **Microsoft Management Console** 을 시작합니다.
2. **파일...**  > **스냅인 추가/제거...**  > **인증서 템플릿** > **추가** > **확인** 을 선택하여 인증서 템플릿 목록을 확인합니다.
3. **인증서 템플릿** 을 확장하고 **스마트 카드 로그온** 을 마우스 오른쪽 단추로 클릭한 후 **템플릿 복제** 를 선택합니다.
4. **일반** 탭을 선택한 다음, **템플릿 표시 이름** 필드에 "ADFS SSO"를 입력합니다. 그러면 템플릿 이름이 자동으로 "ADFSSSO"로 설정됩니다.
   > [!NOTE]
   > 이 인증서는 주문형으로 요청되므로 유효 기간을 8시간으로, 갱신 기간을 1시간으로 줄이는 것이 좋습니다.

5. **주체 이름** 탭을 선택한 다음, **요청에서 제공** 을 선택합니다. 경고 메시지가 표시되면 **확인** 을 선택합니다.

   :::image type="content" source="media/adfs-sso-properties-subject-inline.png" alt-text="SSO 인증서 템플릿의 주체 이름 탭과 올바르게 구성된 경우의 모양을 보여 주는 스크린샷" lightbox="media/adfs-sso-properties-subject-expanded.png":::

6. **발급 요구 사항** 탭을 선택합니다.
7. **다음 개수의 인증된 서명** 을 선택하고 값 **1** 을 입력합니다.

   :::image type="content" source="media/adfs-sso-properties-issuance-inline.png" alt-text="SSO 인증서 템플릿의 발급 요구 사항 탭과 올바르게 구성된 경우의 모양을 보여 주는 스크린샷" lightbox="media/adfs-sso-properties-issuance-expanded.png":::

8. **애플리케이션 정책** 으로 **인증서 요청 에이전트** 를 선택합니다.
9.  **보안** 탭을 선택한 다음, **추가...** 를 선택합니다.
10. **개체 유형...** , **서비스 계정** 및 **확인** 을 차례로 선택합니다.
11. [등록 에이전트 인증서 템플릿 만들기](#create-the-enrollment-agent-certificate-template) 섹션에서와 마찬가지로 AD FS의 서비스 계정 이름을 입력합니다.
    * 격리된 AD FS 설정에서 서비스 계정의 이름은 "adfssvc$"로 지정됩니다.
    * Azure AD Connect를 사용하여 AD FS를 설정하는 경우 서비스 계정의 이름은 "aadcsvc$"로 지정됩니다.
12. 서비스 계정이 추가되고 **보안** 탭에 표시되면 **그룹 또는 사용자 이름** 창에서 해당 계정을 선택하고 "등록" 및 "자동 등록" 둘 다에 대해 **허용** 을 선택한 다음, **확인** 을 선택하여 저장합니다.

   :::image type="content" source="media/adfs-sso-properties-security.png" alt-text="SSO 인증서 템플릿이 제대로 구성된 이후의 보안 탭을 보여 주는 스크린샷":::

### <a name="enable-the-new-certificate-templates"></a>새 인증서 템플릿을 사용하도록 설정합니다.

새 인증서 템플릿을 사용하도록 설정하려면

1. 인증 기관의 시작 메뉴에서 **mmc.exe** 를 실행하여 **Microsoft Management Console** 을 시작합니다.
2. **파일...**  > **스냅인 추가/제거...**  > **인증 기관** >  **추가 >**  > **마침** > **확인** 을 선택하여 인증 기관을 확인합니다.
3. 왼쪽 창에서 인증 기관을 확장하고 **인증서 템플릿** 을 엽니다.
4. 인증서 템플릿 목록을 표시하는 가운데 창에서 마우스 오른쪽 단추를 클릭하고, **새로 만들기** 를 선택한 다음, **발급할 인증서 템플릿** 을 선택합니다.
5. **ADFS 등록 에이전트** 와 **ADFS SSO** 를 둘 다 선택한 후 **확인** 을 선택합니다. 가운데 창에 두 템플릿이 모두 표시됩니다.

   :::image type="content" source="media/adfs-certificate-templates.png" alt-text="새 ADFS 등록 에이전트 및 ADFS SSO를 포함하여 발급할 수 있는 인증서 템플릿 목록을 보여 주는 스크린샷":::

   > [!NOTE]
   > 등록 에이전트 인증서 템플릿이 이미 구성되어 있으면 ADFS SSO 템플릿만 추가하면 됩니다.

## <a name="configure-the-ad-fs-servers"></a>AD FS 서버를 구성 합니다.

새 인증서 템플릿을 사용하도록 AD FS(Active Directory Federation Services) 서버를 구성하고 SSO를 지원하도록 신뢰 당사자 트러스트를 설정해야 합니다.

AD FS 서버와 Azure Virtual Desktop 서비스 간의 신뢰 당사자 트러스트를 통해 Single Sign-On 인증서 요청이 도메인 환경에 올바르게 전달될 수 있습니다.

AD FS Single Sign-On을 구성하는 경우 공유 키 또는 인증서를 선택해야 합니다.

* 단일 AD FS 서버가 있는 경우 공유 키 또는 인증서를 선택할 수 있습니다.
* AD FS 서버가 여러 개 있는 경우 인증서를 선택해야 합니다.

Windows에 로그인하기 위한 토큰을 생성하는 데 사용되는 공유 키 또는 인증서는 [Azure Key Vault](../key-vault/general/overview.md)에 안전하게 저장해야 합니다. 기존 Key Vault에 비밀을 저장하거나 새 비밀을 배포할 수 있습니다. 어떤 경우든 Azure Virtual Desktop 서비스에서 액세스할 수 있도록 올바른 액세스 정책을 설정해야 합니다.

인증서를 사용하는 경우 어떤 범용 인증서도 사용할 수 있으며 주체 이름 또는 SAN(주체 대체 이름)에 대한 요구 사항은 없습니다. 필수는 아니지만 유효한 인증 기관에서 발급한 인증서를 만드는 것이 좋습니다. 이 인증서는 Azure Key Vault에서 직접 만들 수 있으며, 내보낼 수 있는 프라이빗 키가 있어야 합니다. 퍼블릭 키를 내보낸 다음, 아래 스크립트를 사용하여 AD FS 서버를 구성하는 데 사용할 수 있습니다. 이 인증서는 적절한 주체 이름과 유효한 인증 기관이 있어야 하는 AD FS SSL 인증서와는 다릅니다.

[PowerShell 갤러리](https://www.powershellgallery.com/packages/ConfigureWVDSSO)에서 사용할 수 있는 PowerShell 스크립트 **ConfigureWVDSSO.ps1** 은 신뢰 당사자 트러스트에 대한 AD FS 서버를 구성하고 필요한 경우 인증서를 설치합니다.

이 스크립트에는 AD FS로 확인되고 "/adfs"를 접미사로 사용하는 URL에 해당하는 하나의 필수 매개 변수 *ADFSAuthority* 만 있습니다. 예들 들어 `https://adfs.contoso.com/adfs`입니다.

1. AD FS VM에서 다음 PowerShell cmdlet을 실행하여 이전 섹션의 인증서 템플릿을 사용하도록 AD FS를 구성합니다.
  
   ```powershell
   Set-AdfsCertificateAuthority -EnrollmentAgentCertificateTemplate "ADFSEnrollmentAgent" -LogonCertificateTemplate "ADFSSSO" -EnrollmentAgent
   ```
 
   > [!NOTE]
   > EnrollmentAgentCertificateTemplate이 이미 구성되어 있는 경우 ADFSEnrollmentAgent 대신 기존 템플릿 이름을 사용해야 합니다.

2. ConfigureWVDSSO.ps1 스크립트를 실행 합니다.
   > [!NOTE]
   > 지침의 다음 부분을 완료하려면 `$config` 변수 값이 필요하므로 이전 지침을 완료하는 데 사용한 PowerShell 창을 닫지 마세요. 동일한 PowerShell 창을 계속 사용하거나 새 PowerShell 세션을 시작하는 동안 열어 둘 수 있습니다.
   
   * Key Vault에서 공유 키를 사용하는 경우 ADFSServiceUrl을 AD FS 서비스에 도달하기 위한 전체 URL로 바꾸고 AD FS 서버에서 다음 PowerShell cmdlet을 실행합니다.

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > Azure Government와 같은 소버린 클라우드에서 환경을 구성하려면 WvdWebAppAppIDUri 및 RdWebURL 속성이 필요 합니다. Azure 상용 클라우드에서 이러한 속성은 각각 `https://www.wvd.microsoft.com` 및 `https://rdweb.wvd.microsoft.com`으로 자동 설정됩니다.

   * Key Vault에서 인증서를 사용하는 경우 ADFSServiceUrl을 AD FS 서비스에 도달하기 위한 전체 URL로 바꾸고 AD FS 서버에서 다음 PowerShell cmdlet을 실행합니다.

     ```powershell
     Install-Script ConfigureWVDSSO
     $config = ConfigureWVDSSO.ps1 -ADFSAuthority "<ADFSServiceUrl>" -UseCert -CertPath "<Path to the pfx file>" -CertPassword <Password to the pfx file> [-WvdWebAppAppIDUri "<WVD Web App URI>"] [-RdWebURL "<RDWeb URL>"]
     ```

     > [!NOTE]
     > Azure Government와 같은 소버린 클라우드에서 환경을 구성하려면 WvdWebAppAppIDUri 및 RdWebURL 속성이 필요 합니다. Azure 상용 클라우드에서 이러한 속성은 각각 `https://www.wvd.microsoft.com` 및 `https://rdweb.wvd.microsoft.com`으로 자동 설정됩니다.

3. 다음 PowerShell cmdlet을 실행하여 Azure Key Vault에 대한 액세스 정책을 설정합니다.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName "<Key Vault Name>" -ServicePrincipalName 9cdead84-a844-4324-93f2-b2e6bb768d07 -PermissionsToSecrets get -PermissionsToKeys sign
   ```

4. 비밀을 사용하도록 허용된 쉼표로 구분된 구독 ID 목록을 포함하는 태그를 사용하여 Azure Key Vault에 공유 키 또는 인증서를 저장합니다.

   * Key Vault에서 공유 키를 사용하는 경우 다음 PowerShell cmdlet을 실행하여 공유 키를 저장하고 태그를 설정합니다.

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Set-AzKeyVaultSecret -VaultName "<Key Vault Name>" -Name "adfsssosecret" -SecretValue (ConvertTo-SecureString -String $config.SSOClientSecret  -AsPlainText -Force) -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]}
     ```

   * 인증서가 이미 Key Vault에 있는 경우 다음 PowerShell cmdlet을 실행하여 태그를 설정합니다.

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>"
     $secret = Update-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "<Certificate Name>" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -PassThru
     ```

   * 로컬 인증서가 있는 경우 다음 PowerShell cmdlet을 실행하여 Key Vault에서 인증서를 가져오고 태그를 설정합니다.

     ```powershell
     $hp = Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" 
     $secret = Import-AzKeyVaultCertificate -VaultName "<Key Vault Name>" -Name "adfsssosecret" -Tag @{ 'AllowedWVDSubscriptions' = $hp.Id.Split('/')[2]} -FilePath "<Path to pfx>" -Password (ConvertTo-SecureString -String "<pfx password>"  -AsPlainText -Force)
     ```

> [!NOTE]
> 필요에 따라 [AD FS Single Sign-On 설정](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#keep-me-signed-in-for-unauthenticated-devices)을 변경하여 사용자에게 자격 증명을 묻는 메시지가 표시되는 빈도를 구성할 수 있습니다. 기본적으로 등록되지 않은 디바이스에서는 8시간마다 메시지가 표시됩니다.

## <a name="configure-your-azure-virtual-desktop-host-pool"></a>Azure Virtual Desktop 호스트 풀 구성

> [!IMPORTANT]
> 퍼블릭 미리 보기 중에는 호스트 풀이 [유효성 검사 환경](create-validation-host-pool.md)에 있도록 구성해야 합니다.

이제 Azure Virtual Desktop 호스트 풀에서 AD FS SSO 매개 변수를 구성해야 합니다. 이렇게 하려면 Azure Virtual Desktop에 대한 [PowerShell 환경을 설정하지 않았으면 설정하고](powershell-module.md) 계정에 연결합니다.

그런 다음, AD FS VM의 동일한 PowerShell 창에서 다음 두 cmdlet 중 하나를 실행하여 호스트 풀에 대한 SSO 정보를 업데이트합니다.

* Key Vault에서 공유 키를 사용하는 경우 다음 PowerShell cmdlet을 실행합니다.

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType SharedKeyInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > SSO를 배포하는 Azure 클라우드와 일치하도록 SsoClientId 속성을 설정해야 합니다. Azure 상용 클라우드에서 이 속성은 `https://www.wvd.microsoft.com`으로 설정해야 합니다. 그러나 이 속성의 필수 설정은 Azure Government 클라우드 등의 다른 클라우드의 경우에는 다릅니다.

* Key Vault의 인증서를 사용하는 경우 다음 PowerShell cmdlet을 실행합니다.

  ```powershell
  Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority "<ADFSServiceUrl>" -SsoClientId "<WVD Web App URI>" -SsoSecretType CertificateInKeyVault -SsoClientSecretKeyVaultPath $secret.Id
  ```

  > [!NOTE]
  > SSO를 배포하는 Azure 클라우드와 일치하도록 SsoClientId 속성을 설정해야 합니다. Azure 상용 클라우드에서 이 속성은 `https://www.wvd.microsoft.com`으로 설정해야 합니다. 그러나 이 속성의 필수 설정은 Azure Government 클라우드 등의 다른 클라우드의 경우에는 다릅니다.

### <a name="configure-additional-host-pools"></a>추가 호스트 풀 구성

추가 호스트 풀을 구성해야 하는 경우 기존 호스트 풀을 구성하는 데 사용한 설정을 검색하여 새 호스트 풀을 설정할 수 있습니다.

기존 호스트 풀에서 설정을 검색하려면 PowerShell 창을 열고 다음 cmdlet을 실행합니다.

```powershell
Get-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" | fl *
```

동일한 *SsoClientId*, *SsoClientSecretKeyVaultPath*, *SsoSecretType* 및 *SsoadfsAuthority* 값을 사용하여 [Azure Virtual Desktop 호스트 풀을 구성](#configure-your-azure-virtual-desktop-host-pool)하는 단계를 수행할 수 있습니다.

## <a name="removing-sso"></a>SSO 제거

호스트 풀에서 SSO를 사용하지 않도록 설정하려면 다음 cmdlet을 실행합니다.

```powershell
Update-AzWvdHostPool -Name "<Host Pool Name>" -ResourceGroupName "<Host Pool Resource Group Name>" -SsoadfsAuthority ''
```

또한 AD FS 서버에서 SSO를 사용하지 않도록 설정하려면 다음 cmdlet을 실행합니다.

```powershell
Install-Script UnConfigureWVDSSO
UnConfigureWVDSSO.ps1 -WvdWebAppAppIDUri "<WVD Web App URI>" -WvdClientAppApplicationID "a85cf173-4192-42f8-81fa-777a763e6e2c"
```

> [!NOTE]
> WvdWebAppAppIDUri 속성은 배포 중인 Azure 클라우드와 일치해야 합니다. Azure 상용 클라우드에서 이 속성은 `https://www.wvd.microsoft.com`입니다. Azure Government 클라우드 등의 다른 클라우드에서는 달라집니다.

## <a name="next-steps"></a>다음 단계

Single Sign-On을 구성했으므로 이제 지원되는 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 새 자격 증명을 사용하여 세션에 연결하는 방법을 알아보려면 다음 문서를 확인하세요.

* [Windows Desktop 클라이언트를 사용하여 연결](connect-windows-7-10.md)
* [웹 클라이언트를 사용하여 연결](connect-web.md)