---
title: 자습서 - Azure AD Domain Services에 대한 가상 네트워킹 구성 | Microsoft Docs
description: 이 자습서에서는 Azure Portal을 사용하여 Azure Active Directory Domain Services 관리되는 도메인에 대한 Azure 가상 네트워크 서브넷 또는 네트워크 피어링을 만들고 구성하는 방법을 알아봅니다.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: e0d2b235f671ca9b30bf61aef254cb850b25373e
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024777"
---
# <a name="tutorial-configure-virtual-networking-for-an-azure-active-directory-domain-services-managed-domain"></a>자습서: Azure Active Directory Domain Services 관리되는 도메인에 대한 가상 네트워킹 구성

사용자 및 애플리케이션에 연결하기 위해 Azure AD DS(Active Directory Domain Services) 관리형 도메인이 Azure 가상 네트워크 서브넷에 배포됩니다. 이 가상 네트워크 서브넷은 Azure 플랫폼에서 제공하는 관리되는 도메인 리소스에만 사용해야 합니다.

자체 VM 및 애플리케이션을 만드는 경우 동일한 가상 네트워크 서브넷에 배포해서는 안 됩니다. 대신 애플리케이션을 만들어 별도의 가상 네트워크 서브넷 또는 Azure AD DS 가상 네트워크에 피어링되는 별도의 가상 네트워크에 배포해야 합니다.

이 자습서에서는 전용 가상 네트워크 서브넷을 만들고 구성하는 방법 또는 다른 네트워크를 Azure AD DS 관리형 도메인의 가상 네트워크에 피어링하는 방법을 보여 줍니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure AD DS에 대한 도메인 조인 리소스에 대한 가상 네트워크 연결 옵션 이해
> * Azure AD DS 가상 네트워크에서 IP 주소 범위 및 추가 서브넷 만들기
> * Azure AD DS와 별도인 네트워크에 가상 네트워크 피어링 구성

Azure 구독이 없는 경우 시작하기 전에 [계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화되어 구독과 연결된 Azure Active Directory 테넌트
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* Azure AD DS를 구성하려면 Azure AD 테넌트에 *전역 관리자* 권한이 필요합니다.
* 필요한 Azure AD DS 리소스를 만들려면 Azure 구독에 *기여자* 권한이 필요합니다.
* Azure AD 테넌트에서 사용하도록 설정되고 구성된 Azure Active Directory Domain Services 관리되는 도메인
    * 필요한 경우 첫 번째 자습서에서 [Azure Active Directory Domain Services 관리되는 도메인을 만들고 구성합니다][create-azure-ad-ds-instance].

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

이 자습서에서는 Azure Portal을 사용하여 관리되는 도메인을 만들고 구성합니다. 시작하려면 먼저 [Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="application-workload-connectivity-options"></a>애플리케이션 워크로드 연결 옵션

이전 자습서에서는 가상 네트워크에 대한 일부 기본 구성 옵션을 사용하는 관리되는 도메인을 만들었습니다. 이러한 기본 옵션은 Azure 가상 네트워크와 가상 네트워크 서브넷을 만들었습니다. 관리되는 도메인 서비스를 제공하는 Azure AD DS 도메인 컨트롤러는 이 가상 네트워크 서브넷에 연결됩니다.

관리되는 도메인을 사용해야 하는 VM을 만들고 실행할 때 네트워크 연결이 제공되어야 합니다. 이 네트워크 연결은 다음 방법 중 하나로 제공할 수 있습니다.

* 관리되는 도메인의 가상 네트워크에 가상 네트워크 서브넷을 추가로 만듭니다. 이 추가 서브넷은 VM을 만들고 연결합니다.
    * VM이 동일한 가상 네트워크에 속해 있으므로 자동으로 이름 확인을 수행하고 Azure AD DS 도메인 컨트롤러와 통신할 수 있습니다.
* 관리되는 도메인의 가상 네트워크에서 하나 이상의 개별 가상 네트워크로의 Azure 가상 네트워크 피어링을 구성합니다. 이러한 개별 가상 네트워크는 VM을 만들고 연결합니다.
    * 가상 네트워크 피어링을 구성하는 경우 이름 확인을 Azure AD DS 도메인 컨트롤러에 다시 사용하도록 DNS 설정도 구성해야 합니다.

일반적으로 이러한 네트워크 연결 옵션 중 하나만 사용합니다. 이 옵션 선택은 Azure 리소스를 분리하여 관리하는 방법에 따라 결정되는 경우가 많습니다.

* Azure AD DS 및 연결된 VM을 하나의 리소스 그룹으로 관리하려면 VM에 대한 추가 가상 네트워크 서브넷을 만들 수 있습니다.
* Azure AD DS와 연결된 VM을 분리하여 관리하려면 가상 네트워크 피어링을 사용할 수 있습니다.
    * 또한 가상 네트워크 피어링을 사용하여 기존 가상 네트워크에 연결된 Azure 환경의 기존 VM에 대한 연결을 제공하도록 선택할 수도 있습니다.

이 자습서에서는 이러한 가상 네트워크 연결 옵션 중 하나만 구성하면 됩니다.

가상 네트워크를 계획하고 구성하는 방법에 대한 자세한 내용은 [Azure Active Directory Domain Services에 대한 네트워킹 고려 사항][network-considerations]을 참조하세요.

## <a name="create-a-virtual-network-subnet"></a>가상 네트워크 서브넷 만들기

기본적으로 관리되는 도메인을 사용하여 만든 Azure 가상 네트워크에는 단일 가상 네트워크 서브넷이 있습니다. 이 가상 네트워크 서브넷은 Azure 플랫폼에서 관리되는 도메인 서비스를 제공하는 데만 사용해야 합니다. 이 Azure 가상 네트워크에서 사용자 고유의 VM을 만들고 사용하려면 추가 서브넷을 만듭니다.

VM 및 애플리케이션 워크로드에 대한 가상 네트워크 서브넷을 만들려면 다음 단계를 완료합니다.

1. Azure Portal에서 관리되는 도메인의 리소스 그룹(예: *myResourceGroup*)을 선택합니다. 리소스 목록에서 기본 가상 네트워크(예: *aadds-vnet*)를 선택합니다.
1. 가상 네트워크 창의 왼쪽 메뉴에서 **주소 공간**을 선택합니다. 가상 네트워크는 기본 서브넷에서 사용하는 *10.0.2.0/24*의 단일 주소 공간을 사용하여 만들어집니다.

    추가 IP 주소 범위를 가상 네트워크에 추가합니다. 이 주소 범위의 크기와 사용할 실제 IP 주소 범위는 이미 배포된 다른 네트워크 리소스에 따라 달라집니다. IP 주소 범위는 Azure 또는 온-프레미스 환경의 기존 주소 범위와 겹치지 않아야 합니다. 서브넷에 배포하는 데 필요한 VM의 수에 맞게 IP 주소 범위를 충분히 크게 조정해야 합니다.

    다음 예제에서는 *10.0.3.0/24*의 IP 주소 범위가 추가되었습니다. 준비되면 **저장**을 선택합니다.

    ![Azure Portal에서 추가 가상 네트워크 IP 주소 범위를 추가합니다.](./media/tutorial-configure-networking/add-vnet-address-range.png)

1. 다음으로, 가상 네트워크 창의 왼쪽 메뉴에서 **서브넷**을 선택한 다음, **+ 서브넷**을 선택하여 서브넷을 추가합니다.
1. 서브넷의 이름(예: *workloads*)을 입력합니다. 필요한 경우 가상 네트워크에 대해 이전 단계에서 구성한 IP 주소 범위의 하위 세트를 사용하려면 **주소 범위**를 업데이트합니다. 지금은 네트워크 보안 그룹, 경로 테이블, 서비스 엔드포인트와 같은 옵션의 기본값을 그대로 둡니다.

    다음 예제에서는 *10.0.3.0/24*의 IP 주소 범위를 사용하는 *워크로드*라는 서브넷을 만듭니다.

    ![Azure Portal에서 추상 가상 네트워크 서브넷을 추가합니다.](./media/tutorial-configure-networking/add-vnet-subnet.png)

1. 준비되면 **확인**을 선택합니다. 가상 네트워크 서브넷을 만드는 데 몇 분 정도 걸립니다.

관리되는 도메인을 사용해야 하는 VM을 만들 때 이 가상 네트워크 서브넷을 선택해야 합니다. 기본 *aadds-subnet*에는 VM을 만들지 않습니다. 다른 가상 네트워크를 선택하면 가상 네트워크 피어링을 구성하지 않는 한 관리되는 도메인에 연결하기 위한 네트워크 연결 및 DNS 확인이 없습니다.

## <a name="configure-virtual-network-peering"></a>가상 네트워크 피어링 구성

VM에 대한 기존 Azure 가상 네트워크가 있거나 관리되는 도메인 가상 네트워크를 별도로 유지하려고 할 수 있습니다. 관리되는 도메인을 사용하려면 다른 가상 네트워크의 VM에서 Azure AD DS 도메인 컨트롤러와 통신하는 방법이 필요합니다. 이 연결은 Azure 가상 네트워크 피어링을 사용하여 제공할 수 있습니다.

Azure 가상 네트워크 피어링을 사용하면 VPN(가상 사설망) 디바이스가 없어도 두 가상 네트워크가 서로 연결됩니다. 네트워크 피어링을 사용하면 가상 네트워크를 빠르게 연결하고 Azure 환경 전체의 트래픽 흐름을 정의할 수 있습니다.

피어링에 대한 자세한 내용은 [Azure 가상 네트워크 피어링 개요][peering-overview]를 참조하세요.

가상 네트워크를 관리되는 도메인 가상 네트워크에 피어링하려면 다음 단계를 완료합니다.

1. *aadds-vnet*이라는 관리되는 도메인에 대해 만든 기본 가상 네트워크를 선택합니다.
1. 가상 네트워크 창의 왼쪽 메뉴에서 **피어링**을 선택합니다.
1. 피어링을 만들려면 **+ 추가**를 선택합니다. 다음 예에서는 기본 *aadds-vnet*이 *myVnet*이라는 가상 네트워크에 피어링됩니다. 사용자 고유의 값을 사용하여 다음 설정을 구성합니다.

    * **aadds-vnet에서 원격 가상 네트워크로의 피어링 이름**: 두 네트워크에 대한 설명이 포함된 식별자(예: *aadds-vnet-to-myvnet*)
    * **가상 네트워크 배포 유형**: *리소스 관리자*
    * **구독**: 피어링하려는 가상 네트워크의 구독(예: *Azure*)
    * **가상 네트워크**: 피어링하려는 가상 네트워크(예: *myVnet*)
    * **myVnet에서 adds-vnet으로의 피어링 이름**: 두 네트워크에 대한 설명이 포함된 식별자(예: *myvnet-to-aadds-vnet*)

    ![Azure Portal에서 가상 네트워크 피어링 구성](./media/tutorial-configure-networking/create-peering.png)

    환경에 대한 특정 요구 사항이 없는 경우 가상 네트워크 액세스 또는 전달된 트래픽에 대한 다른 기본값을 그대로 둔 다음, **확인**을 선택합니다.

1. Azure AD DS 가상 네트워크와 선택한 가상 네트워크 모두에서 피어링을 만드는 데 몇 분 정도 걸립니다. 준비되면 다음 예와 같이 **피어링 상태**에서 *연결됨*을 보고합니다.

    ![Azure Portal에서 피어링된 네트워크가 성공적으로 연결됨](./media/tutorial-configure-networking/connected-peering.png)

피어링된 가상 네트워크의 VM에서 관리되는 도메인을 사용하려면 먼저 올바른 이름 확인을 허용하도록 DNS 서버를 구성합니다.

### <a name="configure-dns-servers-in-the-peered-virtual-network"></a>피어링된 가상 네트워크에서 DNS 서버 구성

피어링된 가상 네트워크의 VM 및 애플리케이션에서 관리되는 도메인과 성공적으로 통신하려면 DNS 설정을 업데이트해야 합니다. Azure AD DS 도메인 컨트롤러의 IP 주소는 피어링된 가상 네트워크에서 DNS 서버로 구성되어야 합니다. 도메인 컨트롤러를 피어링된 가상 네트워크의 DNS 서버로 구성하는 두 가지 방법이 있습니다.

* Azure AD DS 도메인 컨트롤러를 사용하도록 Azure 가상 네트워크 DNS 서버를 구성합니다.
* 조건부 DNS 전달을 사용하여 쿼리를 관리되는 도메인에 보내도록 피어링된 가상 네트워크에서 사용 중인 기존 DNS 서버를 구성합니다. 이러한 단계는 사용 중인 기존 DNS 서버에 따라 달라집니다.

이 자습서에서는 모든 쿼리를 Azure AD DS 도메인 컨트롤러로 보내도록 Azure 가상 네트워크 DNS 서버를 구성하겠습니다.

1. Azure Portal에서 피어링된 가상 네트워크의 리소스 그룹(예: *myResourceGroup*)을 선택합니다. 리소스 목록에서 피어링된 가상 네트워크(예: *myVnet*)를 선택합니다.
1. 가상 네트워크 창의 왼쪽 메뉴에서 **DNS 서버**를 선택합니다.
1. 기본적으로 가상 네트워크는 Azure에서 제공하는 기본 제공 DNS 서버를 사용합니다. **사용자 지정** DNS 서버를 사용하도록 선택합니다. Azure AD DS 도메인 컨트롤러의 IP 주소(일반적으로 *10.0.2.4* 및 *10.0.2.5*)를 입력합니다. 포털에 있는 관리되는 도메인의 **개요** 창에서 이러한 IP 주소를 확인합니다.

    ![Azure AD DS 도메인 컨트롤러를 사용하도록 가상 네트워크 DNS 서버를 구성합니다.](./media/tutorial-configure-networking/custom-dns.png)

1. 준비되면 **저장**을 선택합니다. 가상 네트워크의 DNS 서버를 업데이트하는 데 몇 분 정도 걸립니다.
1. 업데이트된 DNS 설정을 VM에 적용하려면 피어링된 가상 네트워크에 연결된 VM을 다시 시작합니다.

관리되는 도메인을 사용해야 하는 VM을 만들 때 이 피어링된 가상 네트워크를 선택해야 합니다. 다른 가상 네트워크를 선택하면 관리되는 도메인에 연결하기 위한 네트워크 연결 및 DNS 확인이 없습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Azure AD DS에 대한 도메인 조인 리소스에 대한 가상 네트워크 연결 옵션 이해
> * Azure AD DS 가상 네트워크에서 IP 주소 범위 및 추가 서브넷 만들기
> * Azure AD DS와 별도인 네트워크에 가상 네트워크 피어링 구성

이 관리되는 도메인이 작동하는지 확인하려면 가상 머신을 만들어 도메인에 조인시킵니다.

> [!div class="nextstepaction"]
> [Windows Server 가상 머신을 관리되는 도메인에 조인](join-windows-vm.md)

<!-- INTERNAL LINKS --> 
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[peering-overview]: ../virtual-network/virtual-network-peering-overview.md
[network-considerations]: network-considerations.md
