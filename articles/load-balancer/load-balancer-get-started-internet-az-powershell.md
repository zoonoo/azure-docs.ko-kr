---
title: PowerShell을 사용하여 영역 중복 공용 IP 주소 프런트 엔드로 공용 부하 분산 장치 표준 만들기 | Microsoft Docs
description: PowerShell을 사용하여 영역 중복 공용 IP 주소 프런트 엔드로 공용 부하 분산 장치 표준을 만드는 방법 알아보기
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: kumud
ms.openlocfilehash: 2849ef9b862b4471f348ac83a4a18eb9a3a4d42a
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/23/2018
---
#  <a name="create-a-public-load-balancer-standard-with-zone-redundant-public-ip-address-frontend-using-powershell"></a>PowerShell을 사용하여 영역 중복 공용 IP 주소 프런트 엔드로 공용 부하 분산 장치 표준 만들기

이 문서에서는 공용 IP 표준 주소를 사용하는 영역 중복 프런트 엔드로 공용 [부하 분산 장치 표준](https://aka.ms/azureloadbalancerstandard)을 만드는 단계를 안내합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="register-for-availability-zones-preview"></a>가용성 영역 미리 보기에 등록

이 문서를 사용하려 AzureRM 모듈 4.4.0 버전 이상이 설치되어 있어야 합니다. 버전을 확인하려면 `Get-Module -ListAvailable AzureRM`을 실행합니다. 설치 또는 업그레이드해야 할 경우 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureRM)에서 최신 버전의 AzureRM 모듈을 설치합니다.

> [!NOTE]
> 가용성 영역은 미리 보기 상태이며 개발 및 테스트 시나리오에 대한 준비를 마쳤습니다. Azure 리소스, 지역 및 VM 크기 제품군을 선택하는 데 지원을 사용할 수 있습니다. 시작하는 방법에 대한 자세한 내용 및 가용성 영역을 사용해 볼 수 있는 Azure 리소스, 지역 및 VM 크기 제품군은 [가용성 영역 개요](https://docs.microsoft.com/azure/availability-zones/az-overview)를 참조하세요. 지원을 위해 [StackOverflow](https://stackoverflow.com/questions/tagged/azure-availability-zones)에 연결하거나 [Azure 지원 티켓을 열](../azure-supportability/how-to-create-azure-support-request.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 수 있습니다.  

## <a name="log-in-to-azure"></a>Azure에 로그인

`Login-AzureRmAccount` 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>리소스 그룹 만들기

다음 명령을 사용하여 리소스 그룹을 만듭니다.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location westeurope
```

## <a name="create-a-public-ip-standard"></a>공용 IP 표준 만들기 
다음 명령을 사용하여 공용 IP 표준을 만듭니다.

```powershell
$publicIp = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Name 'myPublicIP' `
  -Location westeurope -AllocationMethod Static -Sku Standard 
```

## <a name="create-a-front-end-ip-configuration-for-the-website"></a>웹 사이트에 대한 프런트 엔드 IP 구성 만들기

다음 명령을 사용하여 프런트 엔드 IP 구성을 만듭니다.

```powershell
$feip = New-AzureRmLoadBalancerFrontendIpConfig -Name 'myFrontEndPool' -PublicIpAddress $publicIp
```

## <a name="create-the-back-end-address-pool"></a>백 엔드 주소 풀 만들기

다음 명령을 사용하여 백 엔드 주소 풀을 만듭니다.

```powershell
$bepool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name 'myBackEndPool'
```

## <a name="create-a-load-balancer-probe-on-port-80"></a>포트 80에서 부하 분산 장치 프로브 만들기

다음 명령을 사용하여 포트 80에서 부하 분산 장치에 대한 상태 프로브를 만듭니다.

```powershell
$probe = New-AzureRmLoadBalancerProbeConfig -Name 'myHealthProbe' -Protocol Http -Port 80 `
  -RequestPath / -IntervalInSeconds 360 -ProbeCount 5
```

## <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기
 다음 명령을 사용하여 부하 분산 장치 규칙을 만듭니다.

```powershell
   $rule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $feip -BackendAddressPool  $bepool -Probe $probe -Protocol Tcp -FrontendPort 80 -BackendPort 80
```

## <a name="create-a-load-balancer"></a>부하 분산 장치 만들기
다음 명령을 사용하여 부하 분산 장치 표준을 만듭니다.

```powershell
$lb = New-AzureRmLoadBalancer -ResourceGroupName myResourceGroup -Name 'MyLoadBalancer' -Location westeurope `
  -FrontendIpConfiguration $feip -BackendAddressPool $bepool `
  -Probe $probe -LoadBalancingRule $rule -Sku Standard
```

## <a name="next-steps"></a>다음 단계
- [가용성 영역에 공용 IP 만드는](../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) 방법 알아보기



