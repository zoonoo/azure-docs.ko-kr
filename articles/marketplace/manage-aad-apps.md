---
title: Azure AD 애플리케이션 추가 및 관리 - Azure Marketplace
description: 파트너 센터의 상업용 Marketplace 프로그램을 위해 Azure AD 애플리케이션을 추가하고 관리하는 방법을 알아봅니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108090"
---
# <a name="add-and-manage-azure-ad-applications"></a>Azure AD 애플리케이션 추가 및 관리

**적절한 역할**

- 소유자
- Manager

회사의 Azure AD(Azure Active Directory)에 속한 애플리케이션 또는 서비스에서 파트너 센터 계정에 액세스하도록 허용할 수 있습니다.

## <a name="add-existing-azure-ad-applications"></a>기존 Azure AD 애플리케이션 추가

회사의 Azure Active Directory에 이미 있는 애플리케이션을 추가하려면 다음을 수행합니다.

1. **사용자** 페이지(**계정 설정** 아래)에서 **Azure AD 애플리케이션 추가** 를 선택합니다.
1. 표시되는 목록에서 하나 이상의 Azure AD 애플리케이션을 선택합니다. 검색 상자를 사용하여 특정 Azure AD 애플리케이션을 검색할 수 있습니다. 파트너 센터 계정에 추가할 Azure AD 애플리케이션을 두 개 이상 선택하는 경우 동일한 역할 또는 사용자 지정 권한 집합을 할당해야 합니다. 역할/권한이 다른 여러 Azure AD 애플리케이션을 추가하려면 각각의 역할 또는 사용자 지정 권한 집합에서 이러한 단계를 반복합니다.
1. Azure AD 애플리케이션 선택을 완료하면 **선택한 항목 추가** 를 선택합니다.
1. **역할** 섹션에서 선택한 Azure AD 애플리케이션에 대한 역할 또는 사용자 지정 권한을 지정합니다.
1. **저장** 을 선택합니다.

## <a name="add-new-azure-ad-applications"></a>새 Azure AD 애플리케이션 추가

파트너 센터에 새로운 Azure AD 애플리케이션 계정에 대한 액세스 권한을 부여하려는 경우 **사용자** 섹션에서 만들 수 있습니다. 이렇게 하면 파트너 센터 계정뿐만 아니라 회사 계정(Azure AD 테넌트)에서도 새 계정이 만들어집니다. 파트너 센터 인증에 이 Azure AD 애플리케이션을 주로 이용하고 사용자가 직접 액세스하지 않아도 되는 경우, 해당 값이 디렉터리의 다른 Azure AD 애플리케이션에서 사용되지 않는 한 **회신 URL** 및 **앱 ID URI** 에 유효한 주소를 입력할 수 있습니다.

1. **사용자** 페이지(**계정 설정** 아래)에서 **Azure AD 애플리케이션 추가** 를 선택합니다.
1. 다음 페이지에서 **새 Azure AD 애플리케이션** 을 선택합니다.
1. 새 Azure AD 애플리케이션에 대한 **회신 URL** 을 입력합니다. 이는 사용자가 로그인하여 Azure AD 애플리케이션을 이용할 수 있는 URL입니다(앱 URL 또는 로그온 URL이라고도 함). *회신 URL* 은 256자를 초과할 수 없으며 디렉터리 내에서 고유해야 합니다.
1. 새 Azure AD 애플리케이션에 대한 **앱 ID URI** 를 입력합니다. 이는 Single Sign-On 요청이 Azure AD에 전송될 때 제공되는 Azure AD 애플리케이션의 논리 식별자입니다. *앱 ID URI* 는 디렉터리의 Azure AD 애플리케이션마다 고유해야 합니다. 이 ID는 256자를 초과할 수 없습니다. 앱 ID URI에 대한 자세한 내용은 [Azure Active Directory와 애플리케이션 통합](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)을 참조하세요.
1. **역할** 섹션에서 Azure AD 애플리케이션에 대한 역할 또는 사용자 지정된 권한을 지정합니다.
1. **저장** 을 선택합니다.

Azure AD 애플리케이션을 추가하거나 만든 후 **사용자** 섹션으로 되돌아가 애플리케이션 이름을 선택하면 테넌트 ID, 클라이언트 ID, 회신 URL, 앱 ID URI 등의 애플리케이션 설정을 검토할 수 있습니다.

## <a name="remove-an-azure-ad-application"></a>Azure AD 애플리케이션 제거

작업 계정(Azure AD 테넌트)에서 애플리케이션을 제거하려면, **사용자**(**계정 설정** 아래)로 이동하여 맨 오른쪽 열의 확인란을 통해 제거할 애플리케이션을 선택한 후 사용 가능한 작업에서 **제거** 를 선택합니다. 선택한 애플리케이션을 제거할 것인지 확인하는 팝업 창이 표시됩니다.

## <a name="manage-keys-for-an-azure-ad-application"></a>Azure AD 애플리케이션용 키 관리

Azure AD 애플리케이션에서 Microsoft Azure AD의 데이터를 읽고 쓰는 경우 키가 필요합니다. 파트너 센터에서 해당 정보를 편집하여 Azure AD 애플리케이션용 키를 만들 수 있습니다. 더 이상 필요하지 않은 키를 제거할 수도 있습니다.

1. **사용자** 페이지(**계정 설정** 아래)에서 Azure AD 애플리케이션의 이름을 선택합니다. 키가 만들어진 날짜와 만료될 시기(50)를 포함하여 Azure AD 애플리케이션에 대한 활성 키가 모두 표시됩니다.
1. 더 이상 필요하지 않은 키를 제거하려면 **제거** 를 선택합니다.
1. 새 키를 추가하려면 **새 키 추가** 를 선택합니다.
1. **클라이언트 ID** 와 **키 값** 을 보여 주는 화면이 표시됩니다. 이 페이지에서 나가면 해당 정보에 다시 액세스할 수 없으므로, 이 정보를 인쇄하거나 복사하도록 합니다.
1. 더 많은 키를 만들려면 **다른 키 추가** 를 선택합니다.
