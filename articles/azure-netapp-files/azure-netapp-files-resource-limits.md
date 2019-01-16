---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: 용량 풀, 볼륨 및 위임된 서브넷에 대한 제한을 포함하여 Azure NetApp Files 리소스에 대한 제한을 설명합니다.
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
ms.topic: reference
ms.date: 01/03/2019
ms.author: b-juche
ms.openlocfilehash: f34afb1df2ae38353f29a80bfb6798c16856dbeb
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54056521"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한
Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="capacity_pools"></a>용량 풀

- 단일 용량 풀의 최소 크기는 4TiB이고, 최대 크기는 500TiB입니다. 
- 각 용량 풀은 하나의 NetApp 계정에만 속할 수 있습니다. 그러나 NetApp 계정에는 여러 개의 용량 풀이 있을 수 있습니다.  

## <a name="volumes"></a>볼륨

- 단일 볼륨의 최소 크기는 100GiB이고, 최대 크기는 92TiB입니다.
- Azure 구독당 지역당 최대 100개의 볼륨을 갖출 수 있습니다.  

## <a name="delegated_subnet"></a>위임된 서브넷 

각 Azure Vnet(Virtual Network)에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
