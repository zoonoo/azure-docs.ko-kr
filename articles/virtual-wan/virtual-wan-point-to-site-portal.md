---
title: Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 연결 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure Virtual WAN을 사용하여 Azure에 지점 및 사이트 간 VPN 연결을 만드는 방법을 알아봅니다.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alzam
Customer intent: As someone with a networking background, I want to connect remote users to my VNets using Virtual WAN and I don't want to go through a Virtual WAN partner.
ms.openlocfilehash: 9fe0c7f7ae0c19833421b647449f0e4100904f5b
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226235"
---
# <a name="tutorial-create-a-point-to-site-connection-using-azure-virtual-wan-preview"></a>자습서: Azure Virtual WAN을 사용하여 지점 및 사이트 간 연결 만들기(미리 보기)

이 자습서에서는 가상 WAN을 사용하여 IPsec/IKE(IKEv2) 또는 OpenVPN VPN 연결을 통해 Azure에서 리소스를 연결하는 방법을 보여줍니다. 이 연결 유형은 클라이언트 컴퓨터에서 클라이언트를 구성해야 합니다. Virtual WAN에 대한 자세한 내용은 [Virtual WAN 개요](virtual-wan-about.md)를 참조하세요.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * WAN 만들기
> * P2S 구성 만들기
> * 허브 만들기
> * P2S 구성을 허브에 적용
> * 허브에 VNet 연결
> * VPN 클라이언트 구성 다운로드 및 적용
> * 가상 WAN 보기
> * 리소스 상태 보기
> * 연결 모니터링

> [!IMPORTANT]
> 이 공개 미리 보기는 Service Level Agreement(서비스 수준 약정)없이 제공되므로 프로덕션 워크로드에 사용하지 말아야 합니다. 특정 기능은 지원되지 않을 수 있거나, 기능이 제한될 수 있거나 모든 Azure 위치에서 사용하지는 못할 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
>

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="register"></a>이 기능 등록

**TryIt**을 클릭하고 Azure Cloud Shell을 사용하여 간편하게 이 기능을 등록하세요. PowerShell을 로컬로 실행하려면 최신 버전이 있는지 확인하고, **Connect-AzAccount** 및 **Select-AzSubscription** 명령을 사용하여 로그인합니다.

>[!NOTE]
>이 기능을 등록하지 않으면 사용할 수 없거나 포털에서 표시되지 않습니다.
>
>

**TryIt**을 클릭하여 Azure Cloud Shell을 연 후, 다음 명령을 복사하여 붙여넣습니다.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```
 
```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowP2SCortexAccess
```

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.Network -FeatureName AllowVnetGatewayOpenVpnProtocol
```

기능이 등록된 것으로 표시되면 구독을 Microsoft.Network 네임스페이스에 등록합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

## <a name="vnet"></a>1. 가상 네트워크 만들기

[!INCLUDE [Create a virtual network](../../includes/virtual-wan-tutorial-vnet-include.md)]

## <a name="openvwan"></a>2. 가상 WAN 만들기

브라우저에서 [Azure Portal(미리 보기)](https://aka.ms/azurevirtualwanpreviewfeatures)로 이동하고 Azure 계정으로 로그인합니다.

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-vwan-include.md)]

## <a name="hub"></a>3. 허브 만들기

> [!NOTE]
> 이 단계에서 "지점 및 사이트 간 게이트웨이 포함" 설정을 선택하지 마십시오.
>

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-tutorial-hub-include.md)]

## <a name="site"></a>4. P2S 구성 만들기

P2S 구성은 원격 클라이언트 연결에 대한 매개 변수를 정의합니다.

1. **모든 리소스**로 이동합니다.
2. 만든 가상 WAN을 클릭합니다.
3. **Virtual WAN 아키텍처** 아래에서 **지점 및 사이트 간 구성**을 클릭합니다.
4. 페이지의 맨 위에서 **+지점 및 사이트 간 구성 추가**를 클릭하여 **새 지점 및 사이트 간 구성 만들기** 페이지를 엽니다.
5. **새 지점 및 사이트 간 구성 만들기** 페이지에서 다음 필드를 입력합니다.

   *  **구성 이름** - 구성을 참조하는 데 사용하려는 이름입니다.
   *  **터널 종류** - 터널에 대해 사용할 프로토콜입니다.
   *  **주소 풀** - 클라이언트가 할당되는 IP 주소 풀입니다.
   *  **루트 인증서 이름** - 인증서에 대한 설명이 포함된 이름입니다.
   *  **루트 인증서 데이터** - Base-64로 인코딩된 X.509 인증서 데이터입니다.

6. **만들기**를 클릭하여 구성을 만듭니다.

## <a name="hub"></a>5. 허브 할당 편집

1. 가상 WAN에 대한 페이지에서 **허브**를 클릭합니다.
2. 지점 및 사이트 간 구성을 할당하려는 허브를 선택합니다.
3. **"..."** 을 클릭하고 **가상 허브 편집**을 선택합니다.
4. **지점 및 사이트 간 게이트웨이 포함**을 선택합니다.
5. 드롭다운에서 **게이트웨이 배율 단위**를 선택합니다.
6. 드롭다운에서 만든 **지점 및 사이트 간 구성**을 선택합니다.
7. 클라이언트에 대한 **주소 풀**을 구성합니다.
8. **확인**을 클릭합니다. 이 작업을 완료하려면 최대 30분이 걸릴 수 있습니다.

## <a name="vnet"></a>6. 허브에 VNet 연결

이 단계에서는 허브와 VNet 간에 피어링 연결을 만듭니다. 연결하려는 각 VNet에 대해 이 단계를 반복합니다.

1. 가상 WAN에 대한 페이지에서 **가상 네트워크 연결**을 클릭합니다.
2. 가상 네트워크 연결 페이지에서 **+연결 추가**를 클릭합니다.
3. **연결 추가** 페이지에서 다음 필드를 채웁니다.

    * **연결 이름** - 연결의 이름을 지정합니다.
    * **허브** - 이 연결과 연결할 허브를 선택합니다.
    * **구독** - 구독을 확인합니다.
    * **가상 네트워크** - 이 허브에 연결할 가상 네트워크를 선택합니다. 가상 네트워크에는 기존의 가상 네트워크 게이트웨이를 사용할 수 없습니다.
4. **확인**을 클릭하여 연결을 추가합니다.

## <a name="device"></a>7. VPN 프로필 다운로드

VPN 프로필을 사용하여 클라이언트를 구성합니다.

1. 가상 WAN에 대한 페이지에서 **허브**를 클릭합니다.
2. 프로필을 다운로드하려는 허브를 선택합니다.
3. **"..."** 을 클릭하고 **프로필 다운로드**를 선택합니다. 
4. 파일 만들기가 끝나면 링크를 클릭하여 다운로드할 수 있습니다.
4. 프로필 파일을 사용하여 지점 및 사이트 간 클라이언트를 구성합니다.

## <a name="device"></a>8. 지점 및 사이트 간 클라이언트 구성
다운로드한 프로필을 사용하여 원격 액세스 클라이언트를 구성합니다. 각 운영 체제에 대한 프로시저가 다릅니다. 아래의 올바른 지침을 따르세요.

### <a name="microsoft-windows"></a>Microsoft Windows
#### <a name="openvpn"></a>OpenVPN

1.  공식 웹 사이트에서 OpenVPN 클라이언트를 다운로드하고 설치합니다.
2.  게이트웨이에 대한 VPN 프로필을 다운로드합니다. 이 작업은 Azure Portal의 지점 및 사이트 간 구성 탭 또는 PowerShell의 New-AzVpnClientConfiguration에서 수행할 수 있습니다.
3.  프로필의 압축을 풉니다. 메모장에서 OpenVPN 폴더의 vpnconfig.ovpn 구성 파일을 엽니다.
4.  base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인증서를 내보내 인코드된 공개 키를 가져오는 방법은 여기를 참조하세요.
5.  base64에서 P2S 클라이언트 인증서 프라이빗 키를 사용하여 프라이빗 키 섹션을 채웁니다. 프라이빗 키를 추출하는 방법은 여기를 참조하세요.
6.  다른 필드를 변경하지 마십시오. 클라이언트 입력에 채워진 구성을 사용하여 VPN에 연결합니다.
7.  vpnconfig.ovpn 파일을 C:\Program Files\OpenVPN\config 폴더에 복사합니다.
8.  시스템 트레이에서 OpenVPN 아이콘을 마우스 오른쪽 단추로 클릭하고 연결을 클릭합니다.

#### <a name="ikev2"></a>IKEv2

1. Windows 컴퓨터의 아키텍처에 해당하는 VPN 클라이언트 구성 파일을 선택합니다. 64비트 프로세서 아키텍처의 경우 'VpnClientSetupAmd64' 설치 관리자 패키지를 선택합니다. 32비트 프로세서 아키텍처의 경우 'VpnClientSetupX86' 설치 관리자 패키지를 선택합니다.
2. 해당 패키지를 두 번 클릭하여 설치합니다. SmartScreen 팝업이 표시되면 자세한 정보, 실행을 차례로 클릭합니다.
3. 클라이언트 컴퓨터에서 네트워크 설정으로 이동하고 VPN을 클릭합니다. VPN 연결에서 연결되는 가상 네트워크의 이름을 표시합니다.
4. 연결을 시도하기 전에 먼저 클라이언트 컴퓨터에 클라이언트 인증서가 설치되어 있어야 합니다. Azure 기본 인증서 인증 유형을 사용할 때 인증을 위해 클라이언트 인증서가 필요합니다. 인증서 생성에 대한 자세한 내용은 인증서 생성을 참조하세요. 클라이언트 인증서를 설치하는 방법은 클라이언트 인증서 설치를 참조하세요.

### <a name="mac-os-x"></a>Mac(OS X)
#### <a name="openvpn"></a>OpenVPN

1.  https://tunnelblick.net/downloads.html에서 TunnelBlik와 같은 OpenVPN 클라이언트를 다운로드 및 설치합니다. 
2.  게이트웨이에 대한 VPN 프로필을 다운로드합니다. 이 작업은 Azure Portal의 지점 및 사이트 간 구성 탭 또는 PowerShell의 New-AzVpnClientConfiguration에서 수행할 수 있습니다.
3.  프로필의 압축을 풉니다. 메모장에서 OpenVPN 폴더의 vpnconfig.ovpn 구성 파일을 엽니다.
4.  base64에서 P2S 클라이언트 인증서 공개 키를 사용하여 P2S 클라이언트 인증서 섹션을 채웁니다. PEM 형식의 인증서에서 .cer 파일을 열고 인증서 헤더 사이에 base64 키를 복사할 수 있습니다. 인증서를 내보내 인코드된 공개 키를 가져오는 방법은 여기를 참조하세요.
5.  base64에서 P2S 클라이언트 인증서 프라이빗 키를 사용하여 프라이빗 키 섹션을 채웁니다. 프라이빗 키를 추출하는 방법은 여기를 참조하세요.
6.  다른 필드를 변경하지 마십시오. 클라이언트 입력의 채워진 구성을 사용하여 VPN에 연결합니다.
7.  프로필 파일을 두 번 클릭하여 tunnelblik에서 프로필 만들기
8.  애플리케이션 폴더에서 Tunnelblik 시작
9.  시스템 트레이에서 Tunneblik 아이콘을 클릭하고 연결 선택

#### <a name="ikev2"></a>IKEv2

Azure는 원시 Azure 인증서 인증을 위해 mobileconfig 파일을 제공하지 않습니다. Azure에 연결할 모든 Mac에서 원시 IKEv2 VPN 클라이언트를 수동으로 구성해야 합니다. Generic 폴더에는 이것을 구성하는 데 필요한 모든 정보가 들어 있습니다. 다운로드에 Generic 폴더가 보이지 않으면 IKEv2가 터널 유형으로 선택되지 않았을 가능성이 큽니다. IKEv2가 선택되면 zip 파일을 다시 생성하여 Generic 폴더를 검색합니다. Generic 폴더에 포함되는 파일은 다음과 같습니다.

- VpnSettings.xml - 서버 주소 및 터널 종류와 같은 중요한 설정이 포함되어 있습니다.
- VpnServerRoot.cer - P2S 연결 설정에서 Azure VPN Gateway의 유효성을 검사하는 데 필요한 루트 인증서가 포함되어 있습니다.

## <a name="viewwan"></a>9. 가상 WAN 보기

1. 가상 WAN 탭으로 이동합니다.
2. 개요 페이지의 맵에 있는 각 점은 허브를 나타냅니다. 점 위로 마우스를 가져가면 허브 상태 요약을 볼 수 있습니다.
3. 허브 및 연결 섹션에서 허브 상태, 사이트, 지역, VPN 연결 상태 및 입/출력 바이트를 볼 수 있습니다.

## <a name="viewhealth"></a>10. 리소스 상태 보기

1. WAN으로 이동합니다.
2. WAN 페이지의 **지원 및 문제 해결** 섹션에서 **상태**를 클릭하고 리소스를 봅니다.

## <a name="connectmon"></a>11. 연결 모니터링

Azure VM과 원격 사이트 간의 통신을 모니터링하는 연결을 만듭니다. 연결 모니터를 설정하는 방법에 대한 자세한 내용은 [네트워크 통신 모니터링](~/articles/network-watcher/connection-monitor.md)을 참조하세요. 원본 필드는 Azure의 VM IP이고 대상 IP는 사이트 IP입니다.

## <a name="cleanup"></a>12. 리소스 정리

이러한 리소스가 더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 및 이에 포함된 모든 리소스를 제거할 수 있습니다. "myResourceGroup"을 리소스 그룹의 이름으로 바꾸고 다음 PowerShell 명령을 실행합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * WAN 만들기
> * 사이트 만들기
> * 허브 만들기
> * 사이트에 허브 연결
> * 허브에 VNet 연결
> * VPN 디바이스 구성 다운로드 및 적용
> * 가상 WAN 보기
> * 리소스 상태 보기
> * 연결 모니터링

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
