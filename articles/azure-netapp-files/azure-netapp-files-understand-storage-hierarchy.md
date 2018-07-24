---
title: Azure NetApp Files의 저장소 계층 구조 이해 | Microsoft Docs
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 6f5ed4e7ede9a098d69b7a40f44dd60f9b400472
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010993"
---
# <a name="understand-the-storage-hierarchy-of-azure-netapp-files"></a>Azure NetApp Files의 저장소 계층 구조 이해

Azure NetApp Files에서 볼륨을 만들기 전에 프로비전된 용량에 대한 풀을 구매하고 설정해야 합니다.  용량 풀을 설정하려면 NetApp 계정이 있어야 합니다. 저장소 계층 구조를 이해하는 것은 Azure NetApp Files 리소스를 설정하고 관리하는 데 도움이 됩니다.

## <a name="azure_netapp_files_account"></a>NetApp 계정

- NetApp 계정은 구성 용량 풀의 관리 그룹으로 사용됩니다.  
- NetApp 계정은 일반 Azure 저장소 계정과 동일하지 않습니다. 
- NetApp 계정은 범위에서 지역적입니다.   
- 지역에서 여러 NetApp 계정을 가질 수 있지만 각 NetApp 계정은 단일 지역에만 연결됩니다.

## <a name="capacity_pools"></a>용량 풀

- 용량 풀은 해당 프로비전된 용량에 의해 측정됩니다.  
- 용량은 구입한 고정된 SKU(예: 4TB 용량)로 프로비전됩니다.
- 용량 풀은 하나의 서비스 수준만 가질 수 있습니다.  
  현재 프리미엄 서비스 수준만 제공됩니다.
- 각 용량 풀은 하나의 NetApp 계정에만 속합니다.  
- 용량 풀은 NetApp 계정 간에 이동할 수 없습니다.   
  예를 들어 아래 [저장소 계층 구조의 개념 다이어그램](#conceptual_diagram_of_storage_hierarchy)에서 용량 풀 1은 미국 동부 NetApp 계정에서 미국 서부 2 NetApp 계정으로 이동할 수 없습니다.  

## <a name="volumes"></a>볼륨

- 볼륨은 논리 용량 사용량에 의해 측정되며 볼륨당 최대 100TB까지 확장 가능합니다.
- 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.
- 각 볼륨은 하나의 풀에만 속하지만 풀은 여러 볼륨을 포함할 수 있습니다. 
- 동일한 NetApp 계정 내에서 풀 간에 볼륨을 이동할 수 있습니다.    
  예를 들어 아래 [저장소 계층 구조의 개념 다이어그램](#conceptual_diagram_of_storage_hierarchy)에서 용량 풀 1에서 용량 풀 2로 볼륨을 이동할 수 있습니다.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>저장소 계층 구조의 개념 다이어그램 
다음 예제에서는 Azure 구독, NetApp 계정, 용량 풀 및 볼륨의 관계를 보여줍니다.   

![저장소 계층 구조의 개념 다이어그램](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>다음 단계

1. [NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)
2. [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
3. [Azure NetApp Files에 대한 볼륨 만들기](azure-netapp-files-create-volumes.md)
4. [볼륨에 대한 내보내기 정책 구성(선택 사항)](azure-netapp-files-configure-export-policy.md)