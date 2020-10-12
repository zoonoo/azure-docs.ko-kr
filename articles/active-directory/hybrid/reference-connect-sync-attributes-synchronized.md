---
title: Azure AD Connect에서 동기화된 특성 | Microsoft Docs
description: Azure Active Directory에 동기화된 특성을 나열합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9411c22183620f883b4d2819eb3078e49837e578
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90016081"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect 동기화: Azure Active Directory에 동기화된 특성
이 항목에서는 Azure AD Connect 동기화에 의해 동기화되는 특성을 보여 줍니다.  
특성은 관련된 Azure AD 앱으로 그룹화되었습니다.

## <a name="attributes-to-synchronize"></a>동기화할 특성
일반적으로 *동기화할 최소 특성 목록*에 대해 궁금해 합니다. 기본 및 권장 되는 방법은 전체 GAL (전체 주소 목록)을 클라우드에서 생성 하 고 Microsoft 365 작업의 모든 기능을 가져올 수 있도록 기본 특성을 유지 하는 것입니다. 이러한 특성에는 다음 예제와 같이 중요 한 개인 데이터가 포함 되어 있기 때문에 조직에서 클라우드에 동기화 하지 않으려는 몇 가지 특성이 있습니다.  
![잘못된 특성](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

이 경우이 항목의 특성 목록에서 시작 하 고 개인 데이터를 포함 하 고 동기화 할 수 없는 특성을 식별 합니다. 그런 다음 [Azure AD 앱 및 특성 필터링](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)을 사용하여 설치 중에 이러한 특성을 선택 취소합니다.

> [!WARNING]
> 특성을 선택 취소하는 경우 주의해야 하고 동기화가 불가능한 특성만 선택 취소해야 합니다. 다른 특성을 선택 취소하면 기능에 부정적인 영향을 미칠 수도 있습니다.
>
>

## <a name="microsoft-365-apps-for-enterprise"></a>엔터프라이즈용 Microsoft 365 앱
| 특성 이름 | 사용자 | 의견 |
| --- |:---:| --- |
| accountEnabled |X |활성화된 계정을 정의합니다. |
| cn |X | |
| displayName |X | |
| objectSID |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| pwdLastSet |X |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
|samAccountName|X| |
| sourceAnchor |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="exchange-online"></a>Exchange Online
| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| 도우미 |X |X | | |
| altRecipient |X | | |Azure AD Connect 빌드 1.1.552.0 이상이 필요합니다. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| 정보 |X |X |X |이 특성은 현재 그룹에 사용되지 않습니다. |
| 이니셜 |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| 멤버 | | |X | |
| mobile |X |X | | |
| maDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Azure AD Connect 버전 1.1.524.0에서 사용 가능 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute2 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute3 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute4 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute5 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg IsOrganizational | | |X | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Azure AD에서 exchange online에 한 번만 동기화 된 후에는이 특성에 대 한 기관의 소스가 되 고 이후 변경 내용은 온-프레미스에서 동기화 될 수 없습니다. 자세한 내용은 ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange))를 참조 하세요.|
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| authOrig |X |X |X | |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone |X |X | | |
| 정보 |X |X |X | |
| Initials |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| 멤버 | | |X | |
| middleName |X |X | | |
| mobile |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |이 특성은 현재 SharePoint Online에 사용되지 않습니다. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Azure AD에서 exchange online에 한 번만 동기화 된 후에는이 특성에 대 한 기관의 소스가 되 고 이후 변경 내용은 온-프레미스에서 동기화 될 수 없습니다. 자세한 내용은 ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange))를 참조 하세요.|
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다.
. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |
| wWWHomePage |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>팀 및 비즈니스용 Skype Online
| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| 멤버 | | |X | |
| mobile |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | |Azure AD에서 exchange online에 한 번만 동기화 된 후에는이 특성에 대 한 기관의 소스가 되 고 이후 변경 내용은 온-프레미스에서 동기화 될 수 없습니다. 자세한 내용은 ([KB](https://support.microsoft.com/help/3062745/user-photos-aren-t-synced-from-the-on-premises-environment-to-exchange))를 참조 하세요.|
| title |X |X | | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| cn |X | |X |일반 이름 또는 별칭입니다. 대부분 [mail] 값의 접두사입니다. |
| displayName |X |X |X |이름을 나타내는 문자열로 보통 친숙한 이름(성명)으로 표시됩니다. |
| mail |X |X |X |메일 주소 전체입니다. |
| 멤버 | | |X | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| proxyAddresses |X |X |X |기계적 속성입니다. Azure AD에서 사용됩니다. 사용자에 대한 모든 보조 메일 주소를 포함합니다. |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |이 UPN은 사용자용 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="intune"></a>Intune
| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| c |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| 멤버 | | |X | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="dynamics-crm"></a>Dynamics CRM
| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| c |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| 멤버 | | |X | |
| mobile |X |X | | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="3rd-party-applications"></a>타사 애플리케이션
이 그룹은 일반 워크로드 또는 애플리케이션에 필요한 최소한의 특성으로 사용되는 특성 모음입니다. 다른 섹션에 나열되지 않은 워크로드 또는 타사 앱에 사용할 수 있습니다. 다음에 명시적으로 사용됩니다.

* Yammer(User만 사용됨)
* [SharePoint와 같은 리소스에서 제공하는 하이브리드 B2B 조직 간 협업 시나리오](https://go.microsoft.com/fwlink/?LinkId=747036)

이 그룹은 Azure AD 디렉터리를 사용 하 여 Microsoft 365, Dynamics 또는 Intune을 지원 하지 않는 경우 사용할 수 있는 특성 집합입니다. 코어 특성의 작은 집합이 있습니다. 일부 타사 응용 프로그램을 Single Sign-On 또는 프로 비전 하려면 여기에 설명 된 특성과 함께 특성 동기화를 구성 해야 합니다. 응용 프로그램 요구 사항은 각 응용 프로그램에 대 한 [SaaS 앱 자습서](../saas-apps/tutorial-list.md) 에 설명 되어 있습니다.

| 특성 이름 | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| cn |X | |X | |
| displayName |X |X |X | |
| employeeID |X |  |  | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| 멤버 | | |X | |
| objectSID |X | | |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가/지역입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="windows-10"></a>윈도우 10
Windows 10 도메인에 가입된 컴퓨터(디바이스)는 일부 특성을 Azure AD에 동기화합니다. 이 시나리오에 대한 자세한 내용은 [Windows 10 환경용 Azure AD에 도메인 조인된 디바이스 연결](../devices/hybrid-azuread-join-plan.md)을 참조하세요. 이 특성은 항상 동기화되며 Windows 10은 선택 취소할 수 있는 앱으로 표시되지 않습니다. Windows 10 도메인에 가입된 컴퓨터는 특성 userCertificate를 채우는 것으로 식별됩니다.

| 특성 이름 | 디바이스 | 의견 |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |도메인에 가입된 컴퓨터에 대해 하드 코딩된 값입니다. |
| displayName |X | |
| ms-DS-CreatorSID |X |registeredOwnerReference라고도 합니다. |
| objectGUID |X |deviceID라고도 합니다. |
| objectSID |X |onPremisesSecurityIdentifier라고도 합니다. |
| operatingSystem |X |deviceOSType이라고도 합니다. |
| operatingSystemVersion |X |deviceOSVersion이라고도 합니다. |
| userCertificate |X | |

이 특성은 선택한 다른 앱 외에 **사용자** 에 대해서도 있습니다.  

| 특성 이름 | 사용자 | 의견 |
| --- |:---:| --- |
| domainFQDN |X |dnsDomainName이라고도 합니다. 예를 들어 contoso.com입니다. |
| domainNetBios |X |netBiosName이라고도 합니다. 예를 들어 CONTOSO입니다. |
| msDS-KeyCredentialLink |X |사용자가 비즈니스용 Windows Hello에 등록됩니다. | 

## <a name="exchange-hybrid-writeback"></a>Exchange 하이브리드 쓰기 저장
이 특성들은 **Exchange 하이브리드**를 사용하도록 설정할 때 Azure AD에서 온-프레미스 Active Directory로 다시 기록됩니다. Exchange 버전에 따라 더 적은 특성을 동기화 할 수 있습니다.

| 특성 이름(온-프레미스 AD) | 특성 이름(Connect UI) | 사용자 | 연락처 | 그룹 | 의견 |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Azure AD의 cloudAnchor에서 파생됩니다. 이 특성은 Exchange 2016 및 Windows Server 2016 AD의 새로운 기능입니다. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |온라인 보관: 고객의 메일을 보관합니다. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |필터링: 온-프레미스 필터링을 다시 쓰고 온라인 보관 및 보낸 사람의 데이터를 클라이어트로부터 차단합니다. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |필터링: 온-프레미스 필터링을 다시 쓰고 온라인 보관 및 보낸 사람의 데이터를 클라이어트로부터 차단합니다. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |필터링: 온-프레미스 필터링을 다시 쓰고 온라인 보관 및 보낸 사람의 데이터를 클라이어트로부터 차단합니다. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |UM(통합 메시징)-온라인 음성 메일 사용: Microsoft Lync Server Intergration 사용자가 온라인 서비스에 음성 메일이 있는지 Lync Server 온-프레미스에 나타내기 위해서 사용합니다. |
| msExchUserHoldPolicies| UserHoldPolicies |X | | |소송 보류: 어떤 사용자가 소송을 보류 중인지 확인하기 위해 클라우드 서비스를 사용합니다. |
| proxyAddresses| proxyAddresses |X |X |X |Exchange Online의 x500 주소만 삽입됩니다. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Exchange Online 사서함에 온-프레미스 Exchange 사서함이 있는 사용자에게 SendOnBehalfTo 권한을 부여할 수 있습니다. Azure AD Connect 빌드 1.1.552.0 이상이 필요합니다. |

## <a name="exchange-mail-public-folder"></a>Exchange 메일 공용 폴더
이러한 특성은 **Exchange 메일 공용 폴더**를 사용하도록 설정할 때 온-프레미스 Active Directory에서 Azure AD에서 동기화됩니다.

| 특성 이름 | PublicFolder | 의견 |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>디바이스 쓰기 저장
Active Directory에 디바이스 개체를 만듭니다. 이러한 개체는 Azure AD에 조인된 디바이스이거나 도메인에 조인된 Windows 10 컴퓨터일 수 있습니다.

| 특성 이름 | 디바이스 | 의견 |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Windows Server 2016 AD 스키마에서만 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>참고
* 대체 ID를 사용하면, 온-프레미스 특성 userPrincipalName은 Azure AD 특성 onPremisesUserPrinciparlName과 동기화됩니다. 대체 ID 특성(예: 메일)은 Azure AD 특성 userPrincipalName과 동기화됩니다.
* 위의 목록에서 개체 형식 **User**는 개체 형식 **iNetOrgPerson**에도 적용됩니다.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](how-to-connect-sync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.