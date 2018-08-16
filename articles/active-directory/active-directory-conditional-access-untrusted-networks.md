---
title: 방법 - 신뢰할 수 없는 네트워크의 액세스 시도에 대한 Azure Active Directory 조건부 액세스 정책 구성 | Microsoft Docs
description: 신뢰할 수 없는 네트워크에서 액세스 시도에 대해 Azure AD(Azure Active Directory)에서 조건부 액세스 정책을 구성하는 방법을 알아봅니다.
services: active-directory
keywords: 앱에 조건부 액세스, Azure AD로 조건부 액세스, 회사 리소스에 대한 액세스 보호, 조건부 액세스 정책
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.component: protection
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 2dea5686add93d93f35e82445f411035a2451e33
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525980"
---
# <a name="how-to-configure-conditional-access-policies-for-access-attempts-from-untrusted-networks"></a>방법: 신뢰할 수 없는 네트워크의 액세스 시도에 대한 조건부 액세스 정책 구성   

모바일 우선, 클라우드 우선 세계에서 Azure AD(Active Directory)는 어디에서나 장치, 앱 및 서비스에 대한 Single Sign-On을 가능하게 합니다. 결과적으로 사용자는 조직의 네트워크에서 뿐만 아니라 신뢰할 수 없는 인터넷 위치에서 클라우드 앱에 액세스할 수 있습니다. [Azure AD(Azure Active Directory) 조건부 액세스](active-directory-conditional-access-azure-portal.md)를 사용하여 권한 있는 사용자가 클라우드 앱에 액세스하는 방법을 제어할 수 있습니다. 이 컨텍스트에서 한 가지 일반적인 요구 사항은 신뢰할 수 없는 네트워크에서 시작된 액세스 시도를 제어하는 것입니다. 이 문서에서는 이 요구 사항을 처리하는 조건부 액세스 정책을 구성하는 데 필요한 정보를 제공합니다. 

## <a name="prerequisites"></a>필수 조건

이 문서에서는 사용자가 다음에 대해 잘 알고 있다고 가정합니다. 

- Azure AD의 조건부 액세스의 기본 개념 
- Azure Portal에서 조건부 액세스 정책 구성

다음을 참조하세요.

- [Azure Active Directory의 조건부 액세스란?](active-directory-conditional-access-azure-portal.md) - 조건부 액세스에 대한 개요 

- [빠른 시작: Azure Active Directory 조건부 액세스를 사용하는 특정 앱에 MFA 요구](conditional-access/app-based-mfa.md) - 조건부 액세스 정책을 구성하는 경험을 쌓으려면 


## <a name="scenario-description"></a>시나리오 설명

보안과 생산성 사이의 균형을 맞추려면 사용자에게 암호를 사용하여 인증하도록 요구하는 것만으로 충분할 수 있습니다. 그러나 신뢰할 수 없는 네트워크 위치에서 액세스 시도가 이루어지는 경우 로그인이 합법적인 사용자에 의해 수행되지 않는 증가된 위험이 있습니다. 이 문제를 해결하기 위해 신뢰할 수 없는 네트워크의 액세스 시도를 차단할 수 있습니다. 또는 시도가 합법적인 계정의 소유자에 의해 이루어졌다는 추가 보증을 다시 얻기 위해 MFA(다단계 인증)를 요구할 수도 있습니다. 

Azure AD 조건부 액세스를 사용하면 액세스 권한을 부여하는 단일 정책으로 이 요구 사항을 처리할 수 있습니다. 

- 선택한 클라우드 앱으로

- 선택한 사용자 및 그룹에 대해  

- 다단계 인증 요구 

- 액세스 시도가 다음에서 이루어지는 경우: 

    - 신뢰할 수 없는 위치


## <a name="considerations"></a>고려 사항

이 시나리오의 과제는 *신뢰할 수 없는 위치에서 액세스 시도가 이루어지는 경우*를 조건부 액세스 조건으로 변환하는 것입니다. 조건부 액세스 정책에서 네트워크 위치와 관련된 시나리오를 해결하도록 [위치 조건](conditional-access/location-condition.md)을 구성할 수 있습니다. 위치 조건을 통해 IP 주소 범위, 국가 및 지역의 논리적 그룹화를 나타내는 명명된 위치를 선택할 수 있습니다.  

일반적으로 조직에서 하나 이상의 주소 범위를 소유합니다(예: 199.30.16.0-199.30.16.24).
다음으로 명명된 위치를 구성할 수 있습니다.

- 이 범위 지정(199.30.16.0/24) 

- **회사 네트워크**와 같은 설명이 포함된 이름 할당 


신뢰할 수 없는 모든 위치를 지정하도록 시도하는 대신 다음을 수행할 수 있습니다.

- 포함 

    ![조건부 액세스](./media/active-directory-conditional-access-untrusted-networks/02.png)

- 신뢰할 수 있는 모든 위치 제외 

    ![조건부 액세스](./media/active-directory-conditional-access-untrusted-networks/01.png)



## <a name="implementation"></a>구현

이 문서에 설명된 접근 방식으로 이제 신뢰할 수 없는 위치에 대한 조건부 액세스 정책을 구성할 수 있습니다. 예상대로 작동하는지 확인하기 위해 프로덕션 환경으로 출시하기 전에 정책을 항상 테스트해야 합니다. 이상적으로 가능한 경우 테스트 테넌트에서 초기 테스트를 수행해야 합니다. 자세한 내용은 [새 정책을 배포하는 방법](conditional-access/best-practices.md#how-should-you-deploy-a-new-policy)을 참조하세요. 



## <a name="next-steps"></a>다음 단계

조건부 액세스에 대해 자세히 알아보려는 경우 [Azure Active Directory의 조건부 액세스란?](active-directory-conditional-access-azure-portal.md)을 참조하세요.