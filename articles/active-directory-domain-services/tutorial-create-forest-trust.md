---
title: 자습서 - Azure AD 도메인 서비스에서 포리스트 트러스트 만들기 | 마이크로 소프트 문서
description: Azure AD 도메인 서비스에 대한 Azure 포털에서 온-프레미스 AD DS 도메인에 편도 아웃바운드 포리스트를 만드는 방법에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: iainfou
ms.openlocfilehash: 5620d1cdc7dc71bdac17057b9a13a74150b12d5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77612523"
---
# <a name="tutorial-create-an-outbound-forest-trust-to-an-on-premises-domain-in-azure-active-directory-domain-services-preview"></a>자습서: Azure Active Directory 도메인 서비스에서 온-프레미스 도메인에 아웃바운드 포리스트 트러스트 만들기(미리 보기)

암호 해시를 동기화할 수 없거나 스마트 카드를 사용하여 단독으로 로그인하여 암호를 모르는 사용자가 있는 환경에서는 Azure Active Directory 도메인 서비스(AD DS)에서 리소스 포리스트를 사용할 수 있습니다. 리소스 포리스트는 Azure AD DS에서 하나 이상의 온-프레미스 AD DS 환경에 대한 단방향 아웃바운드 트러스트를 사용합니다. 이 트러스트 관계를 통해 사용자, 응용 프로그램 및 컴퓨터는 Azure AD DS 관리 도메인의 온-프레미스 도메인에 대해 인증할 수 있습니다. Azure AD DS 리소스 포리스트는 현재 미리 보기 상태입니다.

![Azure AD DS에서 온-프레미스 AD DS에 대한 포리스트 트러스트 다이어그램](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD DS 연결을 지원하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * Azure AD DS에서 단방향 아웃바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 신뢰 관계를 테스트하고 유효성 을 검사합니다.

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure Active Directory 도메인 서비스 관리 도메인 리소스 포리스트를 사용 하 여 만들어지고 Azure AD 테넌트에서 구성 되었습니다.
    * 필요한 경우 [Azure Active Directory Domain Services 인스턴스를 만들고 구성합니다][create-azure-ad-ds-instance-advanced].
    
    > [!IMPORTANT]
    > *리소스* 포리스트를 사용하여 Azure AD DS 관리 도메인을 만들어야 합니다. 기본 옵션은 *사용자* 포리스트를 만듭니다. 리소스 포리스트만 온프레미 AD DS 환경에 대한 트러스트를 만들 수 있습니다. 또한 관리되는 도메인에 최소 *엔터프라이즈* SKU를 사용해야 합니다. 필요한 경우 [Azure AD DS 관리 도메인의 SKU를 변경합니다.][howto-change-sku]

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure 포털을 사용하여 Azure AD DS에서 아웃바운드 포리스트 트러스트를 만들고 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="networking-considerations"></a>네트워킹 고려 사항

Azure AD DS 리소스 포리스트를 호스팅하는 가상 네트워크는 온-프레미스 Active Directory에 대한 네트워크 연결이 필요합니다. 또한 응용 프로그램 및 서비스에는 Azure AD DS 리소스 포리스트를 호스팅하는 가상 네트워크에 대한 네트워크 연결이 필요합니다. Azure AD DS 리소스 포리스트에 대한 네트워크 연결은 항상 켜져 있어야 하며 안정적이어야 하며 그렇지 않으면 사용자가 리소스를 인증하거나 액세스하지 못할 수 있습니다.

Azure AD DS에서 포리스트 트러스트를 구성하기 전에 Azure 와 온-프레미스 환경 간의 네트워킹이 다음 요구 사항을 충족하는지 확인합니다.

* 개인 IP 주소를 사용합니다. 동적 IP 주소 할당을 사용하여 DHCP에 의존하지 마십시오.
* 가상 네트워크 피어링 및 라우팅이 Azure와 온-프레미스 간에 성공적으로 통신할 수 있도록 IP 주소 공간이 겹치지 않도록 합니다.
* Azure 가상 네트워크에는 사이트 간(S2S) VPN 또는 ExpressRoute 연결을 구성하기 위해 게이트웨이 서브넷이 필요합니다.
* 시나리오를 지원하기에 충분한 IP 주소가 있는 서브넷을 만듭니다.
* Azure AD DS에 자체 서브넷이 있는지 확인하고 이 가상 네트워크 서브넷을 응용 프로그램 VM 및 서비스와 공유하지 마십시오.
* 피어있는 가상 네트워크는 전이되지 않습니다.
    * Azure 가상 네트워크 피어링은 온-프레미스 AD DS 환경에 Azure AD DS 리소스 포리스트 트러스트를 사용하려는 모든 가상 네트워크 간에 만들어야 합니다.
* 온-프레미스 Active Directory 포리스트에 지속적인 네트워크 연결을 제공합니다. 온디맨드 연결을 사용하지 마십시오.
* Azure AD DS 리소스 포리스트 이름과 온-프레미스 Active Directory 포리스트 이름 사이에 연속적인 이름 확인(DNS)이 있는지 확인합니다.

## <a name="configure-dns-in-the-on-premises-domain"></a>온-프레미스 도메인에서 DNS 구성

온-프레미스 환경에서 Azure AD DS 관리 도메인을 올바르게 해결하려면 기존 DNS 서버에 전달자를 추가해야 할 수 있습니다. Azure AD DS 관리 도메인과 통신하도록 온-프레미스 환경을 구성하지 않은 경우 온-프레미스 AD DS 도메인에 대한 관리 워크스테이션에서 다음 단계를 완료합니다.

1. 시작 선택 **| 관리 도구 | DNS**
1. *myAD01과*같은 오른쪽 선택 DNS 서버, **속성** 선택
1. **전달자를**선택한 다음 **편집하여** 추가 전달자를 추가합니다.
1. Azure AD DS 관리 도메인의 IP 주소(예: *10.0.1.4* 및 *10.0.1.5)를*추가합니다.

## <a name="create-inbound-forest-trust-in-the-on-premises-domain"></a>온-프레미스 도메인에서 인바운드 포리스트 트러스트 만들기

온-프레미스 AD DS 도메인에는 Azure AD DS 관리 도메인에 대한 들어오는 포리스트 트러스트가 필요합니다. 이 트러스트는 온-프레미스 AD DS 도메인에서 수동으로 만들어야 하며 Azure 포털에서 만들 수 없습니다.

온-프레미스 AD DS 도메인에서 인바운드 트러스트를 구성하려면 온-프레미스 AD DS 도메인에 대한 관리 워크스테이션의 다음 단계를 완료합니다.

1. 시작 선택 **| 관리 도구 | 활성 디렉터리 도메인 및 트러스트**
1. *onprem.contoso.com*등 오른쪽 선택 도메인, **속성** 선택
1. 트러스트 탭을 선택한 다음 **새 트러스트를** **선택합니다.**

   > [!NOTE]
   > **트러스트** 메뉴 옵션이 표시되지 않으면 **속성** 에서 *포리스트 형식의*을 확인합니다. *리소스* 포리스트만 트러스트를 만들 수 있습니다. 포리스트 형식이 *사용자인*경우 트러스트를 만들 수 없습니다. 현재 Azure AD DS 관리 도메인의 포리스트 유형을 변경할 수 있는 방법은 없습니다. 관리되는 도메인을 삭제하고 리소스 포리스트로 다시 만들어야 합니다.

1. Azure AD DS 도메인 이름에 이름을 입력(예: *aaddscontoso.com)* 다음을 **선택합니다.**
1. **포리스트 트러스트를**만드는 옵션을 선택한 다음 **받는 트러스트를** 만드는 한 가지 방법을 만듭니다.
1. **이 도메인에 대해서만**트러스트를 만들도록 선택합니다. 다음 단계에서는 Azure AD DS 관리 도메인에 대한 Azure 포털에서 트러스트를 만듭니다.
1. **포리스트 전체 인증을**사용하도록 선택한 다음 신뢰 암호를 입력하고 확인합니다. 이 암호는 다음 섹션의 Azure 포털에도 입력됩니다.
1. 기본 옵션이있는 다음 몇 가지 창을 단계별로 선택한 다음 아니오에 대한 옵션을 선택하고 **나가는 신뢰를 확인하지 않습니다.**
1. **마침**을 선택합니다.

## <a name="create-outbound-forest-trust-in-azure-ad-ds"></a>Azure AD DS에서 아웃바운드 포리스트 트러스트 만들기

Azure AD DS 관리 도메인과 인바운드 포리스트 트러스트를 해결하도록 구성된 온-프레미스 AD DS 도메인을 사용하여 이제 아웃바운드 포리스트 트러스트를 만들었습니다. 이 아웃바운드 포리스트 트러스트는 온-프레미스 AD DS 도메인과 Azure AD DS 관리 도메인 간의 트러스트 관계를 완료합니다.

Azure 포털에서 Azure AD DS 관리 도메인에 대한 아웃바운드 트러스트를 만들려면 다음 단계를 완료합니다.

1. Azure 포털에서 **Azure AD 도메인 서비스를**검색하고 선택한 다음 *관리되는* 도메인(예: aaddscontoso.com
1. Azure AD DS 관리 도메인의 왼쪽에 있는 메뉴에서 트러스트를 선택한 다음 + 트러스트 **추가를** **선택합니다.**
1. 신뢰를 식별하는 표시 이름을 입력한 다음 온-프레미스에서 신뢰할 수 있는 포리스트 DNS 이름(예: *onprem.contoso.com*
1. 이전 섹션에서 온-프레미스 AD DS 도메인에 대 한 인바운드 포리스트 트러스트를 구성할 때 사용 된 동일한 신뢰 암호를 제공 합니다.
1. 온-프레미스 AD DS 도메인에 대해 *10.0.2.4 및 10.0.2.5와* 같은 두 개 이상의 DNS 서버를 제공합니다. *10.0.2.5*
1. 준비가 되면 아웃바운드 포리스트 트러스트 **저장**

    [Azure 포털에서 아웃바운드 포리스트 트러스트 만들기](./media/create-forest-trust/portal-create-outbound-trust.png)

## <a name="validate-resource-authentication"></a>리소스 인증 유효성 검사

다음 일반적인 시나리오를 통해 포리스트 트러스트가 사용자를 올바르게 인증하고 리소스에 액세스할 수 있는지 확인할 수 있습니다.

* [Azure AD DS 리소스 포리스트의 온-프레미스 사용자 인증](#on-premises-user-authentication-from-the-azure-ad-ds-resource-forest)
* [온-프레미스 사용자를 사용하여 Azure AD DS 리소스 포리스트의 리소스에 액세스](#access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user)
    * [파일 및 프린터 공유 사용](#enable-file-and-printer-sharing)
    * [보안 그룹을 만들고 구성원을 추가합니다.](#create-a-security-group-and-add-members)
    * [포리스트 간 액세스를 위한 파일 공유 만들기](#create-a-file-share-for-cross-forest-access)
    * [리소스에 대한 포리스트 간 인증 유효성 검사](#validate-cross-forest-authentication-to-a-resource)

### <a name="on-premises-user-authentication-from-the-azure-ad-ds-resource-forest"></a>Azure AD DS 리소스 포리스트의 온-프레미스 사용자 인증

Windows Server 가상 컴퓨터가 Azure AD DS 리소스 도메인에 조인되어 있어야 합니다. 이 가상 컴퓨터를 사용하여 온-프레미스 사용자가 가상 컴퓨터에서 인증할 수 있는 테스트를 합니다.

1. 원격 데스크톱 및 Azure AD DS 관리자 자격 증명을 사용하여 Azure AD DS 리소스 포리스트에 가입한 Windows 서버 VM에 연결합니다. NLA(네트워크 수준 인증) 오류가 발생하면 사용한 사용자 계정이 도메인 사용자 계정이 아닌지 확인합니다.

    > [!NOTE]
    > Azure AD 도메인 서비스에 가입한 VM에 안전하게 연결하려면 지원되는 Azure 지역에서 [Azure 요새 호스트 서비스를](https://docs.microsoft.com/azure/bastion/bastion-overview) 사용할 수 있습니다.

1. 명령 프롬프트를 `whoami` 열고 명령을 사용하여 현재 인증된 사용자의 고유 이름을 표시합니다.

    ```console
    whoami /fqdn
    ```

1. `runas` 명령을 사용하여 온-프레미스 도메인에서 사용자로 인증합니다. 다음 명령에서 신뢰할 `userUpn@trusteddomain.com` 수 있는 온-프레미스 도메인에서 사용자의 UPN으로 바꿉습니다. 이 명령은 사용자의 암호를 묻는 메시지를 표시합니다.

    ```console
    Runas /u:userUpn@trusteddomain.com cmd.exe
    ```

1. 인증이 성공하면 새 명령 프롬프트가 열립니다. 새 명령 프롬프트의 `running as userUpn@trusteddomain.com`제목에는 을 포함합니다.
1. 새 `whoami /fqdn` 명령 프롬프트에서 온-프레미스 Active Directory에서 인증된 사용자의 고유 이름을 볼 수 있습니다.

### <a name="access-resources-in-the-azure-ad-ds-resource-forest-using-on-premises-user"></a>온-프레미스 사용자를 사용하여 Azure AD DS 리소스 포리스트의 리소스에 액세스

Azure AD DS 리소스 포리스트에 가입된 Windows Server VM을 사용하여 온-프레미스 도메인의 사용자와 함께 온-프레미스 도메인의 컴퓨터에서 인증할 때 리소스 포리스트에서 호스트된 리소스에 액세스할 수 있는 시나리오를 테스트할 수 있습니다. 다음 예제에서는 다양한 일반적인 시나리오를 만들고 테스트하는 방법을 보여 주며,

#### <a name="enable-file-and-printer-sharing"></a>파일 및 프린터 공유 사용

1. 원격 데스크톱 및 Azure AD DS 관리자 자격 증명을 사용하여 Azure AD DS 리소스 포리스트에 가입한 Windows 서버 VM에 연결합니다. NLA(네트워크 수준 인증) 오류가 발생하면 사용한 사용자 계정이 도메인 사용자 계정이 아닌지 확인합니다.

    > [!NOTE]
    > Azure AD 도메인 서비스에 가입한 VM에 안전하게 연결하려면 지원되는 Azure 지역에서 [Azure 요새 호스트 서비스를](https://docs.microsoft.com/azure/bastion/bastion-overview) 사용할 수 있습니다.

1. **Windows 설정을**열고 네트워크 및 공유 **센터를 검색하고**선택합니다.
1. 고급 공유 설정 변경 옵션을 **선택합니다.**
1. 도메인 **프로필에서** **파일 및 프린터 공유 켜기** 를 선택한 다음 변경 내용 **저장을**선택합니다.
1. **네트워크 및 공유 센터를**닫습니다.

#### <a name="create-a-security-group-and-add-members"></a>보안 그룹을 만들고 구성원을 추가합니다.

1. **Active Directory 사용자 및 컴퓨터**를 엽니다.
1. 도메인 이름을 오른쪽 에서 선택하고 **새로**를 선택한 다음 **조직 단위를**선택합니다.
1. 이름 상자에서 *LocalObjects를*입력한 다음 **확인을**선택합니다.
1. 탐색 창에서 **로컬 개체를** 선택하고 마우스 오른쪽 단추로 클릭합니다. **새로 고신** 다음 **그룹화를**선택합니다.
1. **그룹 이름** 상자에 *FileServerAccess를* 입력합니다. 그룹 **범위의**경우 **도메인 로컬을**선택한 다음 **확인을**선택합니다.
1. 콘텐츠 창에서 **FileServerAccess**를 두 번 클릭합니다. **구성원을** **선택하고, 추가를**선택한 다음 **위치를**선택합니다.
1. **위치** 보기에서 온-프레미스 활성 디렉터리를 선택한 다음 **확인을**선택합니다.
1. 입력 *도메인 사용자를* 입력 개체 이름을 클릭하여 상자를 **선택합니다.** **이름 확인을**선택하고 온-프레미스 활성 디렉터리에 대한 자격 증명을 제공한 다음 **확인을**선택합니다.

    > [!NOTE]
    > 트러스트 관계는 한 가지 방법일 뿐이므로 자격 증명을 제공해야 합니다. 즉, Azure AD DS의 사용자는 리소스에 액세스하거나 신뢰할 수 있는 온-프레미스(온-프레미스) 도메인의 사용자 또는 그룹을 검색할 수 없습니다.

1. 온-프레미스 활성 디렉터리에서 **도메인 사용자** 그룹은 **FileServerAccess** 그룹의 구성원이어야 합니다. **확인을** 선택하여 그룹을 저장하고 창을 닫습니다.

#### <a name="create-a-file-share-for-cross-forest-access"></a>포리스트 간 액세스를 위한 파일 공유 만들기

1. Windows 서버 VM에서 Azure AD DS 리소스 포리스트에 가입하여 폴더를 만들고 *CrossForestShare*와 같은 이름을 제공합니다.
1. 폴더를 오른쪽으로 선택하고 **속성을**선택합니다.
1. **보안** 탭을 선택한 다음 **편집을**선택합니다.
1. *크로스포레스트쉐어* 대화 상자에 대한 사용 권한에서 **추가를**선택합니다.
1. *FileServerAccess* 입력 개체 **이름을 입력하여 선택한**다음 **확인을**선택합니다.
1. 그룹 또는 사용자 **이름** 목록에서 *FileServerAccess를* 선택합니다. **FileServerAccess 에 대한 사용 권한** 목록에서 **수정** 및 **쓰기** 권한 *허용을* 선택한 다음 **확인을**선택합니다.
1. **공유** 탭을 선택한 다음 **고급 공유를 선택합니다...**
1. **이 폴더 공유를**선택한 다음 *CrossForestShare*와 같은 **공유 이름에** 파일 공유에 대한 기억하기 좋은 이름을 입력합니다.
1. 사용 권한 을 **선택합니다.** 모든 **사용 권한** 목록에서 **변경** 권한 **허용을** 선택합니다.
1. **확인을** 두 번 선택한 다음 **닫습니다.**

#### <a name="validate-cross-forest-authentication-to-a-resource"></a>리소스에 대한 포리스트 간 인증 유효성 검사

1. 온-프레미스 Active Directory의 사용자 계정을 사용하여 온-프레미스 Active Directory에 가입한 Windows 컴퓨터에 로그인합니다.
1. **Windows 탐색기를**사용하여 완전 하게 정규화된 호스트 이름과 와 `\\fs1.aaddscontoso.com\CrossforestShare`같은 공유를 사용하여 만든 공유에 연결합니다.
1. 쓰기 권한의 유효성을 검사하려면 폴더에서 오른쪽 을 선택하고 **새로 만들기를**선택한 다음 **텍스트 문서를**선택합니다. 기본 이름 **새 텍스트 문서**사용 .

    쓰기 권한이 올바르게 설정되면 새 텍스트 문서가 만들어집니다. 그런 다음 다음 단계를 통해 파일을 적절하게 열고 편집하고 삭제합니다.
1. 읽기 권한의 유효성을 검사하려면 **새 텍스트 문서를**엽니다.
1. 수정 권한의 유효성을 검사하려면 파일에 텍스트를 추가하고 **메모장을**닫습니다. 변경 내용을 저장하라는 메시지가 표시되면 **저장을**선택합니다.
1. 삭제 권한의 유효성을 검사하려면 **새 텍스트 문서를** 오른쪽으로 선택하고 **삭제를**선택합니다. 파일 삭제를 확인하려면 **예를** 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD DS 연결을 지원하도록 온-프레미스 AD DS 환경에서 DNS 구성
> * 온-프레미스 AD DS 환경에서 단방향 인바운드 포리스트 트러스트 만들기
> * Azure AD DS에서 단방향 아웃바운드 포리스트 트러스트 만들기
> * 인증 및 리소스 액세스에 대한 신뢰 관계를 테스트하고 유효성 을 검사합니다.

Azure AD DS의 포리스트 형식에 대한 자세한 개념 정보는 [리소스 포리스트란 무엇이며][concepts-forest] [Azure AD DS에서 포리스트 트러스트는 어떻게 작동합니까?][concepts-trust]

<!-- INTERNAL LINKS -->
[concepts-forest]: concepts-resource-forest.md
[concepts-trust]: concepts-forest-trust.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance-advanced]: tutorial-create-instance-advanced.md
[howto-change-sku]: change-sku.md
