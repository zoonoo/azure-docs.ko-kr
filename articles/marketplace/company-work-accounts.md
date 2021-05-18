---
title: 회사 계정 및 파트너 센터
description: 회사가 Microsoft에 설정된 회사 계정이 있는지 확인하거나, 새 회사 계정을 만들거나, 파트너 센터에서 사용할 여러 개의 회사 계정을 설정하는 방법입니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 05/30/2019
ms.openlocfilehash: 69e9f9d6d7d132476796a1cdfdaaa2490457640c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107107915"
---
# <a name="company-work-accounts-and-partner-center"></a>회사 계정 및 파트너 센터

파트너 센터는 AD(Azure Active Directory) 테넌트라고도 하는 회사 계정을 사용하여 여러 사용자에 대한 계정 액세스, 제어 권한, 호스트 그룹, 애플리케이션을 관리하고 프로필 데이터를 유지 관리합니다. 회사 이메일 계정 도메인을 파트너 센터 계정에 연결하면 회사 직원이 자신의 회사 계정 사용자 이름 및 암호를 사용하여 파트너 센터에 로그인해서 마켓플레이스 제품을 관리할 수 있습니다.

## <a name="check-whether-your-company-already-has-a-work-account"></a>회사가 이미 회사 계정을 가지고 있는지 확인

회사가 Azure, Microsoft Intune 또는 Microsoft 365와 같은 Microsoft 클라우드 서비스를 구독한 경우 파트너 센터에서 사용할 수 있는 회사 이메일 계정 도메인(Azure Active Directory 테넌트라고도 함)이 이미 있는 것입니다.

다음 단계를 따라 확인합니다.
1. Azure 관리 포털(https://portal.azure.com )에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **Azure Active Directory** 를 선택한 다음 **사용자 지정 도메인 이름** 을 선택합니다.
3. 이미 회사 계정이 있는 경우 도메인 이름이 나열됩니다.

회사가 아직 회사 계정을 가지고 있지 않은 경우 파트너 센터 등록 프로세스 중에 만들어집니다.

## <a name="set-up-multiple-work-accounts"></a>여러 개의 회사 계정 설정

기존 회사 계정을 사용하도록 결정하기 전에 파트너 센터에 액세스해야 하는 회사 계정의 사용자 수가 얼마나 되는지 고려합니다. 파트너 센터에 액세스할 필요가 없는 회사 계정의 사용자가 있는 경우 파트너 센터에 액세스해야 하는 사용자만 특정 계정에 표시되도록 여러 회사 계정을 만드는 것이 좋습니다.

## <a name="create-a-new-work-account"></a>새 회사 계정 만들기

회사에 대해 새 회사 계정을 만들려면 아래의 단계를 따릅니다. 회사의 Microsoft Azure 계정에 대한 관리자 권한이 있는 사용자에게 도움을 요청해야 할 수 있습니다.

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **Azure Active Directory** -> **사용자** 를 선택합니다.
3. **새 사용자** 를 선택하고 이름과 이메일 주소를 입력하여 Azure 회사 계정을 새로 만듭니다. **디렉터리 역할** 이 **사용자** 로 설정되어 있는지 확인하고 맨 아래에 있는 **암호 표시** 확인란을 선택하여 자동 생성된 암호를 확인하고 기록해 둡니다.
4. **만들기** 를 선택하여 새 사용자를 저장합니다.

사용자 계정에 대한 이메일 주소는 디렉터리의 확인된 도메인 이름이어야 합니다. 왼쪽 탐색 메뉴에서 **Azure Active Directory** ->  **사용자 지정 도메인 이름** 을 선택하여 디렉터리의 확인된 도메인을 모두 나열할 수 있습니다.

Azure Active Directory에 사용자 지정 도메인 추가에 대해 자세히 알아보려면 [Azure AD에 도메인 추가 또는 연결](/azure/active-directory/fundamentals/add-custom-domain)을 참조하세요.

## <a name="troubleshoot-work-email-sign-in"></a>회사 이메일 로그인 문제 해결

회사 계정(Azure AD 테넌트라고도 함)에 로그인하는 데 문제가 있는 경우 아래의 다이어그램에서 해당 상황에 가장 일치하는 시나리오를 찾아 권장되는 단계를 따릅니다.

![회사 계정 로그인 문제 해결을 위한 다이어그램](./media/manage-accounts/onboarding-aad-flow.png)

## <a name="next-steps"></a>다음 단계

- [파트너 센터에서 상업용 Marketplace 계정 관리](./manage-account.md)