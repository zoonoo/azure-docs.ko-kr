---
title: '사용자 VPN 연결에 대 한 Azure AD 인증 구성: 가상 WAN'
description: 사용자 VPN에 대 한 Azure Active Directory 인증을 구성 하는 방법을 알아봅니다.
services: virtual-wan
author: kumudD
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/17/2020
ms.author: alzam
ms.openlocfilehash: 2028cae4908214db28de2545f02f5f2997eeb8af
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077480"
---
# <a name="configure-azure-active-directory-authentication-for-user-vpn"></a>사용자 VPN에 대 한 Azure Active Directory 인증 구성

이 문서에서는 OpenVPN VPN 연결을 통해 Azure에서 리소스에 연결 하도록 가상 WAN의 사용자 VPN에 대 한 Azure AD 인증을 구성 하는 방법을 보여 줍니다. Azure Active Directory 인증은 OpenVPN 프로토콜을 사용하는 게이트웨이와 Windows를 실행하는 클라이언트에서만 사용할 수 있습니다.

이 연결 유형은 클라이언트 컴퓨터에서 클라이언트를 구성해야 합니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * WAN 만들기
> * 허브 만들기
> * P2S 구성 만들기
> * VPN 클라이언트 프로필 다운로드
> * P2S 구성을 허브에 적용
> * 허브에 VNet 연결
> * VPN 클라이언트 구성 다운로드 및 적용
> * 가상 WAN 보기

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 연결하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이(VPN 또는 ExpressRoute)가 있으면 모든 게이트웨이를 제거해야 합니다. 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에 연결되어야 합니다.

* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-virtual-wan"></a><a name="wan"></a>Virtual WAN 만들기

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

1. Virtual WAN 페이지로 이동합니다. 포털에서 **+리소스 만들기**를 클릭합니다. 검색 상자에 **Virtual WAN**을 입력하고 Enter를 선택합니다.
2. 결과에서 **Virtual WAN**을 선택합니다. Virtual WAN 페이지에서 **만들기**를 클릭하여 WAN 만들기 페이지를 엽니다.
3. **WAN 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 입력합니다.

   ![가상 WAN](./media/virtual-wan-point-to-site-azure-ad/vwan.png)

   * **구독** - 사용할 Azure 구독을 선택합니다.
   * **리소스 그룹** - 새로 만들거나 기존 항목을 사용합니다.
   * **리소스 그룹 위치** - 드롭다운에서 리소스 위치를 선택합니다. WAN은 전역 리소스이며 특정 지역에 상주하지 않습니다. 하지만 만든 WAN 리소스를 보다 쉽게 관리하고 찾으려면 지역을 선택해야 합니다.
   * **이름** - WAN을 호출할 이름을 입력합니다.
   * **유형:** 표준입니다. 기본 WAN을 만드는 경우 기본 허브만 만들 수 있습니다. 기본 허브는 VPN 사이트 간 연결만 가능합니다.
4. 필드 작성을 완료한 후 **검토 + 만들기**를 선택합니다.
5. 유효성 검사가 통과되면 **만들기**를 선택하여 가상 WAN을 만듭니다.

## <a name="create-an-empty-virtual-hub"></a><a name="site"></a>빈 가상 허브 만들기

1. 가상 WAN 아래에서 허브를 선택 하 고 **+ 새 허브**를 클릭 합니다.

   ![새 사이트](media/virtual-wan-point-to-site-azure-ad/hub1.jpg)
2. [가상 허브 만들기] 페이지에서 다음 필드를 채웁니다.

   **영역** - 가상 허브를 배포할 영역을 선택합니다.

   **이름** - 가상 허브를 호출할 이름을 입력합니다.

   **허브 프라이빗 주소 공간** - CIDR 표기법으로 된 허브의 주소 범위입니다.

   ![새 사이트](media/virtual-wan-point-to-site-azure-ad/hub2.jpg)  
3. **검토 + 만들기**를 클릭합니다.
4. **유효성 검사 통과** 페이지에서 **만들기**를 클릭 합니다.

## <a name="create-a-new-p2s-configuration"></a><a name="site"></a>새 P2S 구성 만들기

P2S 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

1. 가상 WAN 아래에서 **사용자 VPN 구성**을 선택합니다.

   ![새로운 구성](media/virtual-wan-point-to-site-azure-ad/aadportal1.jpg)

2. **+사용자 VPN 구성 만들기**를 클릭합니다.

   ![새로운 구성](media/virtual-wan-point-to-site-azure-ad/aadportal2.jpg)

3. 정보를 입력하고 **만들기**를 클릭합니다.

   ![새로운 구성](media/virtual-wan-point-to-site-azure-ad/aadportal3.jpg)

## <a name="edit-hub-assignment"></a><a name="hub"></a>허브 할당 편집

1. 가상 WAN 아래의 **허브** 블레이드로 이동 합니다.
2. vpn 서버 구성을 연결할 허브를 선택하고, 줄임표(...)를 클릭합니다.

   ![새 사이트](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. **가상 허브 편집**을 클릭합니다.
4. **지점 및 사이트 간 게이트웨이 포함** 확인란을 선택하고 원하는 **게이트웨이 배율 단위**를 선택합니다.

   ![새 사이트](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. VPN 클라이언트에 IP 주소를 할당할 **주소 풀**를 입력합니다.
6. **확인**을 클릭합니다.
7. 작업이 완료될 때까지 최대 30분이 걸릴 수 있습니다.

## <a name="download-vpn-profile"></a><a name="device"></a>VPN 프로필 다운로드

VPN 프로필을 사용하여 클라이언트를 구성합니다.

1. 가상 WAN에 대한 페이지에서 **사용자 VPN 구성**을 클릭합니다.
2. 페이지 맨 위에서 **사용자 VPN 구성 다운로드**를 클릭합니다.
3. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.
4. 프로필 파일을 사용하여 VPN 클라이언트를 구성합니다.

## <a name="configure-user-vpn-clients"></a>사용자 VPN 클라이언트 구성

연결하려면 Azure VPN Client를 다운로드하고, 이전 단계에서 VNet에 연결하려는 모든 컴퓨터에 다운로드한 VPN 클라이언트 프로필을 가져와야 합니다.

> [!NOTE]
> Azure AD 인증은 OpenVPN 프로토콜 연결에 대해서만 지원 됩니다 &reg; .
>

#### <a name="to-download-the-azure-vpn-client"></a>Azure VPN 클라이언트를 다운로드하려면,

이 [링크](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)를 사용하여 Azure VPN Client를 다운로드합니다.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>클라이언트 프로필을 가져오려면,

1. 페이지에서 **가져오기**를 선택합니다.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. 프로필 xml 파일을 찾아서 선택합니다. 파일이 선택된 상태에서 **열기**를 선택합니다.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. 프로필 이름을 지정하고, **저장**을 선택합니다.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. **연결**을 선택하여 VPN에 연결합니다.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. 연결되면 아이콘이 녹색으로 바뀌고 **연결됨**으로 표시됩니다.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>클라이언트 프로필을 삭제하려면,

1. 삭제하려는 클라이언트 프로필 옆의 줄임표(...)를 선택합니다. 그런 다음, **제거**를 선택합니다.

    ![삭제](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. **제거**를 선택하여 삭제합니다.

    ![삭제](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose-connection-issues"></a><a name="diagnose"></a>연결 문제 진단

1. 연결 문제를 진단하려면 **진단** 도구를 사용할 수 있습니다. 진단하려는 VPN 연결 옆에 있는 줄임표(...)를 선택하여 메뉴를 표시합니다. 그런 다음, **진단**을 선택합니다.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. **연결 속성** 페이지에서 **진단 실행**을 선택합니다.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. 자격 증명을 사용하여 로그인합니다.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. 진단 결과를 살펴봅니다.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="view-your-virtual-wan"></a><a name="viewwan"></a>가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.


## <a name="clean-up-resources"></a><a name="cleanup"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
