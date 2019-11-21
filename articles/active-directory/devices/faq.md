---
title: Azure Active Directory 디바이스 관리 FAQ | Microsoft Docs
description: Azure Active Directory 디바이스 관리 FAQ
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a27c9ae1b75b9517bd3af92486df96434c5b34fb
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74207381"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory 디바이스 관리 FAQ

## <a name="general-faq"></a>General FAQ

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Q: I registered the device recently. Azure Portal에서 내 사용자 정보에 디바이스가 표시되지 않는 이유는 무엇인가요? Or why is the device owner marked as N/A for hybrid Azure Active Directory (Azure AD) joined devices?

**A:** Windows 10 devices that are hybrid Azure AD joined don't show up under **USER devices**.
Azure Portal에서 **모든 디바이스** 보기를 사용하세요. PowerShell [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) cmdlet을 사용할 수도 있습니다.

다음 디바이스만 **사용자 디바이스** 아래에 나열됩니다.

- 하이브리드 Azure AD에 조인되지 않은 모든 개인 디바이스 
- 모든 비 Windows 10 또는 Windows Server 2016 디바이스
- 모든 비 Windows 디바이스 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Q: How do I know what the device registration state of the client is?

**A:** In the Azure portal, go to **All devices**. 디바이스 ID를 사용하여 디바이스를 검색합니다. 조인 유형 열 아래의 값을 확인합니다. 경우에 따라 디바이스가 다시 설정되거나 다시 설치될 수 있습니다. 따라서 디바이스에서도 디바이스 등록 상태를 반드시 확인해야 합니다.

- Windows 10 및 Windows Server 2016 이상 디바이스인 경우 `dsregcmd.exe /status` 명령을 실행합니다.
- 하위 수준 OS 버전인 경우 `%programFiles%\Microsoft Workplace Join\autoworkplace.exe` 명령을 실행합니다.

**A:** For troubleshooting information, see these articles:
- [Troubleshooting devices using dsregcmd command](troubleshoot-device-dsregcmd.md)
- [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Q: I see the device record under the USER info in the Azure portal. 그리고 디바이스에서 상태가 등록됨으로 표시됩니다. Am I set up correctly to use Conditional Access?

**A:** The device join state, shown by **deviceID**, must match the state on Azure AD and meet any evaluation criteria for Conditional Access. For more information, see [Require managed devices for cloud app access with Conditional Access](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Q: Why do my users see an error message saying "Your organization has deleted the device" or "Your organization has disabled the device" on their Windows 10 devices ?

**A:** On Windows 10 devices joined or registered with Azure AD, users are issued a [Primary refresh token (PRT)](concept-primary-refresh-token.md) which enables single sign on. The validity of the PRT is based on the validaity of the device itself. Users see this message if the device is either deleted or disabled in Azure AD without initiating the action from the device itself. A device can be deleted or disabled in Azure AD one of the following scenarios: 

- User disables the device from the My Apps portal. 
- An administrator (or user) deletes or disables the device in the Azure portal or by using PowerShell
- Hybrid Azure AD joined only: An administrator removes the devices OU out of sync scope resulting in the devices being deleted from Azure AD
- Upgrading Azure AD connect to the version 1.4.xx.x. [Understanding Azure AD Connect 1.4.xx.x and device disappearance](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-device-disappearance).


See below on how these actions can be rectified.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Q: I disabled or deleted my device in the Azure portal or by using Windows PowerShell. But the local state on the device says it's still registered. 어떻게 해야 하나요?

**A:** This operation is by design. In this case, the device doesn't have access to resources in the cloud. Administrators can perform this action for stale, lost or stolen devices to prevent unauthorized access. If this action was performed unintentionally, you'll need to re-enable or re-register the device as described below

- If the device was disabled in Azure AD, an administrator with sufficient privileges can enable it from the Azure AD portal  
  > [!NOTE]
  > If you are syncing devices using Azure AD Connect, hybrid Azure AD joined devices will be automatically re-enabled during the next  sync cycle. So, if you need to disable a hybrid Azure AD joined device, you need to disable it from your on-premises AD

 - If the device is deleted in Azure AD, you need to re-register the device. To re-register, you must take a manual action on the device. See below for instructions for re-registration based on the device state. 

      To re-register hybrid Azure AD joined Windows 10 and Windows Server 2016/2019 devices, take the following steps:

      1. 관리자 권한으로 명령 프롬프트를 엽니다.
      1. [https://slack.botframework.com](`dsregcmd.exe /debug /leave`) 을 입력합니다.
      1. 로그아웃했다가 다시 로그인하여 디바이스를 Azure AD에 다시 등록하는 예약된 작업을 트리거합니다. 

      For down-level Windows OS versions that are hybrid Azure AD joined, take the following steps:

      1. 관리자 권한으로 명령 프롬프트를 엽니다.
      1. [https://slack.botframework.com](`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`) 을 입력합니다.
      1. [https://slack.botframework.com](`"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`) 을 입력합니다.

      For Azure AD joined devices Windows 10 devices, take the following steps:

      1. 관리자 권한으로 명령 프롬프트를 엽니다.
      1. Enter `dsregcmd /forcerecovery` (Note: You need to be an administrator to perform this action).
      1. Click "Sign in" in the dialog that opens up and continue with the sign in process.
      1. Sign out and sign in back to the device to complete the recovery.

      For Azure AD registered Windows 10 devices, take the following steps:

      1. Go to **Settings** > **Accounts** > **Access Work or School**. 
      1. Select the account and select **Disconnect**.
      1. Click on "+ Connect" and register the device again by going through the sign in process.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Q: Why do I see duplicate device entries in the Azure portal?

**A:**

- Windows 10 및 Windows Server 2016의 경우 동일한 디바이스에 조인 취소 및 다시 조인을 반복적으로 시도하면 중복된 항목이 발생할 수 있습니다. 
- **회사 또는 학교 계정 추가**를 사용하는 각 Windows 사용자는 디바이스 이름이 같은 새 디바이스 레코드를 만듭니다.
- 온-프레미스 Azure Directory 도메인에 조인된 하위 수준 Windows OS 버전의 경우 자동 등록에 의해 디바이스에 로그인하는 각 도메인 사용자의 디바이스와 이름이 같은 새 디바이스 레코드가 생성됩니다. 
- 초기화되었다가 같은 이름으로 다시 설치되고 다시 조인된 Azure AD 조인 머신은 디바이스 이름이 같은 다른 레코드로 표시됩니다.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Q: Does Windows 10 device registration in Azure AD support TPMs in FIPS mode?

**A:** Windows 10 device registration only supported for FIPS-compliant TPM 2.0 and not supported for TPM 1.2. If your devices have FIPS-compliant TPM 1.2, you must disable them before proceeding with Azure AD join or Hybrid Azure AD join. Note that Microsoft does not provide any tools for disabling FIPS mode for TPMs as it is dependent on the TPM manufacturer. Please contact your hardware OEM for support. 

---

**Q: Why can a user still access resources from a device I disabled in the Azure portal?**

**A:** It takes up to an hour for a revoke to be applied from the time the Azure AD device is marked as disabled.

>[!NOTE] 
>등록된 디바이스의 경우 사용자가 리소스에 액세스할 수 없도록 디바이스를 초기화하는 것이 좋습니다. 자세한 내용은 [디바이스 등록이란?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune)을 참조하세요. 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Q: Why are there devices marked as "Pending" under the REGISTERED column in the Azure portal?

**A**:  Pending indicates that the device is not registered. This state indicates that a device has been synchronized using Azure AD connect from an on-premises AD and is ready for device registration. These devices have the JOIN TYPE set to "Hybrid Azure AD joined". Learn more on [how to plan your hybrid Azure Active Directory join implementation](hybrid-azuread-join-plan.md).

>[!NOTE]
>A device can also change from having a registered state to "Pending"
>* If a device is deleted from Azure AD first and re-synchronized from an on-premises AD.
>* If a device is removed from a sync scope on Azure AD Connect and added back.
>
>In both cases, you must re-register the device manually on each of these devices. To review whether the device was previously registered, you can [troubleshoot devices using the dsregcmd command](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Azure AD 조인 FAQ

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin an Azure AD joined device locally on the device?

**A:** For pure Azure AD joined devices, make sure you have an offline local administrator account or create one. Azure AD 사용자 자격 증명으로 로그인할 수 없습니다. 그런 다음, **설정** > **계정** > **회사 또는 학교 액세스**로 이동합니다. 계정을 선택하고 **연결 끊기**를 선택합니다. 프롬프트를 따르고, 메시지가 표시되면 로컬 관리자 자격 증명을 제공합니다. 디바이스를 다시 부팅하여 조인 취소 프로세스를 완료합니다.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Q: Can my users' sign in to Azure AD joined devices that are deleted or disabled in Azure AD?

**A:** 예. Windows에는 이전에 로그인한 사용자가 네트워크 연결 없이도 신속하게 데스크톱에 액세스할 수 있게 해주는 캐시된 사용자 이름 및 암호 기능이 있습니다. 

Azure AD에서 디바이스가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 사용자 이름 및 암호를 사용하여 데스크톱에 계속 액세스할 수 있습니다. But as the device is deleted or disabled, users can't access any resources protected by device-based Conditional Access. 

이전에 로그인하지 않은 사용자는 디바이스에 액세스할 수 없습니다. 이들에게 활성화된 캐시된 사용자 이름 및 암호가 없기 때문입니다. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Q: Can a disabled or deleted user sign in to an Azure AD joined devices

**A:** Yes, but only for a limited time. Azure AD에서 사용자가 삭제 또는 비활성화되어도 Windows 디바이스에서 그 사실을 즉시 알지 못합니다. 따라서 이전에 로그인한 사용자는 캐시된 사용자 이름 및 암호를 사용하여 데스크톱에 액세스할 수 있습니다. 

일반적으로 디바이스는 4시간 이내에 사용자 상태를 인식합니다. 그 후 Windows가 데스크톱에 대한 사용자 액세스를 차단합니다. Azure AD에서 사용자가 삭제 또는 비활성화되면 해당 사용자의 모든 토큰이 취소됩니다. 따라서 리소스에 액세스할 수 없습니다. 

이전에 로그인하지 않았으며 삭제 또는 비활성화된 사용자는 디바이스에 액세스할 수 없습니다. 이들에게 활성화된 캐시된 사용자 이름 및 암호가 없기 때문입니다. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Azure AD joined devices after changing their UPN?

**A:** Currently, UPN changes are not fully supported on Azure AD joined devices. 그러므로 이러한 디바이스에서 UPN을 변경하고 나면 Azure AD 인증이 실패합니다. 그러면 사용자의 디바이스에서 SSO 및 조건부 액세스 문제가 발생합니다. 이 경우 문제를 해결하려면 사용자가 새 UPN을 사용하여 "다른 사용자" 타일을 통해 Windows에 로그인해야 합니다. 현재 이 문제를 해결하기 위한 작업이 진행되고 있습니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Q: My users can't search printers from Azure AD joined devices. How can I enable printing from those devices?

**A:** To deploy printers for Azure AD joined devices, see [Deploy Windows Server Hybrid Cloud Print with Pre-Authentication](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). 하이브리드 클라우드 인쇄를 배포하려면 온-프레미스 Windows Server가 필요합니다. 현재는 클라우드 기반 인쇄 서비스를 사용할 수 없습니다. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Q: How do I connect to a remote Azure AD joined device?

**A:** See [Connect to remote Azure Active Directory-joined PC](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Q: Why do my users see *You can’t get there from here*?

**A:** Did you configure certain Conditional Access rules to require a specific device state? 디바이스가 조건을 충족하지 않으면 사용자가 차단되고 해당 메시지가 표시됩니다. Evaluate the Conditional Access policy rules. 이 메시지가 표시되지 않게 하려면 디바이스가 조건을 충족해야 합니다.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Q: Why don't some of my users get Azure Multi-Factor Authentication prompts on Azure AD joined devices?

**A:** A user might join or register a device with Azure AD by using Multi-Factor Authentication. 그러면 디바이스 자체가 해당 사용자의 신뢰할 수 있는 두 번째 요소가 됩니다. 동일한 사용자가 디바이스에 로그인하여 애플리케이션에 액세스할 때마다 Azure AD는 디바이스를 두 번째 요소로 간주합니다. 해당 사용자가 추가 Multi-Factor Authentication 프롬프트 없이 원활하게 애플리케이션에 액세스할 수 있게 해줍니다. 

이 동작은 다음과 같습니다.

- Azure AD 조인 디바이스 및 Azure AD 등록 디바이스에는 적용되지만 하이브리드 Azure AD 조인 디바이스에는 적용되지 않습니다.
- 해당 디바이스에 로그인하는 다른 사용자에게 적용되지 않습니다. 따라서 해당 디바이스에 액세스하는 다른 모든 사용자에게 Multi-Factor Authentication이 요구됩니다. 그 후 Multi-Factor Authentication을 요구하는 애플리케이션에 액세스할 수 있습니다.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Q: Why do I get a *username or password is incorrect* message for a device I just joined to Azure AD?

**A:** Common reasons for this scenario are as follows:

- 사용자 자격 증명이 더 이상 유효하지 않습니다.
- 컴퓨터가 Azure Active Directory와 통신할 수 없습니다. 네트워크 연결 문제가 있는지 확인합니다.
- 페더레이션 로그인을 수행하려면 사용하도록 설정되어 있고 액세스 가능한 WS-Trust 엔드포인트를 페더레이션 서버에서 지원해야 합니다. 
- 통과 인증을 사용하도록 설정했습니다. 따라서 로그인할 때 임시 암호를 변경해야 합니다.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Q: Why do I see the *Oops… an error occurred!* dialog when I try to Azure AD join my PC?

**A:** This error happens when you set up Azure Active Directory enrollment with Intune. Azure AD 조인을 시도한 사용자에게 올바른 Intune 라이선스가 할당되어야 합니다. 자세한 내용은 [Windows 디바이스에 대한 등록 설정](https://docs.microsoft.com/intune/windows-enroll)을 참조하세요.  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Q: Why did my attempt to Azure AD join a PC fail, although I didn't get any error information?

**A:** A likely cause is that you signed in to the device by using the local built-in administrator account. Azure Active Directory 조인을 사용하여 설치를 완료하기 전에 다른 로컬 계정을 만드세요. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What are the MS-Organization-P2P-Access certificates present on our Windows 10 devices?

**A:** The MS-Organization-P2P-Access certificates are issued by Azure AD to both, Azure AD joined and hybrid Azure AD joined devices. 이러한 인증서는 원격 데스크톱 시나리오에 대한 동일한 테넌트에 있는 디바이스 간 트러스트를 설정하는 데 사용됩니다. 한 인증서는 디바이스에 발급되고 다른 인증서는 사용자에게 발급됩니다. 디바이스 인증서는 `Local Computer\Personal\Certificates`에 있으며 하루 동안 유효합니다. 디바이스가 Azure AD에서도 활성 상태이면 (새 인증서를 발급하여) 이 인증서가 갱신됩니다. 사용자 인증서는 `Current User\Personal\Certificates`에 있으며 마찬가지로 하루 동안 유효하지만, 사용자가 다른 Azure AD 조인 디바이스에 대한 원격 데스크톱 세션을 시도할 때 주문형으로 발급됩니다. 만료 시 갱신되지 않습니다. 두 인증서는 `Local Computer\AAD Token Issuer\Certificates`에 있는 MS-Organization-P2P-Access 인증서를 사용하여 발급됩니다. 이 인증서는 디바이스를 등록할 때 Azure AD에서 발급합니다. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why do I see multiple expired certificates issued by MS-Organization-P2P-Access on our Windows 10 devices? How can I delete them?

**A:** There was an issue identified on Windows 10 version 1709 and lower where expired MS-Organization-P2P-Access certificates continued to exist on the computer store because of cryptographic issues. Your users could face issues with network connectivity, if you are using any VPN clients (for example, Cisco AnyConnect) that cannot handle the large number of expired certificates. 이 문제는 만료된 MS-Organization-P2P-Access 인증서를 자동으로 삭제하는 방법을 사용하여 Windows 10 1803 릴리스에서 해결되었습니다. 디바이스를 Windows 10 1803으로 업데이트하여 이 문제를 해결할 수 있습니다. 업데이트할 수 없는 경우 악영향 없이 이러한 인증서를 삭제할 수 있습니다.  

---

## <a name="hybrid-azure-ad-join-faq"></a>하이브리드 Azure AD 조인 FAQ

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Q: How do I unjoin a Hybrid Azure AD joined device locally on the device?

**A:** For hybrid Azure AD joined devices, make sure to turn off automatic registration. 그러면 예약된 작업에서 디바이스를 다시 등록하지 않습니다. 다음으로, 관리자 권한으로 명령 프롬프트를 열고 `dsregcmd.exe /debug /leave`를 입력합니다. 또는 여러 디바이스에서 이 명령을 스크립트로 실행하여 대량으로 조인 취소합니다.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Q: Where can I find troubleshooting information to diagnose hybrid Azure AD join failures?

**A:** For troubleshooting information, see these articles:

- [Windows 10 및 Windows Server 2016 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-current.md)
- [하위 수준 디바이스에 조인된 하이브리드 Azure Active Directory 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Q: Why do I see a duplicate Azure AD registered record for my Windows 10 hybrid Azure AD joined device in the Azure AD devices list?

**A:** When your users add their accounts to apps on a domain-joined device, they might be prompted with **Add account to Windows?** 사용자가 프롬프트에서 **예**를 입력하면 디바이스가 Azure AD에 등록됩니다. 신뢰 유형은 Azure AD 등록으로 표시됩니다. 조직에서 하이브리드 Azure AD 조인을 사용하도록 설정하면 디바이스도 하이브리드 Azure AD에 조인됩니다. 동일한 디바이스에 대한 두 가지 디바이스 상태가 표시됩니다. 

하이브리드 Azure AD 조인이 Azure AD 등록 상태보다 우선합니다. So your device is considered hybrid Azure AD joined for any authentication and Conditional Access evaluation. Azure AD 포털에서 Azure AD 등록 디바이스 레코드를 삭제해도 무방합니다. [Windows 10 머신에서 이 이중 상태를 피하는 방법 또는 정리하는 방법](hybrid-azuread-join-plan.md#review-things-you-should-know)을 알아보세요. 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Q: Why do my users have issues on Windows 10 hybrid Azure AD joined devices after changing their UPN?

**A:** Currently UPN changes are not fully supported with hybrid Azure AD joined devices. 사용자는 디바이스에 로그인하여 온-프레미스 애플리케이션에 액세스할 수는 있지만 UPN을 변경하고 나면 Azure AD 인증이 실패합니다. 그러면 사용자의 디바이스에서 SSO 및 조건부 액세스 문제가 발생합니다. At this time, you need to unjoin the device from Azure AD (run "dsregcmd /leave" with elevated privileges) and rejoin (happens automatically) to resolve the issue. 현재 이 문제를 해결하기 위한 작업이 진행되고 있습니다. 하지만 비즈니스용 Windows Hello를 통해 로그인하는 사용자에게는 이 문제가 발생하지 않습니다. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Q: Do Windows 10 hybrid Azure AD joined devices require line of sight to the domain controller to get access to cloud resources?

**A:** No, except when the user's password is changed. After Windows 10 hybrid Azure AD join is complete, and the user has signed in at least once, the device doesn't require line of sight to the domain controller to access cloud resources. Windows 10 can get single sign-on to Azure AD applications from anywhere with an internet connection, except when a password is changed. Users who sign in with Windows Hello for Business continue to get single sign-on to Azure AD applications even after a password change, even if they don't have line of sight to their domain controller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Q: What happens if a user changes their password and tries to login to their Windows 10 hybrid Azure AD joined device outside the corporate network?

**A:** If a password is changed outside the corporate network (for example, by using Azure AD SSPR), then the user sign in with the new password will fail. For hybrid Azure AD joined devices, on-premises Active Directory is the primary authority. When a device does not have line of sight to the domain controller, it is unable to validate the new password. So, user needs to establish connection with the domain controller (either via VPN or being in the corporate network) before they're able to sign in to the device with their new password. Otherwise, they can only sign in with their old password because of cached sign in capability in Windows. However, the old password is invalidated by Azure AD during token requests and hence, prevents single sign-on and fails any device-based Conditional Access policies. This issue doesn't occur if you use Windows Hello for Business. 

---

## <a name="azure-ad-register-faq"></a>Azure AD 등록 FAQ

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Q: How do I remove an Azure AD registered state for a device locally?

**A:** 
- For Windows 10 Azure AD registered devices, Go to **Settings** > **Accounts** > **Access Work or School**. 계정을 선택하고 **연결 끊기**를 선택합니다. Device registration is per user profile on Windows 10.
- For iOS and Android, you can use the Microsoft Authenticator application **Settings** > **Device Registration** and select **Unregister device**.
- For macOS, you can use the Microsoft Intune Company Portal application to un-enroll the device from management and remove any registration. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Q: How can I block users from adding additional work accounts (Azure AD registered) on my corporate Windows 10 devices?

**A:** Enable the following registry to block your users from adding additional work accounts to your corporate domain joined, Azure AD joined or hybrid Azure AD joined Windows 10 devices. This policy can also be used to block domain joined machines from inadvertently getting Azure AD registered with the same user account. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Q: Can I register Android or iOS BYOD devices?

**A:** Yes, but only with the Azure device registration service and for hybrid customers. AD FS(Active Directory Federation Services)의 온-프레미스 디바이스 등록 서비스에는 지원되지 않습니다.

---
### <a name="q-how-can-i-register-a-macos-device"></a>Q: How can I register a macOS device?

**A:** Take the following steps:

1.  [규정 준수 정책 만들기](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Define a Conditional Access policy for macOS devices](../active-directory-conditional-access-azure-portal.md) 

**설명**

- The users included in your Conditional Access policy need a [supported version of Office for macOS](../conditional-access/technical-reference.md#client-apps-condition) to access resources. 
- 첫 번째 액세스를 시도하는 동안 사용자에게는 회사 포털을 사용하여 디바이스를 등록하라는 메시지가 표시됩니다.

---
## <a name="next-steps"></a>다음 단계

- [Azure AD 등록 디바이스](concept-azure-ad-register.md)에 대한 자세한 정보
- [Azure AD 조인 디바이스](concept-azure-ad-join.md)에 대한 자세한 정보
- [하이브리드 Azure AD 조인 디바이스](concept-azure-ad-join-hybrid.md)에 대한 자세한 정보
