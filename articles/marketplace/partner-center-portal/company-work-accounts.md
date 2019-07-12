---
title: 파트너 센터 및 회사 작업 계정
description: 회사가 Microsoft를 사용 하 여 설정 되 고이 상태를 새 작업 계정을 만들거나 여러 회사 계정의 파트너 센터를 사용 하 여 사용 하도록 설정 하는 작업 계정에 있는지 여부를 확인 하는 방법입니다.
author: mattwojo
manager: evansma
ms.author: parthp
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: d9326cab6cb5ed4ca76c9a84654697f9f90bcfcd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67619558"
---
# <a name="company-work-accounts-and-partner-center"></a>파트너 센터 및 회사 작업 계정

파트너 센터 회사 작업 계정 또는 Azure AD (Active Directory) 테 넌 트를 사용 하 여 여러 사용자, 권한, 호스트 그룹 및 응용 프로그램에 대 한 계정 액세스를 관리 하 고 프로필 데이터를 유지 합니다. 파트너 센터 계정에 회사의 작업 전자 메일 계정 도메인을 연결 하 여 회사의 직원 로그인 할 수 파트너 센터 자신의 작업 계정 사용자 이름 및 암호를 사용 하 여 marketplace 제품을 관리할 수 있습니다.

## <a name="check-whether-your-company-already-has-a-work-account"></a>회사에 이미 회사 계정이 있는지 여부를 확인 합니다.

Azure, Microsoft Intune 또는 Office 365 같은 Microsoft 클라우드 서비스에 회사에서 구독 한 다음 이미 있는 경우 회사 전자 메일 계정 도메인 (Azure Active Directory 테 넌 트 라고도 함) 파트너 센터를 사용 하 여 사용할 수 있는 합니다.

확인 하려면 다음이 단계를 수행 합니다.
1. 에 Azure 관리 포털에 로그인 https://portal.azure.com 합니다.
2. 선택 **Azure Active Directory** 왼쪽 탐색 메뉴에서 선택 **사용자 지정 도메인 이름**합니다.
3. 회사 계정에 이미 있는 경우 도메인 이름을 나열 됩니다.

회사에 회사 계정을 아직 만들어집니다를 파트너 센터 등록 프로세스 중입니다.

## <a name="set-up-multiple-work-accounts"></a>회사 계정을 여러 개 설정

를 기존 작업 계정을 사용 하도록 결정 하기 전에 작업 계정에 얼마나 많은 사용자가 파트너 센터에 액세스 해야 하는 것이 좋습니다. 파트너 센터에 액세스할 필요가 있는 작업 계정에 있는 사용자가 수 하려는 경우 회사 계정을 여러 개 만드는 것이 좋습니다는 특정 계정에 파트너 센터에 액세스 하는 데 필요한 사용자만 표시 됩니다.

## <a name="create-a-new-work-account"></a>새 작업 계정 만들기

회사에 대 한 새 작업 계정을 만들려면 아래 단계를 수행 합니다. 회사의 Microsoft Azure 계정에 대 한 관리 권한을 가진 누구 든 지 지원을 요청 해야 합니다.

1. [Microsoft Azure 포털](https://portal.azure.com)에 로그인합니다.
2. 왼쪽된 탐색 메뉴에서 선택 합니다 **Azure Active Directory** -> **사용자**합니다.
3. 선택 **새 사용자** 이름 및 전자 메일 주소를 입력 하 여 새 Azure 작업 계정을 만들어야 합니다. 있는지 확인 합니다 **디렉터리 역할** 로 설정 되어 **사용자** 선택한를 **암호 표시** 를 보고 하는 자동으로 생성 된 암호를 기록해 맨 아래에 있는 확인란을 선택 합니다.
4. 선택 **만들기** 새 사용자를 저장 합니다.

사용자 계정에 대 한 전자 메일 주소에는 디렉터리에는 확인 된 도메인 이름 이어야 합니다. 선택 하 여 디렉터리의 모든 확인 된 도메인을 나열할 수 있습니다 **Azure Active Directory** -> **사용자 지정 도메인 이름** 왼쪽 탐색 메뉴에서.

Azure Active Directory에서 사용자 지정 도메인을 추가 하는 방법에 대 한 자세한 내용은 참조 하세요 [추가 또는 연결 된 Azure AD에서 도메인](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)합니다.

## <a name="troubleshoot-work-email-sign-in"></a>회사 전자 메일 로그인 문제 해결

회사 계정 (라고도 Azure AD 테 넌 트)에 로그인 하는 데 문제가 있는 경우 가장 아래 다이어그램에 시나리오 상황에 따라 일치 찾아 권장된 단계를 따릅니다.

![작업 계정 로그인 문제 해결에 대 한 다이어그램](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>다음 단계

- [파트너 센터에서 상용 마켓플레이스 계정 관리](./manage-account.md) 
