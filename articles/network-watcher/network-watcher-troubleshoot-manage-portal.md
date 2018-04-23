---
title: Azure Virtual Network 게이트웨이 및 연결 문제 해결 - 포털 | Microsoft Docs
description: 이 페이지에서는 Azure Network Watcher 문제 해결 포털을 사용하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2017
ms.author: jdial
ms.openlocfilehash: becc4f799e34aabba5c703f09e51a4b7d497c45a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-virtual-network-gateway-and-connections-using-network-watcher-from-azure-portal"></a>Azure Portal에서 Network Watcher를 사용하여 Virtual Network 게이트웨이 및 연결 문제 해결

> [!div class="op_single_selector"]
> - [포털](network-watcher-troubleshoot-manage-portal.md)
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI 1.0](network-watcher-troubleshoot-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

Network Watcher는 Azure에서 네트워크 리소스를 이해하는 데 관련된 다양한 기능을 제공합니다. 이러한 기능 중 하나는 리소스 문제 해결입니다. 리소스 문제 해결은 포털, PowerShell, CLI 또는 REST API를 통해 호출할 수 있습니다. Network Watcher가 호출되면 Virtual Network 게이트웨이 또는 연결의 상태를 검사하거나 해당 결과를 반환합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다.

지원되는 게이트웨이 유형 목록을 보려면 [지원되는 게이트웨이 유형](network-watcher-troubleshoot-overview.md#supported-gateway-types)을 방문하세요.

## <a name="overview"></a>개요

리소스 문제 해결은 Virtual Network 게이트웨이 및 연결에 발생한 문제를 해결하는 기능을 제공합니다. 리소스 문제 해결을 요청하는 경우 로그를 쿼리하고 검사합니다. 검사가 완료되면 결과가 반환됩니다. 리소스 문제 해결 요청은 장기 실행 요청이며 결과를 반환하는 데 몇 분이 걸릴 수 있습니다. 문제를 해결하는 로그는 지정된 저장소 계정의 컨테이너에 저장됩니다.

## <a name="troubleshoot-a-gateway-or-connection"></a>게이트웨이 또는 연결 문제 해결

1. [Azure Portal](https://portal.azure.com)로 이동하여 **네트워킹** > **Network Watcher** > **VPN 진단**을 클릭합니다.
2. **구독**, **리소스 그룹** 및 **위치**를 선택합니다.
3. 리소스 문제 해결은 리소스 상태에 대한 데이터를 반환합니다. 또한 검토할 관련 로그를 저장소 계정에 저장합니다. **Storage 계정**을 클릭하여 Storage 계정을 선택합니다.
4. **Storage 계정** 블레이드에서 기존 Storage 계정을 선택하거나 **+ Storage 계정**을 클릭하여 새 Storage 계정을 만듭니다.
5. **컨테이너** 블레이드에서 기존 컨테이너를 선택하거나 **+ 컨테이너**를 클릭하여 새 컨테이너를 만듭니다. 완료되면 **저장**을 클릭합니다.
6. 문제를 해결할 게이트웨이 및 연결 리소스를 선택하고 **문제 해결 시작**을 클릭합니다.

여러 리소스를 선택한 경우 문제 해결은 게이트웨이 리소스에 동시에 실행됩니다. 연결 및 그와 연결된 게이트웨이에서 동시에 실행될 수 없습니다. 연결 문제 해결이 더 긴 프로세스이므로 게이트웨이 문제를 먼저 해결하는 것이 좋습니다. VPN 진단이 리소스에서 실행 중일 때 **문제 해결 상태** 열에 **실행 중** 상태가 표시됩니다.

완료되면 상태 열이 **정상** 또는 **비정상**으로 변경됩니다.

![문제 해결 완료][2]

## <a name="understanding-the-results"></a>결과 이해

창의 **세부 정보** 섹션에 있는 **상태** 탭에는 선택한 리소스에 대한 마지막 문제 해결 실행의 상태가 표시됩니다. 마지막 진단 결과는 마지막 실행 xx분 후에 표시됩니다.

|자산  |설명  |
|---------|---------|
|리소스     | 리소스에 대한 링크        |
|저장소 경로     |  로그(실행 중에 생성된 경우)가 포함된 컨테이너 및 저장소 계정 경로 이 설정은 포털을 벗어난 후에는 유지되지 않습니다.        |
|요약     | 리소스 상태에 대한 요약        |
|세부 정보     | 리소스 상태에 대한 자세한 정보        |
|마지막 실행     | 마지막 문제 해결이 실행된 시간        |


**Action** 탭에는 문제를 해결하는 방법에 대한 일반적인 지침이 제공됩니다. 문제에 대한 조치를 취할 수 있는 경우 링크는 추가 설명서와 함께 제공됩니다. 추가 지침이 없는 경우에 응답은 지원 사례를 열 URL을 제공합니다.  응답의 속성 및 포함된 항목에 대한 자세한 내용은 [Network Watcher 문제 해결 개요](network-watcher-troubleshoot-overview.md)를 방문하세요.


## <a name="next-steps"></a>다음 단계

VPN 연결을 중지하도록 설정이 변경된 경우 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)를 참조하여 문제가 될 수 있는 네트워크 보안 그룹 및 보안 규칙을 추적합니다.


[2]: ./media/network-watcher-troubleshoot-manage-portal/2.png
