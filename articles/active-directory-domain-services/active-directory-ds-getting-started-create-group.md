---
title: "Azure Active Directory Domain Services: Azure AD DC Administrators 그룹 만들기 | Microsoft Docs"
description: "Azure 클래식 포털을 사용하여 Azure Active Directory Domain Services 활성화"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: b8e5e2213f157c0332f98305266c1c6248c2ea87
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-classic-portal"></a>Azure 클래식 포털을 사용하여 Azure Active Directory Domain Services 활성화
이 문서에서는 Azure AD(Azure Active Directory) 테넌트에 대해 Azure AD DS(Azure Active Directory Domain Services)를 사용하도록 설정하는 데 필요한 구성 작업을 설명하고 안내합니다.

## <a name="task-1-create-the-azure-ad-dc-administrators-group"></a>작업 1: Azure AD DC Administrators 그룹 만들기
첫 번째 작업은 Azure AD 테넌트에 관리 그룹을 만드는 것입니다. 이 특수 관리 그룹을 *AAD DC Administrators*라고 합니다. 이 그룹의 구성원에게는 Azure Active Directory Domain Services 관리 도메인에 가입한 컴퓨터에 대한 관리 권한이 부여됩니다. 이 그룹은 도메인 가입 컴퓨터에서 Administrators 그룹에 추가됩니다. 또한 이 그룹의 구성원은 원격 데스크톱을 사용하여 도메인에 가입한 컴퓨터에 원격으로 연결할 수 있습니다.  

> [!NOTE]
> Azure Active Directory Domain Services를 사용하여 만든 관리되는 도메인에 도메인 관리자 또는 엔터프라이즈 관리자 권한이 없습니다. 관리되는 도메인에서 이러한 권한은 서비스별로 예약되며 테넌트 내의 사용자가 사용할 수 없습니다. 하지만, 권한 있는 작업을 수행하기 위해 이 구성 태스크를 통해 만든 특수 관리자 그룹을 사용할 수 있습니다. 이 작업에는 도메인에 가입한 컴퓨터에서 관리자 그룹에 속하는 도메인에 컴퓨터를 가입시키고, 그룹 정책을 구성하는 작업이 포함됩니다.
>

구성 태스크에서는 관리 그룹을 만들고 디렉터리에 있는 한 명 이상의 사용자를 그룹에 추가합니다. Azure Active Directory Domain Services에 대한 관리 그룹을 만들려면 다음 작업을 수행합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)로 이동합니다.
2. 왼쪽 창에서 **Active Directory** 단추를 선택합니다.
3. Azure Active Directory Domain Services를 사용하도록 설정하려는 Azure AD 테넌트(디렉터리)를 선택합니다. Azure AD 디렉터리에 한 개의 도메인만 만들 수 있습니다.

    ![Azure AD Directory 선택](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. **미리 보기 디렉터리** 페이지에서 **그룹** 탭을 클릭합니다.
5. Azure AD 테넌트에 그룹을 추가하려면 창 아래쪽의 태스크 창에서 **그룹 추가**를 클릭합니다.

    ![그룹 추가 단추](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. **그룹 추가** 대화 상자에서 **AAD DC 관리자**라는 그룹을 만든 후 **그룹 유형**을 **보안**으로 설정합니다.

   > [!WARNING]
   > Azure Active Directory Domain Services 관리되는 도메인 내에서 액세스할 수 있도록 설정하려면 정확히 이 이름으로 그룹을 만듭니다.
   >
   >

    ![그룹 추가 대화 상자](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. **설명** 상자에 다른 사용자가 이 그룹이 Azure Active Directory Domain Services 내에서 관리 권한을 부여한다는 점을 이해할 수 있도록 하는 설명을 입력합니다.
8. 그룹을 만든 후 그룹 이름을 클릭하면 해당 속성을 볼 수 있습니다.
9. 사용자를 그룹의 구성원으로 추가하려면 창 아래쪽에서 **멤버 추가** 단추를 클릭합니다.

    ![그룹 구성원 단추 추가](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
10. **멤버 추가** 대화 상자에서 이 그룹의 멤버여야 하는 사용자를 선택하고 오른쪽 아래에 있는 확인 표시 아이콘을 클릭합니다.

    ![Administrators 그룹에 사용자 추가](./media/active-directory-domain-services-getting-started/add-group-members.png)


## <a name="next-step"></a>다음 단계
[작업 2: Azure 가상 네트워크 만들기 또는 선택](active-directory-ds-getting-started-vnet.md)

