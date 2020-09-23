---
title: Azure Network Watcher의 네트워크 구성 진단 소개 | Microsoft Docs
description: 이 페이지는 Network Watcher-네트워크 구성 진단의 개요를 제공 합니다.
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
ms.openlocfilehash: 4d308b8a1a589308e2481c8bb9e0dc9ec64be25b
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90996699"
---
# <a name="introduction-to-network-configuration-diagnostics-in-azure-network-watcher"></a>Azure Network Watcher의 네트워크 구성 진단 소개

네트워크 구성 진단 도구를 사용 하 여 Azure Virtual Network에서 허용 되거나 거부 되는 트래픽 흐름과 디버그에 대 한 자세한 정보를 파악할 수 있습니다. NSG 규칙이 올바르게 구성 되었는지 이해 하는 데 도움이 될 수 있습니다. 

## <a name="pre-requisites"></a>필수 구성 요소
네트워크 구성 진단을 사용 하려면 구독에서 Network Watcher를 사용 하도록 설정 해야 합니다. 사용 하도록 설정 하려면 [Azure Network Watcher 인스턴스 만들기](https://docs.microsoft.com/azure/network-watcher/network-watcher-create) 를 참조 하세요.

## <a name="background"></a>배경

- Azure의 리소스는 Vnet (가상 네트워크) 및 서브넷을 통해 연결 됩니다. 이러한 Vnet 및 서브넷의 보안은 NSG (네트워크 보안 그룹)를 사용 하 여 관리할 수 있습니다.
- NSG에는 연결 된 리소스에 대 한 네트워크 트래픽을 허용 하거나 거부 하는 보안 규칙 목록이 포함 되어 있습니다. NSGs는 서브넷, 개별 Vm 또는 Vm에 연결 된 개별 Nic (네트워크 인터페이스)와 연결할 수 있습니다. 
- 네트워크의 모든 트래픽 흐름은 해당 NSG의 규칙을 사용 하 여 평가 됩니다.
- 규칙은 우선 순위 번호에서 가장 높은 값을 기준으로 평가 됩니다. 

## <a name="how-does-network-configuration-diagnostic-work"></a>네트워크 구성 진단이 작동 하는 방식 

지정 된 흐름에서 NCD 도구는 흐름의 시뮬레이션을 실행 하 고 흐름을 허용 (또는 거부) 하 고 규칙에 대 한 자세한 정보를 흐름을 허용/거부 하는지 여부를 반환 합니다.  고객은 원본, 대상, 프로토콜 등과 같은 흐름의 세부 정보를 제공 해야 합니다. 이 도구는 트래픽이 허용 되거나 거부 되었는지 여부, 지정 된 흐름에 대해 평가 된 NSG 규칙 및 모든 규칙에 대 한 평가 결과를 반환 합니다.

## <a name="next-steps"></a>다음 단계

다른 인터페이스를 통한 네트워크 구성 진단 사용
 - [REST API](https://docs.microsoft.com/rest/api/network-watcher/networkwatchers/getnetworkconfigurationdiagnostic)
 - [PowerShell](https://docs.microsoft.com/powershell/module/az.network/invoke-aznetworkwatchernetworkconfigurationdiagnostic?view=azps-4.6.1)
 - [Azure CLI](https://docs.microsoft.com/cli/azure/network/watcher?view=azure-cli-latest#az_network_watcher_run_configuration_diagnostic)

