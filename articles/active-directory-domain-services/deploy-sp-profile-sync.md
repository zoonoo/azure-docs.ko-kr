---
title: Azure AD DS를 통해 SharePoint 사용자 프로필 서비스 사용 설정 | Microsoft Docs
description: Azure Active Directory Domain Services 관리되는 도메인을 구성하여 SharePoint Server에 프로필 동기화를 지원하는 방법을 알아봅니다.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: 8fbde10ccf5a7f083f5fbddaadd6668d880a1fac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619830"
---
# <a name="configure-azure-active-directory-domain-services-to-support-user-profile-synchronization-for-sharepoint-server"></a>SharePoint Server에 대한 사용자 프로필 동기화를 지원하도록 Azure Active Directory Domain Services 구성

SharePoint Server에는 사용자 프로필을 동기화하는 서비스가 포함되어 있습니다. 이 기능을 사용하면 사용자 프로필을 중앙 위치에 저장하고 여러 SharePoint 사이트 및 팜에서 액세스할 수 있습니다. SharePoint Server 사용자 프로필 서비스를 구성하려면 Azure AD DS(Azure Active Directory Domain Services) 관리되는 도메인에서 적절한 권한을 부여해야 합니다. 자세한 내용은 [SharePoint Server의 사용자 프로필 동기화](/SharePoint/administration/user-profile-service-administration)를 참조하세요.

이 문서에서는 Azure AD DS를 구성하여 SharePoint Server 사용자 프로필 동기화 서비스를 허용하는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 자습서를 완료하여 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성][create-azure-ad-ds-instance]합니다.
* Azure AD DS 관리되는 도메인에 연결된 Windows Server 관리 VM입니다.
    * 필요한 경우 자습서를 완료하여 [관리 VM을 만듭니다][tutorial-create-management-vm].
* Azure AD 테넌트의 *Azure AD DC Administrators* 그룹에 속한 멤버인 사용자 계정
* 사용자 프로필 동기화 서비스에 대한 SharePoint 서비스 계정입니다.
    * 필요한 경우 [SharePoint Server의 관리 및 서비스 계정 플랜][sharepoint-service-account]을 참조하세요.

## <a name="service-accounts-overview"></a>서비스 계정 개요

관리되는 도메인에서 ‘AAD DC 서비스 계정’이라는 보안 그룹은 ‘사용자’ OU(조직 구성 단위)의 일부로 존재합니다.  이 보안 그룹의 구성원에게 다음 권한이 위임됩니다.

- 루트 DSE에 대한 **디렉터리 변경 내용 복제** 권한
- ‘구성’ 명명 컨텍스트(`cn=configuration` 컨테이너)에 대한 **디렉터리 변경 내용 복제** 권한

‘AAD DC 서비스 계정’ 보안 그룹은 기본 제공 그룹인 *Windows 2000 이전 버전 호환 액세스* 의 구성원이기도 합니다.

이 보안 그룹에 추가되면 올바르게 작동하는 데 필요한 권한이 SharePoint Server 사용자 프로필 동기화 서비스의 서비스 계정에 부여됩니다.

## <a name="enable-support-for-sharepoint-server-user-profile-sync"></a>SharePoint Server 사용자 프로필 동기화에 대한 지원 사용

SharePoint Server의 서비스 계정에는 디렉터리에 대한 변경 내용을 복제하고 SharePoint Server 사용자 프로필 동기화가 제대로 작동하도록 하기 위한 적절한 권한이 필요합니다. 해당 권한을 부여하려면 SharePoint 사용자 프로필 동기화에 사용되는 서비스 계정을 ‘AAD DC 서비스 계정’ 그룹에 추가합니다.

Azure AD DS 관리 VM에서 다음 단계를 완료합니다.

> [!NOTE]
> 관리되는 도메인의 그룹 멤버 자격을 편집하려면 *AAD DC Administrators* 그룹의 구성원인 사용자 계정에 로그인해야 합니다.

1. 시작 화면에서 **관리 도구** 를 선택합니다. [관리 VM을 만들기][tutorial-create-management-vm] 위해 자습서에 설치된 사용 가능한 관리 도구 목록이 표시됩니다.
1. 그룹 멤버 자격을 관리하려면 관리 도구 목록에서 **Active Directory 관리 센터** 를 선택합니다.
1. 왼쪽 창에서 관리되는 도메인(예: *aaddscontoso.com*)을 선택합니다. 다음과 같이 기존 OU 및 리소스 목록이 표시됩니다.
1. **사용자** OU를 선택한 다음 ‘AAD DC 서비스 계정’ 보안 그룹을 선택합니다.
1. **구성원** 을 선택한 다음 **추가...** 를 선택합니다.
1. SharePoint 서비스 계정의 이름을 입력한 다음 **확인** 을 선택합니다. 다음 예제에서 SharePoint 서비스 계정의 이름은 *spadmin* 입니다.

    ![AAD DC 서비스 계정 보안 그룹에 SharePoint 서비스 계정 추가](./media/deploy-sp-profile-sync/add-member-to-aad-dc-service-accounts-group.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 [SharePoint Server에서 사용자 프로필 동기화 관리](/SharePoint/administration/manage-profile-synchronization)를 참조하세요.

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md

<!-- EXTERNAL LINKS -->
[sharepoint-service-account]: /sharepoint/security-for-sharepoint-server/plan-for-administrative-and-service-accounts