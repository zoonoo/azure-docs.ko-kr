---
title: "Azure에서 가상 네트워크 만들기 - Portal | Microsoft Docs"
description: "Azure Portal을 사용하여 가상 네트워크를 만드는 방법을 빠르게 알아봅니다. 가상 네트워크를 사용하면 다양한 유형의 Azure 리소스가 서로 개인적으로 통신할 수 있습니다."
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b1dbe96b9f522474cd2eeb2b63f3429f9ea4d8ed
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/24/2018
---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Azure 포털을 사용하여 가상 네트워크 만들기

이 문서에서는 가상 네트워크를 만드는 방법을 설명합니다. 가상 네트워크를 만든 후, 가상 네트워크에 두 개의 가상 머신을 배포하고 둘 간의 개인 네트워크 통신을 테스트합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인 

Azure Portal( http://portal.azure.com )에 로그인합니다.

## <a name="create-a-virtual-network"></a>가상 네트워크 만들기

1. Azure Portal의 왼쪽 위에 있는 **+ 새로 만들기**를 선택합니다.

2. **네트워킹**을 선택한 다음 **가상 네트워크**를 선택합니다.

3. 다음 그림에 나와 있는 것처럼 **이름**으로 *myVirtualNetwork*를, **리소스 그룹**으로 *myResourceGroup*을 입력하고 **위치** 및 **구독**을 선택한 후 나머지 기본값은 그대로 적용하고 **만들기**를 선택합니다. 

    ![가상 네트워크에 대한 기본 정보 입력](./media/quick-create-portal/virtual-network.png)

    **주소 공간**은 CIDR 표기법으로 지정됩니다. 가상 네트워크는 0개 이상의 서브넷을 포함합니다. 기본 서브넷 **주소 범위** 10.0.0.0/24는 가상 네트워크의 전체 주소 범위를 사용하므로, 기본 주소 공간 및 범위를 사용하는 가상 네트워크 내에는 다른 서브넷을 만들 수 없습니다. 지정된 주소 범위는 IP 주소 10.0.0.0-10.0.0.254를 포함합니다. 그러나 Azure는 각 서브넷에서 처음 4개 주소(0-3)와 마지막 주소를 예약하기 때문에 10.0.0.4-10.0.0.254만 사용할 수 있습니다. 가상 네트워크 내에서 배포된 리소스에 사용 가능한 IP 주소가 할당됩니다.

## <a name="test-network-communication"></a>네트워크 통신 테스트

가상 네트워크를 사용하면 여러 유형의 Azure 리소스가 서로 개인적으로 통신할 수 있습니다. 가상 네트워크에 배포할 수 있는 한 가지 유형의 리소스는 가상 머신입니다. 가상 네트워크에서 두 가상 머신을 만들어 이후 단계에서 둘 간의 개인 통신 유효성을 검사할 수 있도록 합니다.

### <a name="create-virtual-machines"></a>가상 머신 만들기

1. Azure Portal의 왼쪽 위에 있는 **새로 만들기** 단추를 선택합니다.

2. **Compute**를 선택한 후 **Windows Server 2016 Datacenter**를 선택합니다.

3. 다음에 나오는 그림에 표시된 가상 머신 정보를 입력합니다. 입력하는 **사용자 이름** 및 **암호**는 이후 단계에서 가상 머신에 로그인하는 데 사용됩니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다. **구독**을 선택하고 기존 *myResourceGroup* 리소스 그룹을 사용하도록 선택한 후 선택한 **위치**가 가상 네트워크를 만든 것과 같은 위치인지 확인합니다. 완료되면 **확인**을 선택합니다.

    ![가상 머신에 대한 기본 정보 입력](./media/quick-create-portal/virtual-machine-basics.png)

4. 가상 머신의 크기를 선택하고 **선택**을 선택합니다. 더 많은 크기를 보려면 **모두 보기**를 선택하거나 **지원되는 디스크 형식** 필터를 변경합니다. 사용자에 대해 표시되는 크기가 아래 예제와 다를 수 있습니다. 

    ![가상 머신의 크기 선택](./media/quick-create-portal/virtual-machine-size.png)

5. **설정** 아래에서 **가상 네트워크**로 *myVirtualNetwork*가 이미 선택되어 있지만, 선택되어 있지 않으면 **가상 네트워크**를 선택하고 *myVirtualNetwork*를 선택합니다. **서브넷**을 *기본값* 상태로 두고 **확인**을 선택합니다.

    ![가상 네트워크 선택](./media/quick-create-portal/virtual-machine-network-settings.png)

6. **요약** 페이지에서 **만들기**를 선택하여 가상 머신 배포를 시작합니다. 

7. 가상 머신을 만드는 데 몇 분 정도 걸립니다. 만들어진 가상 머신은 Azure Portal 대시보드에 고정되고, 가상 머신 요약이 자동으로 열립니다. **네트워킹**을 선택합니다.

    ![가상 머신 네트워킹 정보](./media/quick-create-portal/virtual-machine-networking.png)

    **개인 IP** 주소가 *10.0.0.4*로 표시됩니다. 5단계에서는 **설정**에서 *myVirtualNetwork* 가상 네트워크를 선택했으며 **서브넷**에 대해 *default*라는 서브넷을 그대로 사용했습니다. [가상 네트워크를 만들 때](#create-a-virtual-network) 서브넷 **주소 범위**로 기본값인 10.0.0.0/24를 그대로 적용했습니다. Azure의 DHCP 서버는 서브넷에 대해 선택한 첫 번째 사용 가능한 주소를 가상 머신에 할당합니다. Azure는 각 서브넷의 처음 4개 주소(0-3)를 예약하므로 서브넷에 사용 가능한 첫 번째 IP 주소는 10.0.0.4입니다.

    할당된 **공용 IP** 주소는 가상 머신에 할당된 주소와 다릅니다. Azure는 기본적으로 각 가상 머신에 공용 인터넷 라우팅 가능 IP 주소를 할당합니다. 공용 IP 주소는 [각 Azure 지역에 할당된 주소 풀](https://www.microsoft.com/download/details.aspx?id=41653)에서 가상 머신에 할당됩니다. Azure는 가상 머신에 할당된 공용 IP 주소를 알고 있지만, 가상 머신에서 실행되는 운영 체제는 할당된 공용 IP 주소를 인식하지 못합니다.

8. 1-7단계를 다시 완료하지만 이번에는 3단계에서 가상 머신의 이름을 *myVm2*로 지정합니다. 

9. 가상 머신을 만든 후 7단계에서와 같이 **네트워킹**을 선택합니다. **개인 IP** 주소가 *10.0.0.5*로 표시됩니다. Azure는 이전에 서브넷의 사용 가능한 첫 번째 주소인 *10.0.0.4*를 *myVm1* 가상 머신에 할당했으므로, 서브넷의 사용 가능한 다음 주소인 *10.0.0.5*를 *myVm2* 가상 머신에 할당했습니다.

### <a name="connect-to-a-virtual-machine"></a>가상 머신에 연결

1. *myVm1* 가상 머신에 원격으로 연결합니다. Azure Portal 맨 위에 *myVm1*을 입력합니다. 검색 결과에서 표시되는 **myVm1**을 선택합니다. **연결** 단추를 선택합니다.

    ![가상 머신 개요](./media/quick-create-portal/virtual-machine-overview.png)

2. **연결** 단추를 선택하면 원격 데스크톱 프로토콜(.rdp) 파일이 만들어진 후 컴퓨터에 다운로드됩니다.  

3. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 선택합니다. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 선택하여 연결을 진행합니다.

### <a name="validate-communication"></a>통신 유효성 검사

Ping은 Windows 방화벽을 통과하도록 허용되지 않으므로 기본적으로 Windows 가상 머신으로의 Ping 시도는 실패합니다. *myVm1*에 대한 Ping을 허용하려면 명령 프롬프트에서 다음 명령을 입력합니다.

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow
```

*myVm2*와의 통신 유효성을 검사하려면 *myVm1* 가상 머신의 명령 프롬프트에서 다음 명령을 입력합니다. 가상 머신을 만들 때 사용한 자격 증명을 제공한 후 연결을 완료합니다.

```
mstsc /v:myVm2
```

두 가상 머신이 *default* 서브넷에서 할당된 개인 IP 주소를 가지고 기본적으로 Windows 방화벽을 통해 원격 데스크톱이 열려 으므로 원격 데스크톱 연결은 성공적으로 수행됩니다. Azure는 가상 네트워크 내의 모든 호스트에 대해 DNS 이름 확인을 제공하므로 호스트 이름으로 *myVm2*에 연결할 수 있습니다. 명령 프롬프트에서 *myVm2*에서 *myVm1*으로 Ping합니다.

```
ping myvm1
```

이전 단계에서 *myVm1* 가상 머신의 Windows 방화벽을 통해 Ping이 수행되도록 허용했으므로 Ping은 성공적으로 수행됩니다. 인터넷으로의 아웃바운드 통신을 확인하려면 다음 명령을 입력합니다.

```
ping bing.com
```

bing.com에서 4개의 응답을 받게 됩니다. 기본적으로 가상 네트워크의 모든 가상 머신은 인터넷으로 아웃바운드 통신을 할 수 있습니다. 

원격 데스크톱 세션을 끝냅니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우, 리소스 그룹 및 해당 콘텐츠를 모두 삭제합니다. Azure Portal 맨 위에 *myResourceGroup*을 입력합니다. 검색 결과에 **MyResourceGroup**이 표시되면 선택합니다. **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 1개의 서브넷이 있는 기본 가상 네트워크를 배포했습니다. 다중 서브넷으로 사용자 지정 가상 네트워크를 만드는 방법을 알아보려면 사용자 지정 가상 네트워크를 만들기 위한 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [사용자 지정 가상 네트워크 만들기](virtual-networks-create-vnet-arm-pportal.md#portal)
