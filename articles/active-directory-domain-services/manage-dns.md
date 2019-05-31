---
title: Azure AD Domain Services에 대 한 DNS 관리 | Microsoft Docs
description: Azure AD Domain Services에 대한 DNS 관리
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 3c844b6ede63b2e036f05b5b7f6cb3803eb4b47c
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245881"
---
# <a name="administer-dns-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리되는 도메인에서 DNS 관리
Azure Active Directory Domain Services는 관리되는 도메인에 대한 DNS 확인을 제공하는 DNS(도메인 이름 확인) 서버를 포함합니다. 경우에 따라 관리되는 도메인에서 DNS를 구성해야 할 수 있습니다. 도메인에 가입되지 않은 컴퓨터에 대한 DNS 레코드를 만들고 부하 분산 장치에 대한 가상 IP 주소를 구성하거나 외부 DNS 전달자를 설정해야 할 수 있습니다. 이러한 이유로 'AAD DC 관리자' 그룹에 속한 사용자에게 관리되는 도메인에 대한 DNS 관리 권한이 부여됩니다.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 완료하려면 다음이 필요합니다.

1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](create-instance.md)에 간략히 설명된 모든 작업을 따릅니다.
4. Azure AD 도메인 서비스 관리되는 도메인을 관리하게 될 **도메인에 가입된 가상 머신** . 이러한 가상 머신이 없는 경우 [Windows 가상 머신을 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)문서에 설명된 모든 작업을 수행하세요.
5. 관리되는 도메인에 대한 DNS를 관리하려면 디렉터리의 **'AAD DC 관리자' 그룹에 속한 사용자 계정** 의 자격 증명이 필요합니다.

<br>

## <a name="task-1---create-a-domain-joined-virtual-machine-to-remotely-administer-dns-for-the-managed-domain"></a>작업 1 - 관리되는 도메인에 대한 DNS를 원격으로 관리하기 위해 도메인에 가입된 가상 머신 만들기
Azure AD 도메인 서비스 관리되는 도메인을 AD PowerShell 또는 ADAC(Active Directory 관리 센터)와 같은 친숙한 Active Directory 관리 도구를 사용하여 원격으로 관리할 수 있습니다. 마찬가지로, DNS 서버 관리 도구를 사용하여 관리되는 도메인에 대한 DNS를 원격으로 관리할 수 있습니다.

Azure AD 디렉터리의 테넌트 관리자는 원격 데스크톱을 통해 관리되는 도메인의 도메인 컨트롤러에 연결할 권한이 없습니다. 'AAD DC 관리자' 그룹의 멤버는 관리되는 도메인에 가입된 Windows Server/클라이언트 컴퓨터에서 DNS 서버 도구를 사용하여 관리되는 도메인에 대한 DNS를 원격으로 관리할 수 있습니다. DNS 서버 도구는 RSAT(원격 서버 관리 도구) 옵션 기능의 일부입니다.

Windows Server 가상 머신을 만들기 위한 첫 번째 작업은 관리되는 도메인에 가입하는 것입니다. 지침은 [Windows Server 가상 머신을 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)이라는 문서를 참조하세요.

## <a name="task-2---install-dns-server-tools-on-the-virtual-machine"></a>작업 2 - 가상 머신에 DNS 서버 도구 설치
도메인에 가입된 가상 머신에 DNS 관리 도구를 설치하려면 다음 단계를 완료합니다. [원격 서버 관리 도구 설치 및 사용](https://technet.microsoft.com/library/hh831501.aspx)에 대한 자세한 내용은 TechNet을 참조하세요.

1. Azure Portal로 이동합니다. 왼쪽 패널에서 **모든 리소스**를 클릭합니다. 작업 1에서 만든 가상 컴퓨터를 찾아 클릭합니다.
2. 개요 탭에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜(.rdp) 파일이 생성되고 다운로드됩니다.

    ![Windows 가상 머신에 연결](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 클릭합니다. 'AAD DC 관리자' 그룹에 속한 사용자의 자격 증명을 사용합니다. 예제: ‘bob@domainservicespreview.onmicrosoft.com’. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 예를 클릭하거나 연결을 계속합니다.

4. 시작 화면에서 **서버 관리자**를 엽니다. 서버 관리자 창의 가운데 창에서 **역할 및 기능 추가** 를 클릭합니다.

    ![가상 컴퓨터에서 서버 관리자 시작](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. **역할 및 기능 추가 마법사**의 **시작하기 전에** 페이지에서 **다음**을 클릭합니다.

    ![시작하기 전에 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. **설치 유형** 페이지에서 **역할 기반 또는 기능 기반 설치** 옵션을 선택한 상태로 두고 **다음**을 클릭합니다.

    ![설치 유형 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. **서버 선택** 페이지에서 서버 풀의 현재 가상 머신을 선택하고 **다음**을 클릭합니다.

    ![서버 선택 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. **서버 역할** 페이지에서 **다음**을 클릭합니다.
9. **기능** 페이지에서 **원격 서버 관리 도구** 노드를 클릭하여 확장한 후 **역할 관리 도구** 노드를 클릭하여 확장합니다. 역할 관리 도구 목록에서 **DNS 서버 도구** 기능을 선택합니다.

    ![기능 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-tools.png)
10. **확인** 페이지에서 가상 컴퓨터에 DNS 서버 도구를 설치하기 위해 **설치**를 클릭합니다. 기능 설치를 성공적으로 완료하면 **닫기**를 클릭하여 **역할 및 기능 추가** 마법사를 종료합니다.

    ![확인 페이지](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-dns-confirmation.png)

## <a name="task-3---launch-the-dns-management-console-to-administer-dns"></a>작업 3 - DNS 관리 콘솔을 실행하여 DNS 관리
이제 관리되는 도메인에서 DNS을 관리하려면 Windows Server DNS 도구를 사용할 수 있습니다.

> [!NOTE]
> 관리되는 도메인의 DNS를 관리하려면 'AAD DC 관리자' 그룹의 구성원이어야 합니다.
>
>

1. 시작 화면에서 **관리 도구**를 클릭합니다. 가상 컴퓨터에 설치된 **DNS** 콘솔이 표시됩니다.

    ![관리 도구 - DNS 콘솔](./media/active-directory-domain-services-admin-guide/install-rsat-dns-tools-installed.png)
2. **DNS** 를 클릭하여 DNS 관리 콘솔을 시작합니다.
3. **DNS 서버에 연결** 대화 상자에서 **다음 컴퓨터**를 클릭하고 관리되는 도메인의 DNS 도메인 이름을 입력합니다(예: 'contoso100.com').

    ![DNS 콘솔 - 도메인에 연결](./media/active-directory-domain-services-admin-guide/dns-console-connect-to-domain.png)
4. DNS 콘솔이 관리되는 도메인에 연결됩니다.

    ![DNS 콘솔 - 도메인 관리](./media/active-directory-domain-services-admin-guide/dns-console-managed-domain.png)
5. 이제 AAD 도메인 서비스를 활성화한 가상 네트워크 내에 있는 컴퓨터에 대한 DNS 항목을 추가하는 데 DNS 콘솔을 사용할 수 있습니다.

> [!WARNING]
> DNS 관리 도구를 사용하여 관리되는 도메인에 대한 DNS를 관리할 때는 주의해야 합니다. **도메인의 도메인 서비스에서 사용하는 기본 제공 DNS 레코드를 삭제 또는 수정하지 않도록**해야 합니다. 기본 제공 DNS 레코드는 도메인 DNS 레코드, 이름 서버 레코드 및 DC 위치에 사용되는 기타 레코드를 포함합니다. 이러한 레코드를 수정하는 경우 가상 네트워크에서 도메인 서비스가 중단됩니다.
>
>

DNS 관리에 대한 자세한 내용은 [Technet의 DNS 도구 문서](https://technet.microsoft.com/library/cc753579.aspx)를 참조하세요.

## <a name="related-content"></a>관련 내용
* [Azure AD 도메인 서비스 - 시작 가이드](create-instance.md)
* [Windows Server 가상 머신을 Azure AD 도메인 서비스 관리되는 도메인에 가입](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD Domain Services 도메인을 관리 합니다.](manage-domain.md)
* [DNS 관리 도구](https://technet.microsoft.com/library/cc753579.aspx)
