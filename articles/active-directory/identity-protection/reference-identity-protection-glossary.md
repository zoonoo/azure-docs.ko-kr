---
title: Azure Active Directory ID 보호 용어집
description: Azure Active Directory ID 보호 용어집
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a3e2df956aaa4f9fd0af83dd2a18e04d731c714
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232355"
---
# <a name="azure-active-directory-identity-protection-glossary"></a>Azure Active Directory ID 보호 용어집

### <a name="at-risk-user"></a>위험(사용자)
A user with one or more active risk detections. 

### <a name="atypical-sign-in-location"></a>비정상적인 로그인 위치
특정 사용자, 비슷한 사용자 또는 테넌트에 일반적이지 않은 지리적 위치에서 시도하는 로그인입니다.

### <a name="azure-ad-identity-protection"></a>Azure AD ID 보호
A security module of Azure Active Directory that provides a consolidated view into risk detections and potential vulnerabilities affecting an organization’s identities.

### <a name="conditional-access"></a>조건부 액세스
리소스에 대한 액세스를 보호하는 정책입니다. Conditional Access rules are stored in the Azure Active Directory and are evaluated by Azure AD before granting access to the resource.  예제 규칙은 사용자 위치, 디바이스 상태 또는 사용자 인증 방법에 따라 액세스를 제한하게 됩니다.

### <a name="credentials"></a>자격 증명
로컬 및 네트워크 리소스에 대한 액세스 권한을 얻는 데 사용되는 ID 및 ID의 증명을 포함하는 정보입니다. 자격 증명의 예는 사용자 이름 및 암호, 스마트 카드 및 인증서가 있습니다.

### <a name="event"></a>행사
Azure Active Directory에서 작업의 기록입니다.

### <a name="false-positive-risk-detection"></a>False-positive (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection was investigated and was incorrectly flagged as a risk detection.

### <a name="identity"></a>ID
암호 또는 인증서와 같은 조건에 따라 인증이라는 방법으로 확인되어야 하는 개인 및 엔터티입니다.

### <a name="identity-risk-detection"></a>Identity risk detection
Azure AD event that was flagged as anomalous by Identity Protection, and may indicate that an identity has been compromised.

### <a name="ignored-risk-detection"></a>Ignored (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the risk detection is closed without taking a remediation action.

### <a name="impossible-travel-from-atypical-locations"></a>비정상적 위치에서 불가능한 이동
A risk detection triggered when two sign-ins for the same user are detected, where at least one of them is from an atypical sign-in location, and where the time between the sign-ins is shorter than the minimum time it would take to physically travel between these locations.  

### <a name="investigation"></a>조사
The process of reviewing the activities, logs, and other relevant information related to a risk detection to decide whether remediation or mitigation steps are necessary, understand if and how the identity was compromised, and understand how the compromised identity was used.

### <a name="leaked-credentials"></a>유출된 자격 증명
A risk detection triggered when current user credentials (user name and password) are found posted publicly in the Dark   web by our researchers.

### <a name="mitigation"></a>해결 방법
ID 또는 디바이스를 안전한 상태로 복원하지 않고 공격자의 능력을 제한하거나 제거하여 공격에 노출된 ID 또는 디바이스를 악용하는 작업입니다. A mitigation does not resolve previous risk detections associated with the identity or device.

### <a name="multi-factor-authentication"></a>Multi-factor authentication
인증서 등 사용자가 가진 정보를 포함하는 두 개 이상의 인증 방법이 필요한 인증 방법입니다. 사용자 이름, 암호 또는 암호 문구 등 사용자가 아는 정보 혹은 지문 등 물리적 특성 및 개인 서명 등 개인 특성일 수 있습니다.

### <a name="offline-detection"></a>오프라인 검색
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 사후 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="policy-condition"></a>정책 조건
엔터티(그룹, 사용자, 앱, 디바이스 플랫폼, 디바이스 상태, IP 범위, 클라이언트 형식)를 정의하는 보안 정책의 일부는 정책에 포함되었거나 제외됩니다.

### <a name="policy-rule"></a>정책 규칙
The part of a security policy that describes the circumstances that would trigger the policy, and the actions taken when the policy is triggered.

### <a name="prevention"></a>방지
손상이 우려되거나 손상된 ID 또는 디바이스를 남용하여 조직에 대한 손상을 방지하는 동작입니다. A prevention action does not secure the device or identity, and does not resolve previous risk detections.

### <a name="privileged-user"></a>권한 있는(사용자)
A user that at the time of a risk detection, had permanent or temporary admin permissions to one or more resources in Azure Active Directory, such as a Global Administrator, Billing Administrator, Service Administrator, User administrator, and Password Administrator. 

### <a name="real-time"></a>실시간
실시간 탐지를 참조하세요.

### <a name="real-time-detection"></a>실시간 탐지
잘못된 부분을 감지하고 이미 발생한 이벤트에 대한 이벤트를 진행하기 전에 로그인 시도와 같은 이벤트의 위험을 평가합니다.

### <a name="remediated-risk-detection"></a>Remediated (risk detection)
A risk detection status set automatically by Identity Protection, indicating that the risk detection was remediated using the standard remediation action for this type of risk detection. For example, when the user password is reset, many risk detections that indicate that the previous password was compromised are automatically remediated.

### <a name="remediation"></a>재구성
이전에 손상이 우려되거나 손상된 ID 또는 디바이스를 보호하는 작업입니다. A remediation action restores the identity or device to a safe state, and resolves previous risk detections associated with the identity or device.

### <a name="resolved-risk-detection"></a>Resolved (risk detection)
A risk detection status set manually by an Identity Protection user, indicating that the user took an appropriate remediation action outside Identity Protection, and that the risk detection should be considered closed.

### <a name="risk-detection-status"></a>Risk detection status
A property of a risk detection, indicating whether the event is active, and if closed, the reason for closing it.

### <a name="risk-detection-type"></a>위험 탐지 유형
A category for the risk detection, indicating the type of anomaly that caused the event to be considered risky.

### <a name="risk-level-risk-detection"></a>Risk level (risk detection)
An indication (High, Medium, or Low) of the severity of the risk detection to help Identity Protection users prioritize the actions they take to reduce the risk to their organization. 

### <a name="risk-level-sign-in"></a>위험 수준(로그인)
사용자의 ID를 사용하려는 다른 사용자라는 특정 로그인에 대한 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-user-compromise"></a>위험 수준(사용자 손상)
ID가 손상된 가능성을 표시(높음, 중간 또는 낮음)합니다.

### <a name="risk-level-vulnerability"></a>위험 수준(취약점)
ID 보호 사용자가 조직에 대한 위험을 줄이기 위해 수행하는 동작을 우선하도록 하는 취약점의 심각도를 표시(높음, 중간 또는 낮음)합니다.

### <a name="secure-identity"></a>보안(ID)
잠재적으로 노출된 ID를 비교할 수 없는 상태로 복원하기 위해 이미지로 다시 설치한 암호 변경 또는 컴퓨터와 같은 수정 작업을 수행합니다.

### <a name="security-policy"></a>보안 정책
정책 규칙 및 조건의 컬렉션입니다. 사용자, 그룹, 앱, 디바이스, 디바이스 플랫폼, 디바이스 상태, IP 범위 및 Auth2.0 클라이언트 유형과 같은 엔터티에 정책을 적용할 수 있습니다. 정책을 사용하는 경우 정책에 포함된 엔터티가 리소스에 토큰을 발급할 때마다 평가됩니다.

### <a name="sign-in-v"></a>로그인(v)
Azure Active Directory의 ID를 인증하려면.

### <a name="sign-in-n"></a>로그인(n)
Azure Active Directory에서 ID를 인증하는 프로세스 또는 동작이며 이 작업을 캡처하는 이벤트입니다.

### <a name="sign-in-from-anonymous-ip-address"></a>익명 IP 주소에서 로그인
A risk detection triggered after a successful sign-in from IP address that has been identified as an anonymous proxy IP address.

### <a name="sign-in-from-infected-device"></a>감염된 디바이스에서 로그인
A risk detection triggered when a sign-in originates from an IP address, which is known to be used by one or more compromised devices, which are actively attempting to communicate with a bot server.

### <a name="sign-in-from-ip-address-with-suspicious-activity"></a>의심스러운 동작으로 IP 주소에서 로그인
A risk detection triggered after a successful sign-in from an IP address with a high number of failed login attempts across multiple user accounts over a short period of time.

### <a name="sign-in-from-unfamiliar-location"></a>알 수 없는 위치에서 로그인
A risk detection triggered when a user successfully signs in from a new location (IP, Latitude/Longitude, and ASN).

### <a name="sign-in-risk"></a>로그인 위험
위험 수준(로그인)을 참조하세요.

### <a name="sign-in-risk-policy"></a>로그인 위험 정책
A Conditional Access policy that evaluates the risk to a specific sign-in and applies mitigations based on predefined conditions and rules.

### <a name="user-compromise-risk"></a>사용자 손상 위험
위험 수준(사용자 손상)을 참조하세요

### <a name="user-risk"></a>사용자 위험
위험 수준(사용자 손상)을 참조하세요.

### <a name="user-risk-policy"></a>사용자 위험 정책
A Conditional Access policy that considers the sign-in and applies mitigations based on predefined conditions and rules.

### <a name="users-flagged-for-risk"></a>위험에 대한 플래그가 지정된 사용자
Users that have risk detections, which are either active or remediated

### <a name="vulnerability"></a>취약점
디렉터리가 악용 또는 위협에 취약하게 만드는 Azure Active Directory의 구성 또는 조건입니다.

## <a name="see-also"></a>참고 항목

- [Azure Active Directory ID 보호](../active-directory-identityprotection.md)
