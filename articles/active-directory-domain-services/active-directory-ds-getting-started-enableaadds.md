---
title: "Azure Active Directory Domain Services: Azure Active Directory Domain Services 활성화 | Microsoft Docs"
description: "Azure 클래식 포털을 사용하여 Azure Active Directory Domain Services 활성화"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: c659da59-f4b5-4edd-b702-1727a8ccb36f
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/28/2017
ms.author: maheshu
ms.translationtype: Human Translation
ms.sourcegitcommit: 1500c02fa1e6876b47e3896c40c7f3356f8f1eed
ms.openlocfilehash: ed72325ca9db99405c6173eb882a92f80cd77f47
ms.contentlocale: ko-kr
ms.lasthandoff: 06/30/2017


---
# Azure 클래식 포털을 사용하여 Azure Active Directory Domain Services 활성화
<a id="enable-azure-active-directory-domain-services-using-the-azure-classic-portal" class="xliff"></a>

## 작업 3: Azure Active Directory Domain Services 활성화
<a id="task-3-enable-azure-active-directory-domain-services" class="xliff"></a>
이 작업에서는 다음 단계를 수행하여 디렉터리에 Azure AD DS(Azure Active Directory Domain Services)를 사용하도록 설정합니다.

1. [Azure 클래식 포털](https://manage.windowsazure.com)로 이동합니다.
2. 왼쪽 창에서 **Active Directory** 단추를 선택합니다.
3. Azure AD DS를 사용하도록 설정하려는 Azure AD(Azure Active Directory) 테넌트(디렉터리)를 선택합니다.

    ![Azure AD 디렉터리 선택](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. **미리 보기 디렉터리** 페이지에서 **구성** 탭을 클릭합니다.

    ![디렉터리의 탭 구성](./media/active-directory-domain-services-getting-started/configure-tab.png)
5. **도메인 서비스**에서 **이 디렉터리에 대해 도메인 서비스 사용** 옵션을 **예**로 변경합니다.  
    이 페이지에는 추가 Azure Active Directory Domain Services 구성 옵션도 표시됩니다.

    ![Domain Services 사용](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

   > [!NOTE]
   > 테넌트에 대해 Azure Active Directory Domain Services를 사용하도록 설정하면 Azure AD는 사용자를 인증하는 데 필요한 Kerberos 및 NTLM 자격 증명 해시를 생성하고 저장합니다.
   >
   >
6. **도메인 서비스의 DNS 도메인 이름**을 지정합니다.

   * 디렉터리의 기본 도메인 이름(**.onmicrosoft.com** 접미사가 있음)이 기본적으로 선택되어 있습니다.

   * 목록에는 **도메인** 탭에서 구성한 확인된 도메인과 확인되지 않은 도메인을 모두 포함하여 Azure AD 디렉터리에 대해 구성된 도메인이 모두 포함되어 있습니다.

   * 사용자 지정 도메인 이름도 입력할 수 있습니다. 이 예에서 사용자 지정 도메인 이름은 *contoso100.com*입니다.

     > [!WARNING]
     > 지정한 도메인 이름의 접두사(예: *contoso100.com* 도메인 이름의 *contoso100*)는 15자 이내의 문자를 포함해야 합니다. 15자를 초과하는 접두사가 포함된 Azure Active Directory Domain Services 도메인은 만들 수 없습니다.
     >
     >
7. 관리되는 도메인에서 선택한 DNS 도메인 이름은 가상 네트워크에 존재하지 않도록 합니다. 특히 다음과 같은 경우인지 확인합니다.

   * 이미 가상 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 있는 경우

   * 선택한 가상 네트워크에 온-프레미스 네트워크와 연결된 VPN이 있고, 온-프레미스 네트워크에 동일한 DNS 도메인 이름을 가진 도메인이 있는 경우

   * 가상 네트워크에 해당 이름을 가진 기존 클라우드 서비스가 있는 경우
8. Azure Active Directory Domain Services를 사용할 가상 네트워크를 선택합니다. **가상 네트워크에 도메인 서비스 연결**이라는 드롭다운 목록에서 만든 가상 네트워크와 전용 서브넷을 선택합니다. 또한 다음 사항도 고려합니다.

   * 지정한 가상 네트워크가 Azure Active Directory Domain Services에서 지원하는 Azure 지역에 속해 있는지 확인합니다. Azure Active Directory Domain Services를 사용할 수 있는 Azure 지역을 확인하려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.

   * Azure Active Directory Domain Services를 지원하지 않는 지역에 속한 가상 네트워크는 드롭다운 목록에 표시되지 않습니다.

   * Azure Active Directory Domain Services에 대한 가상 네트워크 내에서 전용 서브넷을 사용합니다. 게이트웨이 서브넷은 선택하지 *마세요*. [네트워킹 고려 사항](active-directory-ds-networking.md)을 참조합니다.

   * 마찬가지로 Azure Resource Manager를 사용하여 만든 가상 네트워크는 드롭다운 목록에 표시되지 않습니다. Resource Manager 기반 가상 네트워크가 현재 Azure Active Directory Domain Services에서 지원되지 않기 때문입니다.
9. Azure Active Directory Domain Services를 활성화하려면 페이지 아래쪽의 작업 창에서 **저장**을 클릭합니다.
    * 디렉터리에 대해 Azure Active Directory Domain Services가 활성화되어 있는 동안에는 해당 페이지에서 *보류 중* 상태를 표시합니다.

        ![Domain Services 사용 창](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

        > [!NOTE]
        > Azure Active Directory Domain Services는 관리되는 도메인에 고가용성을 제공합니다. Azure Active Directory Domain Services를 활성화하면 가상 네트워크에서 도메인 서비스를 사용할 수 있는 IP 주소가 한 번에 하나씩 표시됩니다. 서비스에서 도메인에 대한 고가용성을 활성화하는 즉시 두 번째 IP 주소가 첫 번째 IP 주소 바로 뒤에 표시됩니다. 높은 가용성이 구성되고 도메인에 활성화되면 **구성** 탭의 **도메인 서비스** 섹션에서 두 개의 IP 주소를 확인해야 합니다.
        >
        >
    * 약 20-30분 후에 **구성** 페이지의 **IP 주소** 필드에는 가상 네트워크에서 도메인 서비스를 사용할 수 있는 첫 번째 IP 주소가 표시됩니다.

        ![첫 번째 프로비전된 IP를 보여 주는 Domain Services 창](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)
    * 도메인에 대해 고가용성이 작동하면 두 개의 IP 주소가 페이지에 표시됩니다. 관리되는 도메인은 이 두 IP 주소에서 선택한 가상 네트워크에서 사용할 수 있습니다.

10. 가상 네트워크에 대한 DNS 설정을 업데이트할 수 있도록 두 IP 주소를 기록해 둡니다. 이렇게 하면 가상 네트워크의 가상 컴퓨터가 도메인 가입과 같은 작업을 위해 도메인에 연결할 수 있습니다.

    ![프로비전된 IP를 보여 주는 Domain Services 창](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [!NOTE]
> Azure AD 테넌트의 크기(사용자 또는 그룹의 수)에 따라 관리되는 도메인에 동기화하는 데 시간이 다소 오래 걸립니다. 이 동기화 프로세스는 백그라운드에서 발생합니다. 수만 개의 개체가 있는 대형 테넌트의 경우, 모든 사용자, 그룹 멤버 자격 및 자격 증명을 동기화하는 데 1-2일이 걸릴 수도 있습니다.
>
>

## 다음 단계
<a id="next-step" class="xliff"></a>
[작업 4: Azure 가상 네트워크에 대한 DNS 설정 업데이트](active-directory-ds-getting-started-update-dns.md)

