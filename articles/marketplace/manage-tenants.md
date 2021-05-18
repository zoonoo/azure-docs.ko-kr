---
title: 상업용 Marketplace의 테넌트 관리 - Azure Marketplace
description: 파트너 센터의 상업용 Marketplace 프로그램을 위한 테넌트를 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 79120eb6d3d5aad9c27ed42bc825fdad4907af49
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108144765"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>상업용 Marketplace의 테넌트 관리

**적절한 역할**

- Manager

이 설명서 전체에서 *회사 계정* 이라고도 하는 Azure AD(Active Directory) 테넌트는 Azure Portal에 설정된 조직을 나타내는 표현이며, 내외부 사용자에 대한 특정 Microsoft 클라우드 서비스 인스턴스를 관리하는 데 도움이 됩니다. 조직이 Microsoft 클라우드 서비스(Azure, Microsoft Intune 또는 Microsoft 365 등)에 가입한 경우 사용자를 위한 Azure AD 테넌트가 설정되었습니다.

파트너 센터에서 사용할 테넌트를 다수 설정할 수 있습니다. 회사에 여러 개의 테넌트(예: contoso.com, contoso.uk 등)가 있는 경우 이 작업을 수행하려고 할 수 있습니다. 여기에서 테넌트를 추가로 연결할 수 있습니다. 이 연결을 통해 상업용 Marketplace 계정의 추가 테넌트에서 사용자를 추가하고 관리할 수 있습니다.

파트너 센터 계정의 관리자 역할을 보유한 사용자에게는 계정에서 Azure AD 테넌트를 추가 및 제거할 수 있는 옵션이 주어집니다.

## <a name="add-an-existing-tenant"></a>기존 테넌트 추가

파트너 센터 계정과 다른 Azure AD 테넌트를 연결하려면 다음을 수행합니다.

1. 파트너 센터의 오른쪽 위에서 **설정** > **계정 설정** 을 선택합니다.
1. **조직 프로필** 에서 **테넌트** 를 선택합니다. 현재 테넌트 연결이 표시됩니다.
1. **개발자** 탭에서 **연결** 을 선택합니다.
1. 연결하려는 테넌트에 대한 Azure AD 자격 증명을 입력합니다.
1. Azure AD 테넌트에 대한 조직 및 도메인 이름을 검토합니다. 연결을 완료하려면 **확인** 을 선택합니다.

연결에 성공하면 파트너 센터의 사용자 섹션에서 계정 사용자를 추가하고 관리할 준비가 된 것입니다. 사용자 추가에 대해 알아보려면 [사용자 관리](add-manage-users.md)를 참조하세요.

## <a name="create-a-new-tenant"></a>새 테넌트 만들기

파트너 센터 계정에서 새로운 Azure AD 테넌트를 만들려면 다음을 수행합니다.

1. 파트너 센터의 오른쪽 위에서 **설정** > **계정 설정** 을 선택합니다.
1. **조직 프로필** 에서 **테넌트** 를 선택합니다. 현재 테넌트 연결이 표시됩니다.
1. 개발자 탭에서 **만들기** 를 선택합니다.
1. 새 Azure AD에 대한 디렉터리 정보를 입력합니다.
    - **도메인 이름**: Azure AD 도메인에 사용할 고유 이름으로, "onmicrosoft.com"과 함께 사용합니다. 예를 들어 "example"을 입력한 경우 Azure AD 도메인은 "example.onmicrosoft.com"이 됩니다.
    - **연락처 전자 메일**: 필요한 경우 계정에 대해 문의할 수 있는 전자 메일 주소입니다.
    - **전역 관리자 사용자 계정 정보**: 새 전역 관리자 계정에 사용할 이름, 성, 사용자 이름 및 암호입니다.
1. 만들기를 선택하여 새 도메인과 연락처 정보를 확인합니다.
1. Azure AD 전역 관리자 사용자 이름과 암호로 로그인하여 [사용자 추가 및 관리](add-manage-users.md)를 시작합니다.

파트너 센터 포털 대신 Azure Portal 내에서 새 테넌트를 만드는 방법에 대한 자세한 내용은 [Azure Active Directory에서 새 테넌트 만들기](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) 문서를 참조하세요.

## <a name="remove-a-tenant"></a>테넌트 제거

파트너 센터 계정에서 테넌트를 제거하려면 **테넌트** 페이지(**계정 설정**)에서 해당 이름을 찾아 **제거** 를 선택합니다. 테넌트를 제거할 것인지 확인하는 메시지가 표시됩니다. 테넌트를 제거한 후 해당 테넌트의 사용자가 파트너 센터 계정에 로그인할 수 없으며, 해당 사용자에 대해 구성한 모든 권한이 제거됩니다.

> [!TIP]
> 현재 동일한 테넌트의 계정을 사용하여 파트너 센터에 로그인하는 경우 테넌트를 제거할 수 없습니다. 테넌트를 제거하려면 계정과 연결된 다른 테넌트에 대한 **관리자** 로서 파트너 센터에 로그인해야 합니다. 계정과 연결된 테넌트가 하나뿐인 경우 해당 테넌트는 계정을 연 Microsoft 계정으로 로그인해야만 제거할 수 있습니다.