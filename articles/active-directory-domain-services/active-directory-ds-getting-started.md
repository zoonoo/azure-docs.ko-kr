---
title: "Azure Active Directory Domain Services: 시작 | Microsoft Docs"
description: "Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화"
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
ms.date: 09/26/2017
ms.author: maheshu
ms.openlocfilehash: d86b9e99619dea408e784f32d76ba6296948bdc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Active Directory Domain Services 활성화
이 문서에서는 Azure Portal을 사용하여 Azure AD DS(Azure Active Directory Domain Services)를 사용하도록 설정하는 방법을 설명합니다.

**Azure AD Domain Services** 마법사를 시작하려면 다음 단계를 완료합니다.

1. [Azure 포털](https://portal.azure.com)로 이동합니다.
2. 왼쪽 창에서 **새로 만들기**를 클릭합니다.
3. **새로 만들기** 페이지의 검색 창에 **Domain Services**를 입력합니다.

    ![Domain Services 검색](./media/getting-started/search-domain-services.png)

4. 검색 제안 목록에서 **Azure AD Domain Services**를 클릭하여 선택합니다. **Azure AD Domain Services** 페이지에서 **만들기** 단추를 클릭합니다.

    ![Domain Services 보기](./media/getting-started/domain-services-blade.png)

5. **Azure AD Domain Services 사용** 마법사가 시작됩니다.


## <a name="task-1-configure-basic-settings"></a>작업 1: 기본 설정 구성
마법사의 **기본 사항** 페이지에서 관리되는 도메인에 대한 DNS 도메인 이름을 지정할 수 있습니다. 관리되는 도메인이 배포되어야 하는 Azure 위치 및 리소스 그룹을 선택할 수도 있습니다.

![기본 사항 구성](./media/getting-started/domain-services-blade-basics.png)

1. 관리되는 도메인에 대한 **DNS 도메인 이름**을 선택합니다.

   * 디렉터리의 기본 도메인 이름(**.onmicrosoft.com** 접미사가 있음)이 기본적으로 지정되어 있습니다.

   * 사용자 지정 도메인 이름도 입력할 수 있습니다. 이 예에서 사용자 지정 도메인 이름은 *contoso100.com*입니다.

     > [!WARNING]
     > 지정한 도메인 이름의 접두사(예: *contoso100.com* 도메인 이름의 *contoso100*)는 15자 이내의 문자를 포함해야 합니다. 15자보다 긴 접두사로 관리되는 도메인을 만들 수 없습니다.
     >
     >

2. 관리되는 도메인에서 선택한 DNS 도메인 이름은 가상 네트워크에 존재하지 않도록 합니다. 특히,

   * 이미 가상 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 있는 경우

   * 관리되는 도메인을 사용하도록 설정할 가상 네트워크에 온-프레미스 네트워크와의 VPN 연결이 있는지 확인합니다. 이 시나리오에서는 온-프레미스 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 없는지 확인합니다.

   * 가상 네트워크에 해당 이름을 가진 기존 클라우드 서비스가 있는 경우

3. **가상 네트워크의 형식**을 선택합니다. 기본적으로는 **Resource Manager** 가상 네트워크 형식을 선택합니다. 새로 만든 관리되는 도메인 모두에 이 형식의 가상 네트워크를 사용하는 것이 좋습니다.

    > [!TIP]
    > **클래식 가상 네트워크 지원이 사용 중단될 예정입니다.** 모든 새로운 배포에서 Resource Manager 가상 네트워크 형식을 선택합니다. 클래식 가상 네트워크는 더 이상 새로운 배포에서 지원되지 않습니다. 클래식 가상 네트워크에 배포된 기존의 관리되는 도메인은 계속 지원됩니다.
    >

4. 관리되는 도메인을 만들려는 Azure **구독**을 선택합니다.

5. 관리되는 도메인이 속해야 하는 **리소스 그룹**을 선택합니다. **새로 만들기** 또는 **기존 항목 사용** 옵션을 선택하여 리소스 그룹을 선택할 수 있습니다.

6. 관리되는 도메인을 만들어야 하는 Azure **위치**를 선택합니다. 마법사의 **네트워크** 페이지에는 선택한 위치에 속하는 가상 네트워크만 표시됩니다.

7. 완료되면 **확인**을 클릭하여 마법사의 **네트워크** 페이지로 이동합니다.


## <a name="next-step"></a>다음 단계
[작업 2: 네트워크 설정 구성](active-directory-ds-getting-started-network.md)
