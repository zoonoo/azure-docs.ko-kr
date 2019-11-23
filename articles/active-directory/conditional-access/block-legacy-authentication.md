---
title: Block legacy authentication - Azure Active Directory
description: Learn how to improve your security posture by blocking legacy authentication using Azure AD Conditional Access.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a65145fe9752a90e3328c308ce603c8626d8708
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380873"
---
# <a name="how-to-block-legacy-authentication-to-azure-ad-with-conditional-access"></a>How to: Block legacy authentication to Azure AD with Conditional Access   

사용자가 클라우드 앱에 쉽게 액세스할 수 있도록 Azure AD(Active Directory)에서 레거시 인증을 포함한 다양한 인증 프로토콜을 지원합니다. 그러나 레거시 프로토콜은 MFA(다단계 인증)를 지원하지 않습니다. MFA는 다양한 환경에서 ID 도용 문제를 해결하기 위한 일반적인 요구 사항입니다. 

If your environment is ready to block legacy authentication to improve your tenant's protection, you can accomplish this goal with Conditional Access. This article explains how you can configure Conditional Access policies that block legacy authentication for your tenant.

## <a name="prerequisites"></a>전제 조건

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다. 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>시나리오 설명

Azure AD는 레거시 인증을 포함하여 가장 널리 사용되는 몇 가지 인증 및 권한 부여 프로토콜을 지원합니다. 레거시 인증은 기본 인증을 사용하는 프로토콜을 가리킵니다. 일반적으로 이러한 프로토콜은 모든 유형의 두 번째 단계 인증을 적용할 수 없습니다. 레거시 인증을 기반으로 하는 앱의 예는 다음과 같습니다.

- 이전 Microsoft Office 앱
- POP, IMAP 및 SMTP와 같은 메일 프로토콜을 사용하는 앱

요즘에는 단일 단계 인증(예: 사용자 이름 및 암호)만으로도 충분하지 않습니다. 추측하기 쉬운 암호는 적합하지 않으며, 인간이 적합한 암호를 선택하는 데도 서투릅니다. 또한 암호는 피싱 또는 암호 스프레이와 같은 다양한 공격에도 취약합니다. 암호 위협으로부터 보호하기 위해 수행할 수 있는 가장 쉬운 방법 중 하나는 MFA를 구현하는 것입니다. MFA를 사용하면 공격자가 사용자의 암호를 획득하더라도 암호만으로 데이터를 성공적으로 인증하고 액세스하기에는 충분하지 않습니다.

레거시 인증을 사용하는 애플리케이션에서 테넌트의 리소스에 액세스하지 못하도록 방지하려면 어떻게 해야 할까요? The recommendation is to just block them with a Conditional Access policy. 필요한 경우 특정 사용자 및 특정 네트워크 위치만 레거시 인증을 기반으로 하는 애플리케이션을 사용하도록 허용합니다.

1단계 인증이 완료된 후 조건부 액세스 정책이 적용됩니다. 이처럼 조건부 액세스는 DoS(서비스 거부) 공격 같은 시나리오에서 최전방 방어선으로 사용하기 위해 개발된 것은 아니지만, 이러한 이벤트의 신호를 활용하여(예: 로그인 위험 수준, 요청 위치 등) 액세스를 결정할 수 있습니다.

## <a name="implementation"></a>구현

This section explains how to configure a Conditional Access policy to block legacy authentication. 

### <a name="identify-legacy-authentication-use"></a>Identify legacy authentication use

Before you can block legacy authentication in your directory, you need to first understand if your users have apps that use legacy authentication and how it affects your overall directory. Azure AD sign-in logs can be used to understand if you’re using legacy authentication.

1. Navigate to the **Azure portal** > **Azure Active Directory** > **Sign-ins**.
1. Add the Client App column if it is not shown by clicking on **Columns** > **Client App**.
1. **Add filters** > **Client App** > select all of the options for **Other clients** and click **Apply**.

Filtering will only show you sign-in attempts that were made by legacy authentication protocols. Clicking on each individual sign-in attempt will show you additional details. The **Client App** field under the **Basic Info** tab will indicate which legacy authentication protocol was used.

These logs will indicate which users are still depending on legacy authentication and which applications are using legacy protocols to make authentication requests. For users that do not appear in these logs and are confirmed to not be using legacy authentication, implement a Conditional Access policy for these users only.

### <a name="block-legacy-authentication"></a>레거시 인증 차단 

In a Conditional Access policy, you can set a condition that is tied to the client apps that are used to access your resources. **모바일 앱 및 데스크톱 클라이언트**에 대해 **기타 클라이언트**를 선택하여 레거시 인증을 사용하는 앱으로 앱의 범위를 좁힐 수 있습니다.

![기타 클라이언트](./media/block-legacy-authentication/01.png)

이러한 앱에 대한 액세스를 차단하려면 **액세스 차단**을 선택해야 합니다.

![액세스 차단](./media/block-legacy-authentication/02.png)

### <a name="select-users-and-cloud-apps"></a>사용자 및 클라우드앱 선택

조직의 레거시 인증을 차단하려면 다음을 선택하여 이 작업을 수행할 수 있습니다.

- 모든 사용자
- 모든 클라우드 앱
- 액세스 차단

![할당](./media/block-legacy-authentication/03.png)

Azure has a safety feature that prevents you from creating a policy like this because this configuration violates the  [best practices](best-practices.md) for Conditional Access policies.
 
![지원되지 않는 정책 구성](./media/block-legacy-authentication/04.png)

*모든 사용자 및 모든 클라우드 앱 차단*은 전체 조직에서 테넌트에 로그인하지 못하도록 차단할 수 있으므로 보안 기능이 필요합니다. 최소 모범 사례 요구 사항을 충족하려면 한 명 이상의 사용자를 제외해야 합니다. 디렉터리 역할을 제외할 수도 있습니다.

![지원되지 않는 정책 구성](./media/block-legacy-authentication/05.png)

정책에서 한 명의 사용자를 제외하여 이 보안 기능을 충족할 수 있습니다. [Azure AD에서 몇 개의 응급 액세스 관리 계정](../users-groups-roles/directory-emergency-access.md)을 정의하고 정책에서 이를 제외하는 것이 좋습니다.

## <a name="policy-deployment"></a>정책 배포

정책을 프로덕션에 적용하기 전에 다음 사항에 주의하세요.
 
- **서비스 계정** - 회의실 전화와 같이 서비스 계정으로 사용되거나 디바이스별로 사용되는 사용자 계정을 식별합니다. 이러한 계정에 강력한 암호가 있는지 확인하고 제외된 그룹에 추가합니다.
- **로그인 보고서** - 로그인 보고서를 검토하고 **다른 클라이언트** 트래픽을 찾습니다. 상위 사용량을 식별하고 사용 중인 이유를 조사합니다. 일반적으로 트래픽은 최신 인증 또는 일부 타사 메일 앱을 사용하지 않는 이전의 Office 클라이언트에서 생성됩니다. 이러한 앱에서 사용량을 이동시키려고 계획하거나, 영향력이 적은 경우 사용자에게 해당 앱을 더 이상 사용할 수 없음을 알립니다.
 
자세한 내용은 [새 정책을 배포하려면 어떻게 해야 합니까?](best-practices.md#how-should-you-deploy-a-new-policy)를 참조하세요.

## <a name="what-you-should-know"></a>알아야 할 사항

Blocking access using **Other clients** also blocks Exchange Online PowerShell and Dynamics 365 using basic auth.

**기타 클라이언트**에 대한 정책 구성은 SPConnect와 같은 특정 클라이언트에서 전체 조직을 차단합니다. 이러한 차단은 이전 버전의 클라이언트가 예기치 않은 방식으로 인증하기 때문에 발생합니다. 이 문제는 이전 버전의 Office 클라이언트와 같은 주요 Office 애플리케이션에 적용되지 않습니다.

정책이 적용되려면 최대 24시간까지 걸릴 수 있습니다.

You can select all available grant controls for the **Other clients** condition; however, the end-user experience is always the same - blocked access.

If you block legacy authentication using the **Other clients** condition, you can also set the device platform and location condition. 예를 들어 모바일 디바이스에 대한 레거시 인증만 차단하려면 다음을 선택하여 **디바이스 플랫폼** 조건을 설정합니다.

- Android
- iOS
- Windows Phone

![지원되지 않는 정책 구성](./media/block-legacy-authentication/06.png)

## <a name="next-steps"></a>다음 단계

- If you are not familiar with configuring Conditional Access policies yet, see [require MFA for specific apps with Azure Active Directory Conditional Access](app-based-mfa.md) for an example.
- For more information about modern authentication support, see [How modern authentication works for Office 2013 and Office 2016 client apps](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) 
