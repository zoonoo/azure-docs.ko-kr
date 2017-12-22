---
title: "Azure Network Watcher를 사용하여 연결 확인 - Azure Portal | Microsoft Docs"
description: "이 페이지에서는 Azure Portal을 사용하여 Network Watcher를 통해 연결 확인을 사용하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2017
ms.author: jdial
ms.openlocfilehash: 1f19da71731039e1a39c4440f925b1369886a993
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/21/2017
---
# <a name="check-connectivity-with-azure-network-watcher-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Network Watcher를 통해 연결 확인

> [!div class="op_single_selector"]
> - [포털](network-watcher-connectivity-portal.md)
> - [PowerShell](network-watcher-connectivity-powershell.md)
> - [CLI 2.0](network-watcher-connectivity-cli.md)
> - [Azure REST API](network-watcher-connectivity-rest.md)

연결을 사용하여 가상 머신에서 지정된 끝점으로의 직접 TCP 연결을 설정할 수 있는지를 확인하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 사용자에게 다음 리소스가 있는 것으로 가정합니다.

* 연결을 확인하려는 영역의 Network Watcher 인스턴스

* 연결을 확인하는 데 사용할 가상 머신

> [!IMPORTANT]
> 연결 확인에는 가상 머신 확장 `AzureNetworkWatcherExtension`이 필요합니다. Windows VM에서 확장을 설치하려면 [Windows용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/windows/extensions-nwa.md)을 방문하고 Linux VM인 경우 [Linux용 Azure Network Watcher 에이전트 가상 머신 확장](../virtual-machines/linux/extensions-nwa.md)을 방문하세요.

## <a name="check-connectivity-to-a-virtual-machine"></a>가상 머신에 대한 연결 확인

이 예제에서는 포트 80을 통해 대상 가상 머신에 대한 연결을 확인합니다.

Network Watcher로 이동하고 **연결 확인(미리 보기)**을 클릭합니다. 가상 머신을 선택하여 연결을 확인합니다. **대상** 섹션에서 **가상 머신 선택**을 선택하고 테스트할 가상 머신과 포트를 정확히 선택합니다.

**확인**을 클릭하면 지정한 포트의 가상 머신 간 연결이 확인됩니다. 이 예에서는 대상 VM이 연결될 수 없으며 홉 목록이 표시됩니다.

![가상 머신에 대한 연결 결과 확인][1]

## <a name="check-remote-endpoint-connectivity"></a>원격 끝점 연결 확인

원격 끝점에 대한 연결 및 대기 시간을 확인하려면 **대상** 섹션에서 **수동으로 지정** 라디오 단추를 선택하고 URL 및 포트를 입력한 다음 **확인**을 클릭합니다.  이 방법은 웹 사이트 및 저장소 끝점과 같은 원격 끝점에 사용됩니다.

![웹 사이트에 대한 연결 확인 결과][2]

## <a name="next-steps"></a>다음 단계

[경고로 트리거된 패킷 캡처 만들기](network-watcher-alert-triggered-packet-capture.md)를 확인하여 가상 머신 경고로 패킷 캡처를 자동화하는 방법을 알아봅니다.

[IP 흐름 확인 확인](network-watcher-check-ip-flow-verify-portal.md)을 방문하여 특정 트래픽이 VM에서 허용되는지 알아봅니다.

[1]: ./media/network-watcher-connectivity-portal/figure1.png
[2]: ./media/network-watcher-connectivity-portal/figure2.png
