---
title: Azure Virtual WAN 가상 허브 경로 테이블을 생성하여 NVA 조정 | Microsoft Docs
description: 네트워크 가상 어플라이언스에 대한 트래픽을 조정하기 위한 가상 WAN 가상 허브 경로 테이블.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to work with routing tables for NVA.
ms.openlocfilehash: fc8dd6770efa1c057a56374ddc0094c2d88d2eb5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60457620"
---
# <a name="create-a-virtual-hub-route-table-to-steer-traffic-to-a-network-virtual-appliance"></a>가상 허브 경로 테이블을 생성하여 네트워크 가상 어플라이언스에 대한 트래픽 조정

이 문서에서는 가상 허브에서 네트워크 가상 어플라이언스로의 트래픽을 조정하는 방법을 보여줍니다. 

![Virtual WAN 다이어그램](./media/virtual-wan-route-table/vwanroute.png)

이 문서에서는 다음 방법을 알아봅니다.

* WAN 만들기
* 허브 만들기
* 허브 가상 네트워크 연결 만들기
* 허브 경로 만들기
* 경로 테이블 만들기
* 경로 테이블 적용

## <a name="before-you-begin"></a>시작하기 전에

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 기준을 충족하는지 확인합니다.

1. 네트워크 가상 어플라이언스 (NVA) 해야합니다. 일반적으로 프로 비전 되는 Azure Marketplace에서 가상 네트워크에서 선택한 타사 소프트웨어입니다.
2. NVA 네트워크 인터페이스에 지정된 프라이빗 IP가 있습니다. 
3. 가상 허브에서 NVA는 배포할 수 없습니다. 별도의 VNet에 배포해야 합니다. 이 문서에서는 NVA VNet을 'DMZ VNet'이라고 합니다.
4. 'DMZ VNet'에는 하나 이상의 가상 네트워크가 연결되어 있을 수 있습니다. 이 문서에서는 이 VNet을 '간접 스포크 VNet'이라고 합니다. 이러한 VNet은 VNet 피어링을 사용하여 DMZ VNet에 연결할 수 있습니다.
5. 2개의 VNet이 이미 생성되었는지 확인합니다. 이 Vnet은 스포크 VNet으로 사용됩니다. 이 문서에서 VNet 스포크 주소 공간은 10.0.2.0/24 및 10.0.3.0/24입니다. VNet을 만드는 방법에 관한 정보가 필요하면 [PowerShell을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-powershell.md)를 참조하세요.
6. VNet에 가상 네트워크 게이트웨이가 없는지 확인합니다.

## <a name="signin"></a>1. 로그인

최신 버전의 Resource Manager PowerShell cmdlet을 설치합니다. PowerShell cmdlet 설치에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/install-az-ps)을 참조하세요. 이는 이 연습에 필요한 현재 값이 이전 버전의 cmdlet에 포함되어 있지 않으므로 중요합니다.

1. 상승된 권한으로 PowerShell 콘솔을 열고 Azure 계정에 로그인합니다. 이 cmdlet은 로그인 자격 증명을 요구하는 메시지를 표시합니다. 로그인한 다음, Azure PowerShell에 사용할 수 있도록 계정 설정을 다운로드합니다.

   ```powershell
   Connect-AzAccount
   ```
2. Azure 구독 목록을 가져옵니다.

   ```powershell
   Get-AzSubscription
   ```
3. 사용할 구독을 지정합니다.

   ```powershell
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```

## <a name="rg"></a>2. 리소스 만들기

1. 리소스 그룹을 만듭니다.

   ```powershell
   New-AzResourceGroup -Location "West US" -Name "testRG"
   ```
2. 가상 WAN 만들기

   ```powershell
   $virtualWan = New-AzVirtualWan -ResourceGroupName "testRG" -Name "myVirtualWAN" -Location "West US"
   ```
3. 가상 허브 만들기

   ```powershell
   New-AzVirtualHub -VirtualWan $virtualWan -ResourceGroupName "testRG" -Name "westushub" -AddressPrefix "10.0.1.0/24"
   ```

## <a name="connections"></a>3. 연결 만들기

간접 스포크 VNet과 DMZ VNet에서 가상 허브로의 허브 가상 네트워크 연결을 생성합니다.

  ```powershell
  $remoteVirtualNetwork1= Get-AzVirtualNetwork -Name "indirectspoke1" -ResourceGroupName "testRG"
  $remoteVirtualNetwork2= Get-AzVirtualNetwork -Name "indirectspoke2" -ResourceGroupName "testRG"
  $remoteVirtualNetwork3= Get-AzVirtualNetwork -Name "dmzvnet" -ResourceGroupName "testRG"

  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection1" -RemoteVirtualNetwork $remoteVirtualNetwork1
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection2" -RemoteVirtualNetwork $remoteVirtualNetwork2
  New-AzVirtualHubVnetConnection -ResourceGroupName "testRG" -VirtualHubName "westushub" -Name  "testvnetconnection3" -RemoteVirtualNetwork $remoteVirtualNetwork3
  ```

## <a name="route"></a>4. 가상 허브 경로 만들기

이 문서에서 간접 스포크 VNet 주소 공간은 10.0.2.0/24 및 10.0.3.0/24이고 DMZ NVA 네트워크 인터페이스 개인 IP 주소는 10.0.4.5입니다.

```powershell
$route1 = New-AzVirtualHubRoute -AddressPrefix @("10.0.2.0/24", "10.0.3.0/24") -NextHopIpAddress "10.0.4.5"
```

## <a name="applyroute"></a>5. 가상 허브 경로 테이블 만들기

가상 허브 경로 테이블을 생성한 다음 생성된 경로를 해당 테이블에 적용합니다.
 
```powershell
$routeTable = New-AzVirtualHubRouteTable -Route @($route1)
```

## <a name="commit"></a>6. 변경 내용 커밋

변경 내용을 가상 허브에 커밋합니다.

```powershell
Update-AzVirtualHub -VirtualWanId $virtualWan.Id -ResourceGroupName "testRG" -Name "westushub" -RouteTable $routeTable
```

## <a name="next-steps"></a>다음 단계

가상 WAN에 대해 자세히 알아보려면 [가상 WAN 개요](virtual-wan-about.md) 페이지를 참조하세요.
