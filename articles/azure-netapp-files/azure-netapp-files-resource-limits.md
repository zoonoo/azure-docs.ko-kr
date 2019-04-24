---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: NetApp 계정, 용량 풀, 볼륨, 스냅숏 및 위임된 서브넷에 대한 제한 사항을 포함하여 Azure NetApp Files 리소스에 대한 제한 사항을 설명합니다.
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
ms.topic: conceptual
ms.date: 02/14/2019
ms.author: b-juche
ms.openlocfilehash: 897ca26bcbb05287d33a4fb8e731ca959e39e271
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60452639"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

- 각 Azure 구독에는 최대 10개의 NetApp 계정이 있을 수 있습니다.
- 각 NetApp 계정에는 최대 25개의 용량 풀이 있을 수 있습니다.
- 각 용량 풀은 하나의 NetApp 계정에만 속할 수 있습니다.  
- 단일 용량 풀의 최소 크기는 4TiB이고, 최대 크기는 500TiB입니다. 
- 각 용량 풀에는 최대 500개의 볼륨이 있을 수 있습니다.
- 단일 볼륨의 최소 크기는 100GiB이고, 최대 크기는 92TiB입니다.
- 각 볼륨에는 최대 255개의 스냅숏이 있을 수 있습니다.
- 각 Azure Vnet(Virtual Network)에는 Azure NetApp Files에 위임된 하나의 서브넷만 있을 수 있습니다.

**다음 단계**

[Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
