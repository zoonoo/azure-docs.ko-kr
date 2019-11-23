---
title: Plan hybrid Azure Active Directory join - Azure Active Directory
description: 하이브리드 Azure Active Directory 가입 디바이스를 구성하는 방법에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a4f85aeaf2fb263ba2df8f34a51f9e25c212aff
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379327"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>How To: Plan your hybrid Azure Active Directory join implementation

사용자와 마찬가지로, 디바이스는 보호가 필요한 또 다른 핵심 ID이며, 언제 어디서나 디바이스를 사용하여 리소스를 보호해야 합니다. 다음 방법 중 하나를 사용하여 디바이스 ID를 Azure AD로 가져와서 관리하면 이 목표를 달성할 수 있습니다.

- Azure AD 조인
- 하이브리드 Azure AD 조인
- Azure AD 등록

Azure AD에 디바이스를 가져오면 클라우드와 온-프레미스 리소스에서 SSO(Single Sign-On)를 통해 사용자의 생산성을 극대화할 수 있습니다. At the same time, you can secure access to your cloud and on-premises resources with [Conditional Access](../active-directory-conditional-access-azure-portal.md).

If you have an on-premises Active Directory (AD) environment and you want to join your AD domain-joined computers to Azure AD, you can accomplish this by doing hybrid Azure AD join. 이 문서에서는 사용자 환경에서 하이브리드 Azure AD 조인을 구현하는 데 관련된 단계를 제공합니다. 

## <a name="prerequisites"></a>전제 조건

This article assumes that you are familiar with the [Introduction to device identity management in Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> The minimum required domain controller version for Windows 10 hybrid Azure AD join is Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>구현 계획

하이브리드 Azure AD 구현을 계획하려면 다음 사항을 숙지해야 합니다.

|   |   |
| --- | --- |
| ![확인][1] | 지원되는 디바이스 검토 |
| ![확인][1] | 알아야 할 사항 검토 |
| ![확인][1] | Review controlled validation of hybrid Azure AD join |
| ![확인][1] | Select your scenario based on your identity infrastructure |
| ![확인][1] | Review on-premises AD UPN support for hybrid Azure AD join |

## <a name="review-supported-devices"></a>지원되는 디바이스 검토

하이브리드 Azure AD 조인은 광범위한 Windows 디바이스를 지원합니다. 이전 버전의 Windows를 실행하는 디바이스에 대한 구성에는 추가 또는 다른 단계가 필요하므로 지원되는 디바이스는 두 가지 범주로 그룹화됩니다.

### <a name="windows-current-devices"></a>Windows 현재 디바이스

- Windows 10
- Windows Server 2016
- Windows Server 2019

For devices running the Windows desktop operating system, supported version are listed in this article [Windows 10 release information](https://docs.microsoft.com/windows/release-information/). As a best practice, Microsoft recommends you upgrade to the latest version of Windows 10.

### <a name="windows-down-level-devices"></a>Windows 하위 수준 디바이스

- Windows 8.1
- Windows 7. For support information on Windows 7, see [Support for Windows 7 is ending](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. For support information on Windows Server 2008 and 2008 R2, see [Prepare for Windows Server 2008 end of support](https://www.microsoft.com/cloud-platform/windows-server-2008).

첫 번째 계획 단계에서는 사용자 환경을 검토하고 Windows 하위 수준 디바이스를 지원해야 하는지 여부를 결정해야 합니다.

## <a name="review-things-you-should-know"></a>알아야 할 사항 검토

Hybrid Azure AD join is currently not supported if your environment consists of a single AD forest synchronizing identity data to more than one Azure AD tenant.

If your environment uses virtual desktop infrastructure (VDI), see [Device identity and desktop virtualization](https://docs.microsoft.com/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

Hybrid Azure AD join is supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Hybrid Azure AD join. Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. Starting from WIndows 10 1903 release, TPMs 1.2 are not used for hybrid Azure AD join and devices with those TPMs will be considered as if they don't have a TPM.

Hybrid Azure AD join is not supported for Windows Server running the Domain Controller (DC) role.

Hybrid Azure AD join is not supported on Windows down-level devices when using credential roaming or user profile roaming or mandatory profile.

If you are relying on the System Preparation Tool (Sysprep) and if you are using a **pre-Windows 10 1809** image for installation, make sure that image is not from a device that is already registered with Azure AD as Hybrid Azure AD join.

If you are relying on a Virtual Machine (VM) snapshot to create additional VMs, make sure that snapshot is not from a VM that is already registered with Azure AD as Hybrid Azure AD join.

If your Windows 10 domain joined devices are [Azure AD registered](overview.md#getting-devices-in-azure-ad) to your tenant, it could lead to a dual state of Hybrid Azure AD joined and Azure AD registered device. We recommend upgrading to Windows 10 1803 (with KB4489894 applied) or above to automatically address this scenario. In pre-1803 releases, you will need to remove the Azure AD registered state manually before enabling Hybrid Azure AD join. In 1803 and above releases, the following changes have been made to avoid this dual state:

- Any existing Azure AD registered state would be automatically removed <i>after the device is Hybrid Azure AD joined</i>.
- You can prevent your domain joined device from being Azure AD registered by adding this registry key - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- In Windows 10 1803, if you have Windows Hello for Business configured, the user needs to re-setup Windows Hello for Business after the dual state clean up.This issue has been addressed with KB4512509

> [!NOTE]
> The Azure AD registered device will not be automatically removed if it is managed by Intune.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Review controlled validation of hybrid Azure AD join

When all of the pre-requisites are in place, Windows devices will automatically register as devices in your Azure AD tenant. The state of these device identities in Azure AD is referred as hybrid Azure AD join. More information about the concepts covered in this article can be found in the article [Introduction to device identity management in Azure Active Directory](overview.md).

Organizations may want to do a controlled validation of hybrid Azure AD join before enabling it across their entire organization all at once. Review the article [controlled validation of hybrid Azure AD join](hybrid-azuread-join-control.md) to understand how to accomplish it.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Select your scenario based on your identity infrastructure

Hybrid Azure AD join works with both, managed and federated environments depending on whether the UPN is routable or non-routable. See bottom of the page for table on supported scenarios.  

### <a name="managed-environment"></a>관리 환경

관리형 환경은 [Seamless Single Sign On](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso)을 사용하여 [PHS(암호 해시 동기화)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 또는 [PTA(통과 인증)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta)를 통해 배포할 수 있습니다.

이러한 시나리오는 인증용 페더레이션 서버를 구성할 필요가 없습니다.

### <a name="federated-environment"></a>페더레이션 환경

페더레이션 환경은 다음 요구 사항을 지원하는 ID 공급자가 있어야 합니다. AD FS(Active Directory Federation Services)를 사용하는 페더레이션된 환경을 사용하는 경우에는 아래 요구 사항이 이미 지원됩니다.

- **WIAORMULTIAUTHN claim:** This claim is required to do hybrid Azure AD join for Windows down-level devices.
- **WS-Trust protocol:** This protocol is required to authenticate Windows current hybrid Azure AD joined devices with Azure AD. AD FS를 사용하는 경우 다음 WS-Trust 엔드포인트를 사용하도록 설정해야 합니다. `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **adfs/services/trust/2005/windowstransport** 또는 **adfs/services/trust/13/windowstransport**는 모두 인트라넷 연결 엔드포인트로만 사용하도록 설정해야 하며 웹 애플리케이션 프록시를 통해 엑스트라넷 연결 엔드포인트로 노출되어서는 안됩니다. WS-Trust Windows 엔드포인트를 비활성화는 방법에 대해 자세히 알아보려면 [프록시에서 WS-Trust Windows 엔드포인트 사용 안 함](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)을 참조하세요. **서비스** > **엔드포인트**에서 AD FS 관리 콘솔을 통해 어떤 엔드포인트가 사용하도록 설정되었는지 확인할 수 있습니다.

> [!NOTE]
> Azure AD는 관리형 도메인에서 스마트 카드나 인증서를 지원하지 않습니다.

버전 1.1.819.0부터 Azure AD Connect는 하이브리드 Azure AD 조인을 구성하는 마법사를 제공합니다. 마법사를 사용하면 구성 프로세스를 크게 간소화할 수 있습니다. 필요한 버전의 Azure AD Connect를 설치할 수 없는 경우 [디바이스 등록을 수동으로 구성하는 방법](hybrid-azuread-join-manual.md)을 참조하세요. 

Based on the scenario that matches your identity infrastructure, see:

- [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
- [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Review on-premises AD UPN support for Hybrid Azure AD join

경우에 따라 온-프레미스 AD UPN이 Azure AD UPN과 다를 수 있습니다. 그러한 경우 Windows 10 하이브리드 Azure AD 조인은 [인증 방법](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), 도메인 유형 및 Windows 10 버전을 기반으로 온-프레미스 AD UPN에 대한 제한된 지원을 제공합니다. 사용자 환경에 있을 수 있는 온-프레미스 AD UPN에는 두 가지 유형이 있습니다.

- Routable UPN: A routable UPN has a valid verified domain, that is registered with a domain registrar. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.org는 Contoso에서 소유하고 [Azure AD에서 확인](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)된 기본 도메인입니다.
- Non-routable UPN: A non-routable UPN does not have a verified domain. 조직의 사설망 내에서만 적용됩니다. 예를 들어 contoso.com이 Azure AD의 기본 도메인인 경우 contoso.com은 온-프레미스 AD의 기본 도에미인이지만 인터넷에서 확인할 수 없는 도메인이며 Consoso의 네트워크 내에서만 사용됩니다.

아래 표에서는 Windows 10 하이브리드 Azure AD 조인에서 이러한 온-프레미스 AD UPN에 대한 지원을 자세히 설명합니다.

| 온-프레미스 AD UPN 유형 | 도메인 유형 | Windows 10 버전 | 설명 |
| ----- | ----- | ----- | ----- |
| 라우팅 가능 | 페더레이션 | 1703 릴리스 | 일반 공급 |
| 라우팅 불가능 | 페더레이션 | 1803 릴리스 | 일반 공급 |
| 라우팅 가능 | Managed | 1803 릴리스 | Generally available, Azure AD SSPR on Windows lockscreen is not supported |
| 라우팅 불가능 | Managed | 지원하지 않음 | |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Configure hybrid Azure Active Directory join for federated environment](hybrid-azuread-join-federated-domains.md)
> [Configure hybrid Azure Active Directory join for managed environment](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
