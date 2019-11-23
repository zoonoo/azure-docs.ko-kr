---
title: Require MFA from untrusted networks - Azure Active Directory
description: Learn how to configure a Conditional Access policy in Azure Active Directory (Azure AD) to for access attempts from untrusted networks.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cedec7f0bd51460796d8138f8d481d2982098f4
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380000"
---
# <a name="how-to-require-mfa-for-access-from-untrusted-networks-with-conditional-access"></a>How to: Require MFA for access from untrusted networks with Conditional Access   

Azure AD(Azure Active Directory)에서는 어디에서든지 디바이스, 앱 및 서비스에 대해 Single Sign-On을 수행할 수 있습니다. 사용자는 조직의 네트워크뿐만 아니라 신뢰할 수 없는 인터넷 위치에서도 클라우드 앱에 액세스할 수 있습니다. 신뢰할 수 없는 네트워크에서의 액세스에 대한 일반적인 모범 사례는 MFA(다단계 인증)를 요구하는 것입니다.

This article gives you the information you need to configure a Conditional Access policy that requires MFA for access from untrusted networks. 

## <a name="prerequisites"></a>전제 조건

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다. 

- The [basic concepts](overview.md) of Azure AD Conditional Access 
- The [best practices](best-practices.md) for configuring Conditional Access policies in the Azure portal

## <a name="scenario-description"></a>시나리오 설명

보안과 생산성 간의 균형을 유지하기 위해 조직의 네트워크에서 로그인하는 경우에만 암호를 요구하는 것으로 충분할 수 있습니다. 그러나 신뢰할 수 없는 네트워크 위치에서 액세스하는 경우 로그인이 합법적인 사용자에 의해 수행되지 않을 위험이 증가합니다. 이 문제를 해결하기 위해 신뢰할 수 없는 네트워크의 액세스 시도를 차단할 수 있습니다. 또는 시도가 합법적인 계정의 소유자에 의해 이루어졌다는 추가 보증을 다시 얻기 위해 MFA(다단계 인증)를 요구할 수도 있습니다. 

With Azure AD Conditional Access, you can address this requirement with a single policy that grants access: 

- 선택한 클라우드 앱으로
- 선택한 사용자 및 그룹에 대해  
- 다단계 인증 요구 
- 다음 위치에서 액세스하는 경우: 
   - 신뢰할 수 없는 위치

## <a name="implementation"></a>구현

The challenge of this scenario is to translate *access from an untrusted network location* into a Conditional Access condition. In a Conditional Access policy, you can configure the [locations condition](location-condition.md) to address scenarios that are related to network locations. 위치 조건을 통해 IP 주소 범위, 국가 및 지역의 논리적 그룹화인 명명된 위치를 선택할 수 있습니다.  

Typically, your organization owns one or more address ranges, for example, 199.30.16.0 - 199.30.16.15.
다음으로 명명된 위치를 구성할 수 있습니다.

- Specifying this range (199.30.16.0/28) 
- **회사 네트워크**와 같은 설명이 포함된 이름 할당 

신뢰할 수 없는 모든 위치를 지정하도록 시도하는 대신 다음을 수행할 수 있습니다.

- 모든 위치 포함 

   ![조건부 액세스](./media/untrusted-networks/02.png)

- 신뢰할 수 있는 모든 위치 제외 

   ![조건부 액세스](./media/untrusted-networks/01.png)

## <a name="policy-deployment"></a>정책 배포

With the approach outlined in this article, you can now configure a Conditional Access policy for untrusted locations. 정책이 예상대로 작동하는지 확인하는 데 권장되는 모범 사례는 프로덕션에 배포하기 전에 테스트하는 것입니다. 테스트 테넌트를 사용하여 새 정책이 의도한 대로 작동하는지 확인하는 것이 좋습니다. 자세한 내용은 [새 정책을 배포하는 방법](best-practices.md#how-should-you-deploy-a-new-policy)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

If you would like to learn more about Conditional Access, see [What is Conditional Access in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
