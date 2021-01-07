---
title: Azure Network Watcher의 효과적인 보안 규칙 보기 소개 | Microsoft Docs
description: 이 페이지에서는 Network Watcher 적용 되는 보안 규칙 보기 기능에 대 한 개요를 제공 합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: damendo
ms.openlocfilehash: d172ba7353b7ac4c490584cd7c84871c6244f214
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94961824"
---
# <a name="introduction-to-effective-security-rules-view-in-azure-network-watcher"></a>Azure Network Watcher의 효과적인 보안 규칙 보기 소개

네트워크 보안 그룹은 서브넷 수준 또는 NIC 수준에서 연결됩니다. 서브넷 수준에서 연결되는 경우 서브넷의 모든 VM 인스턴스에 적용됩니다. 효과적인 보안 규칙 보기는 구성에 대 한 통찰력을 제공 하는 가상 머신의 NIC 및 서브넷 수준에서 연결 된 모든 구성 된 NSGs 및 규칙을 반환 합니다. 또한 VM의 각 NIC에 대해 유효 보안 규칙이 반환됩니다. 효과적인 보안 규칙 보기를 사용 하 여 포트 열기와 같은 네트워크 취약성에 대 한 VM을 평가할 수 있습니다. 또한 네트워크 보안 그룹이 [구성된 보안 규칙과 승인된 보안 규칙 간의 비교](network-watcher-nsg-auditing-powershell.md)에 따라 예상대로 작동하는지 검증할 수도 있습니다.

더 확장된 사용 사례는 보안 규정 준수 및 감사에 있습니다. 조직의 보안 관리를 위해 규범적인 보안 규칙의 집합을 모델로 정의할 수 있습니다. 정기적 규정 준수 감사는 네트워크의 각 VM에 대해 규범적인 규칙을 유효 규칙과 비교하여 프로그래밍 방식으로 구현할 수 있습니다.

에서 포털 규칙은 각 네트워크 인터페이스에 대해 표시 되 고 인바운드 vs 아웃 바운드를 기준으로 그룹화 됩니다. 가상 머신에 적용되는 규칙에 대한 간단한 보기를 제공합니다. CSV 파일에 있는 탭에 관계 없이 모든 보안 규칙을 쉽게 다운로드하는 다운로드 단추가 제공됩니다.

![보안 그룹 보기][1]

규칙을 선택할 수 있으며 네트워크 보안 그룹 및 원본 및 대상 접두사를 표시하는 새 블레이드가 열립니다. 이 블레이드에서 네트워크 보안 그룹 리소스로 직접 이동할 수 있습니다.

![드릴다운][2]

### <a name="next-steps"></a>다음 단계

또한 아래에 나열 된 다른 방법을 통해 *유효한 보안 그룹* 기능을 사용할 수 있습니다.
* [REST API](/rest/api/virtualnetwork/NetworkInterfaces/ListEffectiveNetworkSecurityGroups)
* [PowerShell](/powershell/module/az.network/get-azeffectivenetworksecuritygroup?view=azps-4.4.0)
* [Azure CLI](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-list-effective-nsg)

[PowerShell을 사용하여 네트워크 보안 그룹 설정 감사](network-watcher-nsg-auditing-powershell.md)를 방문하여 네트워크 보안 그룹 설정을 감사하는 방법에 대해 알아보기

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png