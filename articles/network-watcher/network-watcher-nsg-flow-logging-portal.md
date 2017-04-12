---
title: "Azure Network Watcher를 사용하여 네트워크 보안 그룹 흐름 로그 관리 | Microsoft Docs"
description: "이 페이지에서는 Azure Network Watcher의 네트워크 보안 그룹 흐름 로그를 관리하는 방법을 설명합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 01606cbf-d70b-40ad-bc1d-f03bb642e0af
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: d716f11818bc0ad6dd9e5f93951b011dd6774c7b
ms.lasthandoff: 03/22/2017


---

# <a name="manage-network-security-group-flow-logs-in-the-azure-portal"></a>Azure Portal에서 네트워크 보안 그룹 흐름 로그 관리

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

네트워크 보안 그룹 흐름 로그는 네트워크 보안 그룹을 통해 수신 및 송신 IP 트래픽에 대한 정보를 볼 수 있는 Network Watcher의 기능입니다. 이러한 흐름 로그는 json 형식으로 작성되고 트래픽이 허용되거나 거부된 경우 각 규칙을 기준으로 아웃바운드 및 인바운드 흐름, 흐름이 적용되는 NIC, 흐름에 대한 5개의 튜플 정보(원본/대상 IP, 원본/대상 포트, 프로토콜)를 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에

이 시나리오에서는 사용자가 Network Watcher를 만드는 [Network Watcher 만들기](network-watcher-create.md)의 단계를 이미 수행했다고 가정합니다. 또한 시나리오에서는 유효한 가상 컴퓨터를 포함한 리소스 그룹을 사용할 수 있다고 가정합니다.

## <a name="enable-flow-logs"></a>흐름 로그를 사용하도록 설정

다음 단계는 네트워크 보안 그룹에서 흐름 로그를 사용하도록 설정하는 방법을 안내합니다.

### <a name="step-1"></a>1단계

Network Watcher 인스턴스로 이동하고 **흐름 로그**를 선택합니다.

![흐름 로그 개요][1]

### <a name="step-2"></a>2단계

목록에서 클릭하여 네트워크 보안 그룹을 선택합니다.

![흐름 로그 개요][2]

### <a name="step-3"></a>3단계 

**흐름 로그 설정** 블레이드에서 상태를 **켜기**로 설정하고 저장소 계정을 구성합니다.  완료되면 **확인**을 클릭하고 **저장**을 클릭합니다.

![흐름 로그 개요][3]

## <a name="download-flow-logs"></a>흐름 로그 다운로드

흐름 로그는 저장소 계정에 저장됩니다. 흐름 로그를 보려면 다운로드해야 합니다.

### <a name="step-1"></a>1단계

흐름 로그를 다운로드하려면 **구성된 저장소 계정에서 흐름 로그를 다운로드할 수 있습니다**를 클릭합니다.  그러면 저장소 계정 보기가 표시되며 여기에서 다운로드할 로그에 이동할 수 있습니다.

![흐름 로그 설정][4]

### <a name="step-2"></a>2단계

올바른 저장소 계정으로 이동한 후 **컨테이너** > **insights-log-networksecuritygroupflowevent**로 이동합니다.

![흐름 로그 설정][5]

### <a name="step-3"></a>3단계

흐름 로그 위치로 드릴다운하고 흐름 로그를 선택하고 **다운로드**를 클릭합니다.

![흐름 로그 설정][6]

로그의 구조에 대한 내용은 [네트워크 보안 그룹 흐름 로그 개요](network-watcher-nsg-flow-logging-overview.md)를 방문하세요.

## <a name="next-steps"></a>다음 단계

[PowerBI를 사용하여 NSG 흐름 로그를 시각화](network-watcher-visualize-nsg-flow-logs-power-bi.md)하는 방법 알아보기

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-portal/figure1.png
[2]: ./media/network-watcher-nsg-flow-logging-portal/figure2.png
[3]: ./media/network-watcher-nsg-flow-logging-portal/figure3.png
[4]: ./media/network-watcher-nsg-flow-logging-portal/figure4.png
[5]: ./media/network-watcher-nsg-flow-logging-portal/figure5.png
[6]: ./media/network-watcher-nsg-flow-logging-portal/figure6.png

