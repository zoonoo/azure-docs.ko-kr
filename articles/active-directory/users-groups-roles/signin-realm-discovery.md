---
title: 로그인 하는 동안 사용자 이름 조회 - Azure 활성 디렉터리 | 마이크로 소프트 문서
description: Azure Active Directory에서 로그인하는 동안 화면 메시징이 사용자 이름 조회를 반영하는 방법
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
ms.openlocfilehash: c8b6a65a964016f702fcf75aa4cbdab33a952e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024246"
---
# <a name="home-realm-discovery-for-azure-active-directory-sign-in-pages"></a>Azure Active 디렉터리 로그인 페이지에 대한 홈 영역 검색

새 인증 방법을 위한 공간을 마련하고 유용성을 개선하기 위해 Azure Active Directory(Azure AD) 로그인 동작을 변경하고 있습니다. 로그인하는 동안 Azure AD는 사용자가 인증해야 하는 위치를 결정합니다. Azure AD는 로그인 페이지에 입력한 사용자 이름에 대한 조직 및 사용자 설정을 읽음으로써 지능형 결정을 내보릅니다. 이것은 FIDO 2.0과 같은 추가 자격 증명을 가능하게하는 암호가없는 미래를 향한 단계입니다.

## <a name="home-realm-discovery-behavior"></a>홈 영역 검색 동작

지금까지 홈 영역 검색은 일부 레거시 응용 프로그램에 대해 로그인 시 또는 홈 Realm 검색 정책에 의해 제공되는 도메인에 의해 관리되었습니다. 예를 들어 검색 동작에서 Azure Active Directory 사용자는 사용자 이름을 잘못 입력할 수 있지만 조직의 자격 증명 수집 화면에 계속 도달할 수 있습니다. 이 문제는 사용자가 조직의 도메인 이름 "contoso.com"을 올바르게 제공하는 경우에 발생합니다. 이 동작은 세분성이 개별 사용자에 대한 환경을 사용자 지정하는 것을 허용하지 않습니다.

더 넓은 범위의 자격 증명을 지원하고 유용성을 높이기 위해 로그인 프로세스 중에 Azure Active Directory의 사용자 이름 조회 동작이 업데이트되었습니다. 새 동작은 로그인 페이지에 입력한 사용자 이름을 기반으로 테넌트 및 사용자 수준 설정을 읽음으로써 지능적인 결정을 내립니다. 이를 위해 Azure Active Directory는 로그인 페이지에 입력된 사용자 이름이 지정된 도메인에 있는지 확인하거나 사용자를 리디렉션하여 자격 증명을 제공하도록 합니다.

이 작업의 또 다른 이점은 향상된 오류 메시징입니다. 다음은 Azure Active Directory 사용자만 지원하는 응용 프로그램에 로그인할 때 향상된 오류 메시징의 몇 가지 예입니다.

- 사용자 이름이 잘못 입력되었거나 사용자 이름이 아직 Azure AD에 동기화되지 않았습니다.
  
    ![사용자 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/signin-realm-discovery/typo-username.png)
  
- 도메인 이름이 잘못 입력되었습니다.
  
    ![도메인 이름이 잘못 입력되어 있거나 찾을 수 없습니다.](./media/signin-realm-discovery/typo-domain.png)
  
- 사용자는 알려진 소비자 도메인으로 로그인하려고 시도합니다.
  
    ![알려진 소비자 도메인으로 로그인](./media/signin-realm-discovery/consumer-domain.png)
  
- 암호가 잘못 입력되었지만 사용자 이름이 정확합니다.  
  
    ![암호가 좋은 사용자 이름으로 잘못 입력되었습니다.](./media/signin-realm-discovery/incorrect-password.png)
  
> [!IMPORTANT]
> 이 기능은 이전 도메인 수준 홈 Realm 검색에 의존하여 페더레이션도메인을 강제로 페더레이션하는 페더레이션 도메인에 영향을 미칠 수 있습니다. 페더레이션 도메인 지원이 추가되는 시기에 대한 업데이트는 [Microsoft 365 서비스에 대해 로그인하는 동안 홈 영역 검색을](https://azure.microsoft.com/updates/signin-hrd/)참조하십시오. 그 동안 일부 조직에서는 도메인 이름이 현재 사용자의 조직을 조직의 도메인 끝점으로 라우팅하기 때문에 Azure Active Directory에 없지만 적절한 도메인 이름을 포함하는 사용자 이름으로 로그인하도록 직원을 교육했습니다. 새 로그인 동작에서는 이를 허용하지 않습니다. 사용자에게 사용자 이름을 수정하라는 알림이 표시되며 Azure Active Directory에 없는 사용자 이름으로 로그인할 수 없습니다.
>
> 사용자 또는 조직에 이전 동작에 종속된 관행이 있는 경우 조직 관리자는 직원 로그인 및 인증 설명서를 업데이트하고 직원이 Azure Active Directory 사용자 이름을 사용하여 로그인하도록 교육하는 것이 중요합니다.
  
새 동작에 문제가 있는 경우 이 문서의 **피드백** 섹션에 의견을 남겨 주세요.  

## <a name="next-steps"></a>다음 단계

[로그인 브랜딩 사용자 지정](../fundamentals/add-custom-domain.md)
