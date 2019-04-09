---
title: 인증 하는 동안 로그인-Azure Active Directory 사용자 이름 조회 | Microsoft Docs
description: 화면의 어떻게 로그인 하는 동안 사용자 이름 조회를 반영 메시징
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/03/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91d0dbaf9b648f42ef535d8b491df04b2c7042d7
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007567"
---
# <a name="home-realm-discovery-during-sign-in-for-microsoft-365-services"></a>Microsoft 365 서비스에 대 한 로그인 하는 동안 홈 영역 검색

이 Azure Active Directory (Azure AD)의 로그인 동작을 새 인증 방법에 대 한 공간을 확보 기능성을 개선 변경 합니다. 로그인 하는 동안 Azure AD 사용자를 인증 해야 하는 위치를 결정 합니다. Azure AD 로그인 페이지에서 입력 한 사용자 이름에 대 한 조직 및 사용자 설정을 참조 하 여 결정을 내릴 수 있습니다. FIDO 2.0과 같은 추가 자격 증명을 사용 하도록 설정 하는 암호가 없는 향후 위한 단계입니다.

## <a name="home-realm-discovery-behavior"></a>홈 영역 검색 동작

지금까지 홈 영역 검색 로그인에 제공 되는 도메인에서 또는 일부 레거시 응용 프로그램에 대 한 홈 영역 검색 정책에 의해 적용 되었습니다. 예를 들어 이전 검색 동작을 Azure Active Directory 사용자를 자신의 사용자 이름을 잘못 입력 수 있지만 여전히 조직의 자격 증명 모음 화면에 도착 합니다. 이 사용자는 조직의 도메인 이름을 "contoso.com"을 올바르게 제공 하는 경우 발생 합니다. 이 동작은 개별 사용자에 대 한 환경에 맞게 세분성을 허용 하지 않습니다.

광범위 한 자격 증명 지원 향상을 이제 Azure Active Directory의 사용자 이름 조회 동작 로그인 프로세스 중 업데이트 됩니다. 새 동작 기반 로그인 페이지에서 입력 한 사용자 이름으로 테 넌 트 및 사용자 수준 설정을 읽는 결정을 내릴 수 있습니다. 가능 하 게 하려면이 Azure Active Directory 로그인 페이지에 입력 된 사용자가 지정한 도메인에 있는지 또는 자격 증명을 제공 하도록 사용자를 리디렉션합니다 확인 됩니다. 

이 작업의 또 다른 이점은 향상 된 오류 메시지입니다. Azure Active Directory 사용자만 지 원하는 응용 프로그램에 로그인 할 때 메시징 향상 된 오류의 몇 가지 예는 다음과 같습니다.

1. 사용자를 잘못 입력 하거나 사용자 이름은 아직 Azure AD에 동기화 되지 않았습니다.
  
    ![사용자 이름은 잘못 입력 되었거나 찾을 수 없음](./media/signin-realm-discovery/typo-username.png)
  
2. 도메인 이름은 잘못 입력 됩니다.
  
    ![도메인 이름은 잘못 입력 되었거나 찾을 수 없음](./media/signin-realm-discovery/typo-domain.png)
  
3. 사용자가 알려진된 소비자 도메인을 사용 하 여 로그인 하려고 합니다.
  
    ![알려진된 소비자 도메인으로 로그인](./media/signin-realm-discovery/consumer-domain.png)
  
4. 암호를 잘못 입력 이지만 사용자 이름을 정확 하 게 합니다.  
  
    ![암호는 적절 한 사용자 이름으로 잘못 입력](./media/signin-realm-discovery/incorrect-password.png)
  
## <a name="additional-info"></a>추가 정보

향상 된 로그인 사용자 환경 외에도이 변경 대규모 사용자 이름이 열거의 악용을 완화 하는 데 도움이 되는 메커니즘을 포함 합니다.

이 변경 관리 되는 도메인에 대 한 대상으로 처음에 월 2019에 롤아웃하기 시작 하 고 있지만 2019의 끝에서 페더레이션된 도메인을 롤아웃하기 시작 되지 않습니다. 페더레이션된 도메인에 대 한 정확한 출시 날짜는 고객의 의견에 따라 달라 집니다.

> [!IMPORTANT]
> 이 기능 이전 도메인 수준 강제로 페더레이션 홈 영역 검색에 의존 하는 페더레이션된 도메인에 상당한 영향을 미치게 됩니다. 일부 조직에서는 학습 직원이 라우팅하므로 도메인 이름이 사용자가 현재 조직의 도메인 끝점에 Azure Active Directory에 존재 하지 않지만 적절 한 도메인 이름을 포함 하는 사용자 이름으로 로그인 합니다. 새 로그인 동작에이 허용 하지 않습니다. 사용자 이름을 수정 하려면 사용자가 알림을 한 경우 Azure Active Directory에서 존재 하지 않는 사용자 이름을 사용 하 여 로그인에 허용 되지 않습니다.
>
> 사용자 또는 사용자 조직에 사례 이전 동작에 의존 하는 경우 조직 관리자가 직원의 로그인 및 인증 설명서를 업데이트 하 고, Azure Active Directory 사용자 이름을 사용 하 여 로그인 하는 직원의 학습에 대 한 중요 한 것입니다.
  
를 새 동작으로 문제가 있는 경우에 설명이 남겨 주세요 합니다 **피드백** 이 문서의 섹션입니다.  

## <a name="next-steps"></a>다음 단계

[브랜딩 사용자 지정 프로그램에 로그인](../fundamentals/add-custom-domain.md)