---
title: Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 연결 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: alzam
ms.openlocfilehash: 0319e3aec71d37b49a094861fdcbb3b96b6def67
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585425"
---
# <a name="tutorial-create-a-user-vpn-connection-using-azure-virtual-wan"></a>자습서: Azure Virtual WAN을 사용하여 사용자 VPN 연결 만들기

이 자습서에서는 가상 WAN을 사용하여 IPsec/IKE(IKEv2) 또는 OpenVPN VPN 연결을 통해 Azure에서 리소스를 연결하는 방법을 보여줍니다. 이 연결 유형은 클라이언트 컴퓨터에서 클라이언트를 구성해야 합니다. 가상 WAN에 대한 자세한 내용은 [가상 WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * WAN 만들기
> * 허브 만들기
> * P2S 구성 만들기
> * VPN 클라이언트 프로필 다운로드
> * P2S 구성을 허브에 적용
> * 허브에 VNet 연결
> * VPN 클라이언트 구성 다운로드 및 적용
> * 가상 WAN 보기
> * 리소스 상태 보기

![Virtual WAN 다이어그램](./media/virtual-wan-about/virtualwanp2s.png)

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음 기준을 충족하는지 확인합니다.

* 연결하려는 가상 네트워크가 있습니다. 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../virtual-network/quick-create-portal.md)을 참조하세요.

* 가상 네트워크에 가상 네트워크 게이트웨이가 없습니다. 가상 네트워크에 게이트웨이(VPN 또는 ExpressRoute)가 있으면 모든 게이트웨이를 제거해야 합니다. 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에 연결되어야 합니다.

* 허브 지역의 IP 주소 범위를 확보합니다. 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 온-프레미스에 연결하는 주소 범위와도 겹칠 수 없습니다. 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="wan"></a>Virtual WAN 만들기

브라우저에서 [Azure 포털](https://portal.azure.com) 로 이동하고 Azure 계정으로 로그인합니다.

1. Virtual WAN 페이지로 이동합니다. 포털에서 **+리소스 만들기**를 클릭합니다. 검색 상자에 **Virtual WAN**을 입력하고 Enter를 선택합니다.
2. 결과에서 **Virtual WAN**을 선택합니다. Virtual WAN 페이지에서 **만들기**를 클릭하여 WAN 만들기 페이지를 엽니다.
3. **WAN 만들기** 페이지의 **기본 사항** 탭에서 다음 필드를 입력합니다.

   ![가상 WAN](./media/virtual-wan-point-to-site-portal/vwan.png)

   * **구독** - 사용할 Azure 구독을 선택합니다.
   * **리소스 그룹** - 새로 만들거나 기존 항목을 사용합니다.
   * **리소스 그룹 위치** - 드롭다운에서 리소스 위치를 선택합니다. WAN은 전역 리소스이며 특정 지역에 상주하지 않습니다. 하지만 만든 WAN 리소스를 보다 쉽게 관리하고 찾으려면 지역을 선택해야 합니다.
   * **이름** - WAN을 호출할 이름을 입력합니다.
   * **유형:** 표준입니다. 기본 WAN을 만드는 경우 기본 허브만 만들 수 있습니다. 기본 허브는 VPN 사이트 간 연결만 가능합니다.
4. 필드 작성을 완료한 후 **검토 + 만들기**를 선택합니다.
5. 유효성 검사를 통과하면 **만들기**를 선택하여 Virtual WAN을 만듭니다.

## <a name="site"></a>빈 가상 허브 만들기

1. 가상 WAN 아래에서 허브를 선택하고 **+새 허브**를 클릭합니다.

   ![새 사이트](media/virtual-wan-point-to-site-portal/hub1.jpg)
2. [가상 허브 만들기] 페이지에서 다음 필드를 채웁니다.

   **영역** - 가상 허브를 배포할 영역을 선택합니다.

   **이름** - 가상 허브를 호출할 이름을 입력합니다.

   **허브 프라이빗 주소 공간** - CIDR 표기법으로 된 허브의 주소 범위입니다.

   ![새 사이트](media/virtual-wan-point-to-site-portal/hub2.jpg)  
3. **검토 + 만들기**를 클릭합니다.
4. **유효성 검사 통과** 페이지에서 **만들기**를 클릭합니다.

## <a name="site"></a>P2S 구성 만들기

P2S 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

1. **모든 리소스**로 이동합니다.
2. 만든 가상 WAN을 클릭합니다.
3. 페이지 맨 위에서 **+사용자 VPN 구성 만들기**를 클릭하여 **새 사용자 VPN 구성 만들기** 페이지를 엽니다.

   ![새 사이트](media/virtual-wan-point-to-site-portal/p2s1.jpg)
4. **새 사용자 VPN 구성 만들기** 페이지에서 다음 필드를 채웁니다.

   **구성 이름** - 구성을 참조하는 데 사용하려는 이름입니다.

   **터널 종류** - 터널에 대해 사용할 프로토콜입니다.

   **루트 인증서 이름** - 인증서에 대한 설명이 포함된 이름입니다.

   **공용 인증서 데이터** - Base-64로 인코딩된 X.509 인증서 데이터입니다.
  
   ![새 사이트](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. **만들기**를 클릭하여 구성을 만듭니다.

## <a name="hub"></a>허브 할당 편집

1. 가상 WAN 아래의 **허브** 블레이드로 이동합니다.
2. vpn 서버 구성을 연결할 허브를 선택하고 **...** 를 클릭합니다.

   ![새 사이트](media/virtual-wan-point-to-site-portal/p2s4.jpg)
3. **가상 허브 편집**을 클릭합니다.
4. **지점 및 사이트 간 게이트웨이 포함** 확인란을 선택하고 원하는 **게이트웨이 배율 단위**를 선택합니다.

   ![새 사이트](media/virtual-wan-point-to-site-portal/p2s2.jpg)
5. VPN 클라이언트에 IP 주소를 할당할 **주소 풀**를 입력합니다.
6. **확인**을 클릭합니다.
7. 작업이 완료될 때까지 최대 30분이 걸릴 수 있습니다.

## <a name="device"></a>VPN 프로필 다운로드

VPN 프로필을 사용하여 클라이언트를 구성합니다.

1. 가상 WAN에 대한 페이지에서 **사용자 VPN 구성**을 클릭합니다.
2. 페이지 맨 위에서 **사용자 VPN 구성 다운로드**를 클릭합니다.
3. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.
4. 프로필 파일을 사용하여 VPN 클라이언트를 구성합니다.

### <a name="configure-user-vpn-clients"></a>사용자 VPN 클라이언트 구성
다운로드한 프로필을 사용하여 원격 액세스 클라이언트를 구성합니다. 각 운영 체제에 대한 프로시저가 다릅니다. 아래의 올바른 지침을 따르세요.

#### <a name="microsoft-windows"></a>Microsoft Windows
##### <a name="openvpn"></a>OpenVPN

1. 공식 웹 사이트에서 OpenVPN 클라이언트를 다운로드하고 설치합니다.
2. 게이트웨이에 대한 VPN 프로필을 다운로드합니다. Azure Portal의 사용자 VPN 구성 탭 또는 PowerShell의 New-AzureRmVpnClientConfiguration에서 이 작업을 수행할 수 있습니다.
3. 프로필의 압축을 풉니다. 메모장에서 OpenVPN 폴더의 vpnconfig.ovpn 구성 파일을 엽니다.
4. base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인증서를 내보내 인코드된 공개 키를 가져오는 방법은 여기를 참조하세요.
5. base64에서 P2S 클라이언트 인증서 프라이빗 키를 사용하여 프라이빗 키 섹션을 채웁니다. 프라이빗 키를 추출하는 방법은 여기를 참조하세요.
6. 다른 필드를 변경하지 마십시오. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
7. vpnconfig.ovpn 파일을 C:\Program Files\OpenVPN\config 폴더에 복사합니다.
8. 시스템 트레이에서 OpenVPN 아이콘을 마우스 오른쪽 단추로 클릭하고 연결을 클릭합니다.

##### <a name="ikev2"></a>IKEv2

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다.
2. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 자세한 정보, 실행을 차례로 클릭합니다.
3. 클라이언트 컴퓨터에서 네트워크 설정으로 이동하고 VPN을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.
4. 연결을 시도하기 전에 먼저 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다. 인증서 생성에 대한 자세한 내용은 인증서 생성을 참조하세요. 클라이언트 인증서를 설치하는 방법은 클라이언트 인증서 설치를 참조하세요.

## <a name="viewwan"></a>가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다. 점 위로 마우스를 가져가면 허브 상태 요약을 볼 수 있습니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="viewhealth"></a>리소스 상태 보기

1. WAN으로 이동합니다.
2. WAN 페이지의 **지원 및 문제 해결** 섹션에서 **상태**를 클릭하고 리소스를 봅니다.


## <a name="cleanup"></a>리소스 정리

리소스가 더 이상 필요하지 않은 경우 [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup)을 사용하여 리소스 그룹 및 여기에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.