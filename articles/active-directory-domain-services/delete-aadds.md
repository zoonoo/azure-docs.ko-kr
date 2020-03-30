---
title: Azure Active Directory 도메인 서비스 삭제 | 마이크로 소프트 문서
description: Azure 포털을 사용하여 Azure Active Directory 도메인 서비스 관리 도메인을 비활성화하거나 삭제하는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: e1836f91b8afc1bb4f5b7e141949f3724c57c857
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614043"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Azure 포털을 사용하여 관리되는 Azure Active Directory 도메인 서비스 삭제

더 이상 관리되는 도메인이 필요하지 않은 경우 Azure Active Directory 도메인 서비스(Azure AD DS) 인스턴스를 삭제할 수 있습니다. Azure AD DS 관리 도메인을 해제하거나 일시적으로 비활성화할 수 있는 옵션은 없습니다. Azure AD DS 관리 도메인을 삭제해도 Azure AD 테넌트가 삭제되거나 부정적인 영향을 미치지 않습니다. 이 문서에서는 Azure 포털을 사용하여 Azure AD DS 관리 도메인을 삭제하는 방법을 보여 주며 있습니다.

> [!WARNING]
> **삭제는 영구적이며 되돌릴 수 없습니다.**
> Azure AD DS 관리 도메인을 삭제하면 다음 단계가 수행됩니다.
>   * 관리되는 도메인의 도메인 컨트롤러가 가상 네트워크에서 프로비전 해제되고 제거됩니다.
>   * 관리되는 도메인의 데이터가 영구적으로 삭제됩니다. 이 데이터에는 사용자 지정 OUs, GPO, 사용자 지정 DNS 레코드, 서비스 주체, GMSA 등이 포함됩니다.
>   * 관리되는 도메인에 가입된 컴퓨터는 도메인과의 트러스트 관계를 해제하고 도메인 가입을 해제해야 합니다.
>       * 회사 AD 자격 증명을 사용하여 이러한 컴퓨터에 로그인할 수 없습니다. 대신 컴퓨터에 대한 로컬 관리자 자격 증명을 사용해야 합니다.

## <a name="delete-the-managed-domain"></a>관리되는 도메인 삭제

Azure AD DS 관리 도메인을 삭제하려면 다음 단계를 완료합니다.

1. Azure 포털에서 **Azure AD 도메인 서비스를**검색하고 선택합니다.
1. *aaddscontoso.com*와 같은 Azure AD DS 관리 도메인의 이름을 선택합니다.
1. **개요** 페이지에서 **삭제**를 선택합니다. 삭제를 확인하려면 관리되는 도메인의 도메인 이름을 다시 입력한 다음 **삭제를**선택합니다.

Azure AD DS 관리 도메인을 삭제하는 데 15~20분 이상 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS에서 보려는 기능에 대한 [피드백을 공유하는][feedback] 것이 좋습니다.

Azure AD DS를 다시 시작하려면 [Azure Active Directory 도메인 서비스 인스턴스 만들기 및 구성을][create-instance]참조하십시오.

<!-- INTERNAL LINKS -->
[feedback]: contact-us.md
[create-instance]: tutorial-create-instance.md
