---
title: Azure Network Watcher를 사용하여 네트워크 보안 그룹 흐름 로그 관리 | Microsoft Docs
description: 이 페이지에서는 Azure Network Watcher의 네트워크 보안 그룹 흐름 로그를 관리하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: cb41781c5ac8fb759cecea01402c08dd716bf7d7
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2018
---
# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Azure Portal에서 네트워크 보안 그룹 흐름 로그 관리

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI 1.0](network-watcher-nsg-flow-logging-cli-nodejs.md)
> - [CLI 2.0](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

네트워크 보안 그룹 흐름 로그는 네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 이러한 흐름 로그는 JSON 형식으로 작성되며 다음과 같은 중요한 정보를 제공합니다. 

- 규칙 단위 기반의 아웃바운드 및 인바운드 흐름
- 흐름이 적용되는 NIC
- 흐름에 대한 5-튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)
- 트래픽에 대한 허용 또는 거부 여부 정보

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음 리소스가 이미 있어야 합니다.

- 기존 Network Watcher Network Watcher를 만들려면 [Network Watcher 인스턴스 만들기](network-watcher-create.md)를 참조하세요.
- 유효한 가상 머신이 있는 기존 리소스 그룹 가상 머신이 없는 경우 [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 또는 [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) 가상 머신 만들기를 참조하세요.

## <a name="register-insights-provider"></a>Insights 공급자 등록

흐름 로깅이 성공적으로 작동하기 위해서 **Microsoft.Insights** 공급자를 등록해야 합니다. 공급자를 등록하려면 다음 단계를 수행합니다. 

1. **구독**으로 이동한 다음 흐름 로그를 사용하도록 설정할 구독을 선택합니다. 
2. **구독** 블레이드에서 **리소스 공급자**를 선택합니다. 
3. 공급자의 목록을 살펴보고 **microsoft.insights** 공급자가 등록되어 있는지 확인합니다. 그렇지 않으면 **등록**을 선택합니다.

![공급자 보기][providers]

## <a name="enable-flow-logs"></a>흐름 로그를 사용하도록 설정

다음 단계는 네트워크 보안 그룹에서 흐름 로그를 사용하도록 설정하는 프로세스를 안내합니다.

### <a name="step-1"></a>1단계

Network Watcher 인스턴스로 이동한 다음 **NSG 흐름 로그**를 선택합니다.

![흐름 로그 개요][1]

### <a name="step-2"></a>2단계

목록에서 네트워크 보안 그룹을 선택합니다.

![흐름 로그 개요][2]

### <a name="step-3"></a>3단계 

**흐름 로그 설정** 블레이드에서 상태를 **켜기**로 설정한 다음 저장소 계정을 구성합니다. 저장소 계정에 대한 **설정** 아래의 **방화벽 및 가상 네트워크** 아래에서 **모든 네트워크**(기본값)를 선택한 기존 저장소 계정을 선택합니다. 저장소 계정을 선택한 후 **확인**을 선택한 다음, **저장**을 선택합니다.

![흐름 로그 개요][3]

## <a name="download-flow-logs"></a>흐름 로그 다운로드

흐름 로그는 저장소 계정에 저장됩니다. 보려는 흐름 로그를 다운로드합니다.

### <a name="step-1"></a>1단계

흐름 로그를 다운로드하려면 **구성된 저장소 계정에서 흐름 로그를 다운로드할 수 있습니다**를 선택합니다. 이 단계에서는 저장소 계정 보기로 이동하여 다운로드할 로그를 선택할 수 있습니다.

![흐름 로그 설정][4]

### <a name="step-2"></a>2단계

올바른 저장소 계정으로 이동합니다. 그런 다음 **컨테이너** > **insights-log-networksecuritygroupflowevent**를 선택합니다.

![흐름 로그 설정][5]

### <a name="step-3"></a>3단계

흐름 로그의 위치로 이동하여 선택한 다음 **다운로드**를 선택합니다.

![흐름 로그 설정][6]

로그의 구조에 대한 내용은 [네트워크 보안 그룹 흐름 로그 개요](network-watcher-nsg-flow-logging-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

[PowerBI를 사용하여 NSG 흐름 로그를 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)하는 방법 알아보기

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png
[providers]: ./media/network-watcher-nsg-flow-logging-portal/providers.png
