---
title: Azure NetApp Files의 스토리지 계층 구조란? | Microsoft Docs
description: Azure NetApp Files 계정, 용량 풀 및 볼륨을 포함하는 저장소 계층 구조를 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/16/2019
ms.author: b-juche
ms.openlocfilehash: c2984e012ae83a8bc17d72ed4eac0c5c469c2694
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522879"
---
# <a name="what-is-the-storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp Files의 스토리지 계층 구조란?

Azure NetApp Files에서 볼륨을 만들기 전에 프로비전된 용량에 대한 풀을 구매하고 설정해야 합니다.  용량 풀을 설정하려면 NetApp 계정이 있어야 합니다. 저장소 계층 구조를 이해하는 것은 Azure NetApp Files 리소스를 설정하고 관리하는 데 도움이 됩니다.

## <a name="azure_netapp_files_account"></a>NetApp 계정

- NetApp 계정은 구성 용량 풀의 관리 그룹으로 사용됩니다.  
- NetApp 계정은 일반 Azure 저장소 계정과 동일하지 않습니다. 
- NetApp 계정은 범위에서 지역적입니다.   
- 지역에서 여러 NetApp 계정을 가질 수 있지만 각 NetApp 계정은 단일 지역에만 연결됩니다.

## <a name="capacity_pools"></a>용량 풀

- 용량 풀은 해당 프로비전된 용량에 의해 측정됩니다.  
- 용량은 구입한 고정 SKU(예: 4TiB 용량)를 통해 프로비전됩니다.
- 용량 풀은 하나의 서비스 수준만 가질 수 있습니다.  
- 각 용량 풀은 하나의 NetApp 계정에만 속할 수 있습니다. 그러나 NetApp 계정에는 여러 개의 용량 풀이 있을 수 있습니다.  
- 용량 풀은 NetApp 계정 간에 이동할 수 없습니다.   
  예를 들어 아래 [저장소 계층 구조의 개념 다이어그램](#conceptual_diagram_of_storage_hierarchy)에서 용량 풀 1은 미국 동부 NetApp 계정에서 미국 서부 2 NetApp 계정으로 이동할 수 없습니다.  
- 용량 풀은 용량 풀 내 모든 볼륨이 삭제되기 전까지 삭제할 수 없습니다.

## <a name="volumes"></a>볼륨

- 볼륨은 논리적 용량 사용량으로 측정되며 확장 가능합니다. 
- 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.
- 각 볼륨은 하나의 풀에만 속하지만 풀은 여러 볼륨을 포함할 수 있습니다. 
- 볼륨은 용량 풀 간에 이동할 수 없습니다. <!--Within the same NetApp account, you can move a volume across pools.  -->   
  예를 들어 아래 [스토리지 계층 구조의 개념 다이어그램](#conceptual_diagram_of_storage_hierarchy)에서 용량 풀 1에서 용량 풀 2로 볼륨을 이동할 수 없습니다.
- 볼륨은 해당하는 모든 스냅샷이 삭제되기 전까지 삭제할 수 없습니다.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>저장소 계층 구조의 개념 다이어그램 
다음 예제에서는 Azure 구독, NetApp 계정, 용량 풀 및 볼륨의 관계를 보여줍니다.   

![저장소 계층 구조의 개념 다이어그램](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
- [Azure NetApp Files에 등록](azure-netapp-files-register.md)
