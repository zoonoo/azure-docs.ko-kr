<properties
	pageTitle="Azure AD Connect 동기화: Azure Active Directory에 동기화된 특성"
	description="Azure Active Directory에 동기화된 특성을 나열합니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2015"
	ms.author="markusvi"/>


# Azure AD Connect 동기화: Azure Active Directory에 동기화된 특성

이 항목에서는 Azure AD Connect 동기화에 의해 동기화되는 특성을 보여줍니다.<br> 특성은 관련된 Azure AD 앱으로 그룹화됩니다.
 




## Office 365 ProPlus

| 특성 이름| 사용자| 주석 |
| --- | :-: | --- |
| accountEnabled| X| userAccountControl에서 파생됩니다.|
| cn| X| |
| displayName| X| |
| objectSID| X| |
| pwdLastSet| X| |
| sourceAnchor| X| 사용자에게 사용되는 특성은 설치 가이드에서 구성됩니다.|
| usageLocation| X| AD DS에 있는 msExchUsageLocation|
| userPrincipalName| X| |


## Exchange Online

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | userAccountControl에서 파생됩니다.|
| 도우미| X| X| | |
| authOrig| X| X| X| |
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| homePhone| X| X| | |
| info| X| X| X| |
| Initials| X| X| | |
| l| X| X| | |
| legacyExchangeDN| X| X| X| |
| mailNickname| X| X| X| |
| mangedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| maDS-HABSeniorityIndex| X| X| X| |
| msDS-PhoneticDisplayName| X| X| X| |
| msExchArchiveGUID| X| | | |
| msExchArchiveName| X| | | |
| msExchAssistantName| X| X| | |
| msExchAuditAdmin| X| | | |
| msExchAuditDelegate| X| | | |
| msExchAuditDelegateAdmin| X| | | |
| msExchAuditOwner| X| | | |
| msExchBlockedSendersHash| X| X| | |
| msExchBypassAudit| X| | | |
| msExchCoManagedByLink| | | X| |
| msExchDelegateListLink| X| | | |
| msExchELCExpirySuspensionEnd| X| | | |
| msExchELCExpirySuspensionStart| X| | | |
| msExchELCMailboxFlags| X| | | |
| msExchEnableModeration| X| | X| |
| msExchExtensionCustomAttribute1| X| X| X| |
| msExchExtensionCustomAttribute2| X| X| X| |
| msExchExtensionCustomAttribute3| X| X| X| |
| msExchExtensionCustomAttribute4| X| X| X| |
| msExchExtensionCustomAttribute5| X| X| X| |
| msExchHideFromAddressLists| X| X| X| |
| msExchImmutableID| X| | | |
| msExchLitigationHoldDate| X| X| X| |
| msExchLitigationHoldOwner| X| X| X| |
| msExchMailboxAuditEnable| X| | | |
| msExchMailboxAuditLogAgeLimit| X| | | |
| msExchMailboxGuid| X| | | |
| msExchModeratedByLink| X| X| X| |
| msExchModerationFlags| X| X| X| |
| msExchRecipientDisplayType| X| X| X| |
| msExchRecipientTypeDetails| X| X| X| |
| msExchRemoteRecipientType| X| | | |
| msExchRequireAuthToSendTo| X| X| X| |
| msExchResourceCapacity| X| | | |
| msExchResourceDisplay| X| | | |
| msExchResourceMetaData| X| | | |
| msExchResourceSearchProperties| X| | | |
| msExchRetentionComment| X| X| X| |
| msExchRetentionURL| X| X| X| |
| msExchSafeRecipientsHash| X| X| | |
| msExchSafeSendersHash| X| X| | |
| msExchSenderHintTranslations| X| X| X| |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| msExchUserHoldPolicies| X| | | |
| msOrg IsOrganizational| | | X| |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| proxyAddresses| X| X| X| |
| publicDelegates| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 사용자에게 사용되는 특성은 설치 가이드에서 구성됩니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| usageLocation| X| | | AD DS에 있는 msExchUsageLocation|
| userCertificate| X| X| | |
| userPrincipalName| X| | | |
| userSMIMECertificates| X| X| | |
| wWWHomePage| X| X| | |


## SharePoint Online

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | userAccountControl에서 파생됩니다.|
| authOrig| X| X| X| |
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| dLMemRejectPerms| X| X| X| |
| dLMemSubmitPerms| X| X| X| |
| extensionAttribute1| X| X| X| |
| extensionAttribute10| X| X| X| |
| extensionAttribute11| X| X| X| |
| extensionAttribute12| X| X| X| |
| extensionAttribute13| X| X| X| |
| extensionAttribute14| X| X| X| |
| extensionAttribute15| X| X| X| |
| extensionAttribute2| X| X| X| |
| extensionAttribute3| X| X| X| |
| extensionAttribute4| X| X| X| |
| extensionAttribute5| X| X| X| |
| extensionAttribute6| X| X| X| |
| extensionAttribute7| X| X| X| |
| extensionAttribute8| X| X| X| |
| extensionAttribute9| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| hideDLMembership| | | X| |
| homephone| X| X| | |
| info| X| X| X| |
| initials| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| middleName| X| X| | |
| mobile| X| X| | |
| msExchTeamMailboxExpiration| X| | | |
| msExchTeamMailboxOwners| X| | | |
| msExchTeamMailboxSharePointLinkedBy| X| | | |
| msExchTeamMailboxSharePointUrl| X| | | |
| objectSID| X| | X| |
| oOFReplyToOriginator| | | X| |
| otherFacsimileTelephone| X| X| | |
| otherHomePhone| X| X| | |
| otherIpPhone| X| X| | |
| otherMobile| X| X| | |
| otherPager| X| X| | |
| otherTelephone| X| X| | |
| pager| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| postOfficeBox| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| reportToOriginator| | | X| |
| reportToOwner| | | X| |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 사용자에게 사용되는 특성은 설치 가이드에서 구성됩니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| targetAddress| X| X| | |
| telephoneAssistant| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| unauthOrig| X| X| X| |
| url| X| X| | |
| usageLocation| X| | | AD DS에 있는 msExchUsageLocation|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |

## Lync Online

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | userAccountControl에서 파생됩니다.|
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| department| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| X| |
| givenName| X| X| | |
| homephone| X| X| | |
| ipPhone| X| X| | |
| l| X| X| | |
| mail| X| X| X| |
| mailNickname| X| X| X| |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| msExchHideFromAddressLists| X| X| X| |
| msRTCSIP-ApplicationOptions| X| | | |
| msRTCSIP-DeploymentLocator| X| X| | |
| msRTCSIP-Line| X| X| | |
| msRTCSIP-OptionFlags| X| X| | |
| msRTCSIP-OwnerUrn| X| | | |
| msRTCSIP-PrimaryUserAddress| X| X| | |
| msRTCSIP-UserEnabled| X| X| | |
| objectSID| X| | X| |
| otherTelephone| X| X| | |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 사용자에게 사용되는 특성은 설치 가이드에서 구성됩니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| thumbnailphoto| X| X| | |
| title| X| X| | |
| usageLocation| X| | | AD DS에 있는 msExchUsageLocation|
| userPrincipalName| X| | | |
| wWWHomePage| X| X| | |


## Azure RMS

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | 활성화된 계정을 정의합니다.|
| cn| X| | X| 일반 이름 또는 별칭입니다. 대부분 [mail] 값의 접두사입니다.|
| displayName| X| X| X| 이름을 나타내는 문자열로 보통 친숙한 이름(성명)으로 표시됩니다.|
| mail| X| X| X| 메일 주소 전체입니다.|
| member| | | X| |
| objectSID| X| | X| 기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다.|
| proxyAddresses| X| X| X| 기계적 속성입니다. Azure AD에서 사용됩니다. 사용자에 대한 모든 보조 메일 주소를 포함합니다.|
| pwdLastSet| X| | | 기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다.|
| securityEnabled| | | X| groupType에서 파생됩니다.|
| sourceAnchor| X| X| X| 기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다.|
| usageLocation| X| | | 기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다.|
| userPrincipalName| X| | | 이 UPN은 사용자용 로그인 ID입니다. 대부분 [mail] 값과 같습니다.|


## Intune

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | userAccountControl에서 파생됩니다.|
| C| X| X| | |
| cn| X| | X| |
| description| X| X| X| |
| displayName| X| X| X| |
| mail| X| X| X| |
| mailnickname| X| X| X| |
| member| | | X| |
| objectSID| X| | X| |
| proxyAddresses| X| X| X| |
| pwdLastSet| X| | | |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sourceAnchor| X| X| X| 사용자에게 사용되는 특성은 설치 가이드에서 구성됩니다.|
| usageLocation| X| | | AD DS에 있는 msExchUsageLocation|
| userPrincipalName| X| | | |


## Dynamics CRM

| 특성 이름| 사용자| 연락처| 그룹| 주석 |
| --- | :-: | :-: | :-: | --- |
| accountEnabled| X| | | userAccountControl에서 파생됩니다.|
| C| X| X| | |
| cn| X| | X| |
| co| X| X| | |
| company| X| X| | |
| countryCode| X| X| | |
| description| X| X| X| |
| displayName| X| X| X| |
| facsimiletelephonenumber| X| X| | |
| givenName| X| X| | |
| l| X| X| | |
| managedBy| | | X| |
| manager| X| X| | |
| member| | | X| |
| mobile| X| X| | |
| objectSID| X| | X| |
| physicalDeliveryOfficeName| X| X| | |
| postalCode| X| X| | |
| preferredLanguage| X| | | |
| pwdLastSet| X| | | |
| securityEnabled| | | X| groupType에서 파생됩니다|
| sn| X| X| | |
| sourceAnchor| X| X| X| 사용자에게 사용되는 특성은 설치 가이드에서 구성됩니다.|
| st| X| X| | |
| streetAddress| X| X| | |
| telephoneNumber| X| X| | |
| title| X| X| | |
| usageLocation| X| | | AD DS에 있는 msExchUsageLocation|
| userPrincipalName| X| | | |


## 추가 리소스

* [Azure AD Connect Sync: 사용자 지정 동기화 옵션](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
 
<!--Image references-->

<!---HONumber=August15_HO7-->