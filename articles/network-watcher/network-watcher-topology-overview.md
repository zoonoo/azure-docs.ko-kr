---
title: "Azure Network Watcher에서 토폴로지 소개 | Microsoft Docs"
description: "이 페이지는 Network Watcher 토폴로지 기능에 대한 개요를 제공합니다."
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e753a435-38e0-482b-846b-121cb547555c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fa79ab0ab80481c8969c6fa653e92b10985cab3b
ms.openlocfilehash: 42443f614b76b8180ac163b9889163021adbf048
ms.lasthandoff: 03/31/2017

---

# <a name="introduction-to-topology-in-azure-network-watcher"></a>Azure Network Watcher에서 토폴로지 소개

토폴로지는 가상 네트워크에서 네트워크 리소스의 그래프를 반환합니다. 이 그래프는 리소스 간 상호 연결을 보여 주고 종단 간 네트워크 연결을 나타냅니다.

![토폴로지 개요][1]

포털에서 토폴로지는 가상 네트워크를 기반으로 리소스 개체를 반환합니다. 관계는 Network Watcher 지역 외부에 있는 리소스로 리소스 그룹에 표시되지 않더라도 리소스 간에 선으로 표시됩니다. 포털 보기에서 반환된 리소스는 그래프 표시된 네트워킹 구성 요소의 하위 집합입니다. 전체 네트워킹 리소스 목록을 보려면 [PowerShell](network-watcher-topology-powershell.md) 또는 [REST](network-watcher-topology-rest.md)를 사용할 수 있습니다.

> [!NOTE]
> 토폴로지를 실행할 각 지역에 Network Watcher의 인스턴스가 필요합니다.

리소스가 반환되면 리소스 간 연결은 두 관계로 모델링됩니다.

- **포함** - 예: Virtual Network는 NIC를 포함하는 서브넷을 포함합니다.
- **연결됨** -예제: NIC는 VM과 연결됩니다.

### <a name="next-steps"></a>다음 단계

[PowerShell에서 Network Watcher 토폴로지](network-watcher-topology-powershell.md)에서 PowerShell을 사용하여 토폴로지 보기를 검색하는 방법에 대해 알아보세요.

<!--Image references-->

[1]: ./media/network-watcher-topology-overview/topology.png

