---
title: Azure Network Watcher의 네트워크 구성 진단 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher - 네트워크 구성 진단의 개요를 제공합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2020
ms.author: damendo
ms.openlocfilehash: 5feef79a08789ad381b0c93cb938abd9effdfcc8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "102502011"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure Network Watcher의 네트워크 구성 진단 소개

네트워크 구성 진단 도구를 통해 고객은 Azure Virtual Network에서 허용되거나 거부될 트래픽 흐름과 디버깅에 대한 자세한 정보를 파악할 수 있습니다. NSG 규칙이 올바르게 구성되었는지 이해하는 데 도움이 될 수 있습니다. 

## <a name="pre-requisites"></a>필수 구성 요소
네트워크 구성 진단을 사용하려면 구독에서 Network Watcher를 사용하도록 설정해야 합니다. 사용하도록 설정하려면 [Azure Network Watcher 인스턴스 만들기](./network-watcher-create.md)를 참조하세요.

## <a name="background"></a>배경

- Azure의 리소스는 VNET(Virtual Network) 및 서브넷을 통해 연결됩니다. 이러한 VNet 및 서브넷의 보안은 NSG(네트워크 보안 그룹)를 사용하여 관리할 수 있습니다.
- NSG에는 연결된 리소스에 대한 네트워크 트래픽을 허용하거나 거부하는 보안 규칙 목록이 포함되어 있습니다. NSG는 서브넷, 개별 VM 또는 VM에 연결된 개별 네트워크 인터페이스(NIC)와 연결할 수 있습니다. 
- 네트워크의 모든 트래픽 흐름은 해당 NSG의 규칙을 사용하여 평가합니다.
- 규칙은 가장 낮음부터 가장 높음까지 우선순위 번호에 따라 평가됩니다. 

## <a name="how-does-network-configuration-diagnostic-work"></a>네트워크 구성 진단이 작동하는 방식 

주어진 흐름의 경우, NCD 도구는 흐름의 시뮬레이션을 실행하고 흐름이 허용(또는 거부)되는지 여부와 흐름을 허용/거부하는 규칙에 대한 자세한 정보를 반환합니다.  고객은 원본, 대상, 프로토콜 등과 같은 흐름의 세부 정보를 제공해야 합니다. 이 도구는 트래픽 허용 여부, 지정된 흐름에 대해 평가된 NSG 규칙 및 모든 규칙에 대한 평가 결과를 반환합니다.

## <a name="next-steps"></a>다음 단계

다른 인터페이스를 통한 네트워크 구성 진단 사용
 - [REST API](/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic)
 - [Azure CLI](/cli/azure/network/watcher#az_network_watcher_run_configuration_diagnostic)