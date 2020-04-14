---
title: 회사 회사 계정 및 파트너 센터
description: 회사에 Microsoft로 회사 계정이 설정되어 있는지 확인하거나, 새 회사 계정을 만들거나, 파트너 센터에서 사용할 여러 회사 계정을 설정하는 방법.
author: dsindona
ms.author: parthp
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 3c0ee2f8d5291f7904435dea32d913adeaaf25c5
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262369"
---
# <a name="company-work-accounts-and-partner-center"></a>회사 회사 계정 및 파트너 센터

파트너 센터는 AD(Azure Active Directory) 테넌토리라고도 하는 회사 회사 회사 계정을 사용하여 여러 사용자에 대한 계정 액세스를 관리하고, 사용 권한, 호스트 그룹 및 응용 프로그램을 제어하고, 프로필 데이터를 유지 관리합니다. 회사의 회사 전자 메일 계정 도메인을 파트너 센터 계정에 연결하면 회사의 직원이 파트너 센터에 로그인하여 자신의 회사 계정 사용자 이름과 암호를 사용하여 마켓플레이스 오퍼를 관리할 수 있습니다.

## <a name="check-whether-your-company-already-has-a-work-account"></a>회사에 회사 계정이 이미 있는지 확인

회사에서 Azure, Microsoft Intune 또는 Office 365와 같은 Microsoft 클라우드 서비스에 가입한 경우 파트너 센터에서 사용할 수 있는 회사 전자 메일 계정 도메인(Azure Active Directory 테넌트이라고도 함)이 이미 있습니다.

다음 단계를 수행하여 다음을 확인합니다.
1. 에서 https://portal.azure.comAzure 관리 포털에 로그인합니다.
2. 왼쪽 탐색 메뉴에서 **Azure Active Directory를** 선택한 다음 **사용자 지정 도메인 이름을**선택합니다.
3. 이미 회사 계정이 있는 경우 도메인 이름이 나열됩니다.

회사에 아직 회사 계정이 없는 경우 파트너 센터 등록 프로세스 중에 회사 계정이 만들어집니다.

## <a name="set-up-multiple-work-accounts"></a>여러 직장 계정 설정

기존 직장 계정을 사용하기로 결정하기 전에 파트너 센터에 액세스해야 하는 사용자 수를 고려하십시오. 파트너 센터에 액세스할 필요가 없는 사용자(사내 계정)에 있는 사용자가 있는 경우 파트너 센터에 액세스해야 하는 사용자만 특정 계정에 표시되도록 여러 직장 계정을 만드는 것이 좋습니다.

## <a name="create-a-new-work-account"></a>새 직장 계정 만들기

회사에 대한 새 회사 계정을 만들려면 아래 단계를 따르세요. 회사의 Microsoft Azure 계정에 대한 관리 권한이 있는 모든 사람에게 도움을 요청해야 할 수 있습니다.

1. Microsoft Azure [포털에](https://portal.azure.com)로그인합니다.
2. 왼쪽 탐색 메뉴에서 **Azure Active Directory** -> **사용자를**선택합니다.
3. **새 사용자를** 선택하고 이름과 전자 메일 주소를 입력하여 새 Azure 직장 계정을 만듭니다. **디렉터리 역할이** **사용자로** 설정되어 있는지 확인하고 하단의 **암호 표시** 확인란을 선택하여 자동 생성된 암호를 보고 기록합니다.
4. 새 사용자를 저장하려면 **만들기를** 선택합니다.

사용자 계정의 전자 메일 주소는 디렉터리에서 확인된 도메인 이름이어야 합니다. 왼쪽 탐색 메뉴에서 **Azure Active Directory** -> 사용자 지정 도메인 이름을 선택하여 디렉터리에서 확인된 모든**도메인을** 나열할 수 있습니다.

Azure Active Directory에서 사용자 지정 도메인을 추가하는 방법에 대해 자세히 알아보려면 [Azure AD의 도메인 추가 또는 연결](https://docs.microsoft.com/azure/active-directory/active-directory-add-domain)을 참조하세요.

## <a name="troubleshoot-work-email-sign-in"></a>작업 이메일 로그인 문제 해결

사용자 계정(Azure AD 테넌트이라고도 함)에 로그인하는 데 문제가 있는 경우 아래 다이어그램에서 상황에 가장 적합한 시나리오를 찾아 권장 단계를 따릅니다.

![직장 계정 로그인 문제 해결을 위한 다이어그램](./media/onboarding-aad-flow.png)

## <a name="next-steps"></a>다음 단계

- [파트너 센터에서 상용 마켓플레이스 계정 관리](./manage-account.md) 
