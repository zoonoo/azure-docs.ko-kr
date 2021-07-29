---
title: Azure AD Domain Services에서 범위가 지정된 동기화 | Microsoft Docs
description: Azure Portal을 사용하여 Azure AD에서 Azure Active Directory Domain Services가 관리되는 도메인으로 범위가 지정되는 동기화를 구성하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34692f5e563e4931a27ea59db84d9c88f27817da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98660901"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure AD에서 Azure Active Directory Domain Services로 범위가 지정된 동기화 구성

인증 서비스를 제공하기 위해 Azure AD DS(Azure Active Directory Domain Services)는 Azure AD에서 사용자 및 그룹을 동기화합니다. 하이브리드 환경에서는 온-프레미스 Active Directory Domain Services (AD DS) 환경의 사용자 및 그룹을 먼저 Azure AD Connect를 사용하여 Azure AD에 동기화한 다음 Azure AD DS 관리되는 도메인에 동기화할 수 있습니다.

기본적으로 Azure AD 디렉터리 내의 모든 사용자와 그룹이 관리되는 도메인에 동기화됩니다. 특정 요구 사항이 있는 경우 정의된 사용자 집합만 동기화하도록 선택할 수 있습니다.

이 문서에서는 범위가 지정된 동기화를 구성한 다음, Azure Portal을 사용하여 범위가 지정된 사용자 집합을 변경하거나 사용하지 않도록 설정하는 방법을 알려드립니다. 또한 이 과정은 [Azure PowerShell을 사용하여 수행할 수도 있습니다][scoped-sync-powershell].

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성][tutorial-create-instance]합니다.
* Azure AD DS 동기화 범위를 변경하려면 Azure AD 테넌트에서 *전역 관리자* 권한이 필요합니다.

## <a name="scoped-synchronization-overview"></a>범위가 지정된 동기화 개요

기본적으로 Azure AD 디렉터리 내의 모든 사용자와 그룹이 관리되는 도메인에 동기화됩니다. 소수의 사용자만 관리되는 도메인에 액세스해야 하는 경우 해당 사용자 계정만 동기화할 수 있습니다. 이 범위 지정 동기화는 그룹 기반입니다. 그룹 기반 범위 지정 동기화를 구성할 때 지정한 그룹에 속한 사용자 계정만 관리되는 도메인에 동기화됩니다. 중첩된 그룹은 동기화되지 않으며 선택한 특정 그룹만 동기화됩니다.

관리되는 도메인을 만들기 전이나 후 언제든 동기화 범위를 변경할 수 있습니다. 동기화 범위는 응용 프로그램 식별자가 2565bd9d-da50-47d4-8b85-4c97f669dc36인 서비스 주체에 의해 정의됩니다. 범위 손실을 방지하려면 서비스 주체를 삭제하거나 변경하지 마세요. 실수로 삭제된 경우 동기화 범위를 복구할 수 없습니다. 

동기화 범위를 변경하는 경우 다음과 같은 주의 사항이 있습니다.

- 전체 동기화가 발생합니다.
- 관리되는 도메인에 더 이상 필요하지 않은 개체는 삭제됩니다. 새 개체는 관리되는 도메인에서 만들어집니다.

동기화 프로세스에 대한 자세한 내용은 [Azure AD Domain Services 동기화 이해][concepts-sync]를 참조하세요.

## <a name="enable-scoped-synchronization"></a>범위가 지정된 동기화 사용

Azure Portal에서 범위 지정 동기화를 사용하도록 설정하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 메뉴에서 **동기화** 를 선택합니다.
1. *동기화 유형* 에 **범위 지정** 을 선택합니다.
1. **그룹 선택** 을 선택하고 추가할 그룹을 검색하여 선택합니다.
1. 모든 변경이 완료되면 **동기화 범위 저장** 을 선택합니다.

동기화 범위를 변경하면 관리되는 도메인에서 모든 데이터를 다시 동기화합니다. 관리되는 도메인에서 더 이상 필요하지 않은 개체가 삭제되며 다시 동기화를 완료하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="modify-scoped-synchronization"></a>범위 지정 동기화 수정

사용자가 관리되는 도메인에 동기화해야 하는 그룹 목록을 수정하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 메뉴에서 **동기화** 를 선택합니다.
1. 그룹을 추가하려면 위쪽에서 **+ 그룹 선택** 을 선택하고 다음으로 추가할 그룹을 선택합니다.
1. 동기화 범위에서 그룹을 제거하려면 현재 동기화된 그룹 목록에서 해당 그룹을 선택하고 **그룹 제거** 를 선택합니다.
1. 모든 변경이 완료되면 **동기화 범위 저장** 을 선택합니다.

동기화 범위를 변경하면 관리되는 도메인에서 모든 데이터를 다시 동기화합니다. 관리되는 도메인에서 더 이상 필요하지 않은 개체가 삭제되며 다시 동기화를 완료하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="disable-scoped-synchronization"></a>범위가 지정된 동기화 해제

관리되는 도메인에 그룹 기반 범위 지정 동기화를 비활성화하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **Azure AD Domain Services** 를 검색하여 선택합니다. 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다.
1. 왼쪽 메뉴에서 **동기화** 를 선택합니다.
1. *동기화 유형* 을 **범위 비정** 에서 **전체** 로 변경한 다음, **동기화 범위 저장** 을 선택합니다.

동기화 범위를 변경하면 관리되는 도메인에서 모든 데이터를 다시 동기화합니다. 관리되는 도메인에서 더 이상 필요하지 않은 개체가 삭제되며 다시 동기화를 완료하는 데 다소 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

동기화 프로세스에 대한 자세한 내용은 [Azure AD Domain Services 동기화 이해][concepts-sync]를 참조하세요.

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
