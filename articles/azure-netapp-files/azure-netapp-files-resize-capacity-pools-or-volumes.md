---
title: Azure NetApp Files에 대한 용량 풀 또는 볼륨의 크기 조정 | Microsoft Docs
description: 용량 풀 또는 볼륨의 크기를 변경하는 방법을 설명합니다.
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
ms.date: 05/14/2019
ms.author: b-juche
ms.openlocfilehash: c58ceef57b984f46b86bb2a8577c53b75082b78b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65794622"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>용량 풀 또는 볼륨 크기 조정
필요에 따라 용량 풀 또는 볼륨의 크기를 변경할 수 있습니다. 

## <a name="resize-the-capacity-pool"></a>용량 풀 크기 조정 

1-TiB 증가 시키거나 감소 시킵니다 용량 풀 크기를 변경할 수 있습니다. 그러나 용량 풀 크기는 4 TiB 보다 작을 수 없습니다. 용량 풀 크기를 조정하면 구매한 Azure NetApp Files 용량을 변경합니다.

1. NetApp 계정 관리 블레이드에서 크기를 조정하려는 용량 풀을 클릭합니다. 
2. 상황에 맞는 메뉴를 표시하려면 용량 풀 이름을 마우스 오른쪽 단추로 클릭하거나, 용량 풀의 행의 끝에 있는 "..." 아이콘을 클릭합니다. 
3. 상황에 맞는 메뉴 옵션을 사용하여 용량 풀을 크기 조정하거나 삭제합니다.

## <a name="resize-a-volume"></a>볼륨 크기 조정

필요에 따라 볼륨의 크기를 변경할 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다.

1. NetApp 계정 관리 블레이드에서 **볼륨**을 클릭합니다. 
2. 상황에 맞는 메뉴를 표시하려면 크기를 조정하려는 볼륨 이름을 마우스 오른쪽 단추로 클릭하거나 볼륨의 행의 끝에 있는 "..." 아이콘을 클릭합니다.
3. 상황에 맞는 메뉴 옵션을 사용하여 볼륨을 크기 조정하거나 삭제합니다.

