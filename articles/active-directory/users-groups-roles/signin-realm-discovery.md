---
title: 로그인 Azure Active Directory 하는 동안 사용자 이름 조회 | Microsoft Docs
description: Azure Active Directory에서 로그인 하는 동안 화면 메시지에서 사용자 이름 조회를 반영 하는 방법
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: kexia
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1f27c7b91a78da8944c23fd353d3b6791b3e015
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582562"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active Directory 로그인 페이지에 대 한 홈 영역 검색

새 인증 방법을 위한 공간을 확보 하 고 유용성을 향상 시키기 위해 Azure Active Directory (Azure AD) 로그인 동작을 변경 합니다. 로그인 하는 동안 Azure AD는 사용자가 인증 해야 하는 위치를 결정 합니다. Azure AD에서는 로그인 페이지에 입력 한 사용자 이름에 대 한 조직 및 사용자 설정을 읽어 지능적 결정을 내립니다. 이는 FIDO 2.0와 같은 추가 자격 증명을 사용 하는 암호 없는 미래를 위한 단계입니다.

## <a name="home-realm-discovery-behavior"></a>홈 영역 검색 동작

지금까지 홈 영역 검색은 일부 레거시 응용 프로그램에 대 한 홈 영역 검색 정책 또는 로그인 시 제공 되는 도메인에 의해 제어 됩니다. 예를 들어 검색 동작에서 사용자의 사용자 이름을 잘못 입력 한 경우에도 해당 조직의 자격 증명 컬렉션 화면에 도착 하는 Azure Active Directory 있습니다. 이는 사용자가 조직의 도메인 이름 "contoso.com"를 올바르게 제공할 때 발생 합니다. 이 동작으로 개별 사용자에 대 한 환경을 사용자 지정할 수는 없습니다.

더 광범위 한 자격 증명을 지원 하 고 유용성을 높이려면 로그인 프로세스 중에 Azure Active Directory의 사용자 이름 조회 동작이 업데이트 됩니다. 새 동작은 로그인 페이지에 입력 된 사용자 이름을 기반으로 조직 수준 및 사용자 수준 설정을 읽어 지능적 결정을 내립니다. 이러한 작업을 가능 하 게 하려면 로그인 페이지에 입력 된 사용자 이름이 지정 된 도메인에 있는지 확인 하거나 사용자에 게 자격 증명을 제공 하도록 리디렉션합니다 Azure Active Directory 합니다.

이 작업의 추가 혜택은 향상 된 오류 메시지입니다. Azure Active Directory 사용자만을 지 원하는 응용 프로그램에 로그인 하는 경우 향상 된 오류 메시징의 몇 가지 예는 다음과 같습니다.

- 사용자 이름의 철자가 잘못 되었거나 사용자 이름이 아직 Azure AD와 동기화 되지 않았습니다.
  
    ![사용자 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/signin-realm-discovery/typo-username.png)
  
- 도메인 이름의 철자가 잘못 되었습니다.
  
    ![도메인 이름이 잘못 입력 되었거나 찾을 수 없습니다.](./media/signin-realm-discovery/typo-domain.png)
  
- 사용자가 알려진 소비자 도메인으로 로그인 하려고 합니다.
  
    ![알려진 소비자 도메인으로 로그인](./media/signin-realm-discovery/consumer-domain.png)
  
- 암호를 잘못 입력 했지만 사용자 이름이 정확 합니다.  
  
    ![올바른 사용자 이름으로 암호의 철자가 잘못 되었습니다.](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 이 기능은 페더레이션을 강제로 적용 하기 위해 이전 도메인 수준 홈 영역 검색에 의존 하는 페더레이션된 도메인에 영향을 미칠 수 있습니다. 페더레이션된 도메인 지원을 추가 하는 경우에 대 한 업데이트는 [Microsoft 365 services에 로그인 하는 동안 홈 영역 검색](https://azure.microsoft.com/updates/signin-hrd/)을 참조 하세요. 한편, 일부 조직에서는 Azure Active Directory에 없지만 올바른 도메인 이름을 포함 하는 사용자 이름으로 로그인 하는 직원을 교육 했습니다. 도메인 이름은 현재 조직의 도메인 끝점으로 사용자를 라우팅합니다. 새 로그인 동작은이를 허용 하지 않습니다. 사용자에 게 사용자 이름을 수정 하 라는 알림이 표시 되 고 Azure Active Directory에 없는 사용자 이름으로 로그인 할 수 없습니다.
>
> 사용자 또는 조직에서 이전 동작에 의존 하는 사례를 사용 하는 경우 조직 관리자가 직원 로그인 및 인증 설명서를 업데이트 하 고 직원의 Azure Active Directory 사용자 이름을 사용 하 여 로그인 하도록 교육 하는 것이 중요 합니다.
  
새 동작에 문제가 있는 경우이 문서의 **사용자 의견** 섹션에서 설명을 그대로 두십시오.  

## <a name="next-steps"></a>다음 단계

[로그인 브랜딩 사용자 지정](../fundamentals/add-custom-domain.md)
