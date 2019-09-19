---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 09/18/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 0f528117b19a3f26b964dee7ae453fc184c8dddb
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123058"
---
Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다.  이러한 가상 머신 크기는 규정 준수 및 규정 요구 사항과 같은 요소를 포함하는 작업에 대해 다른 고객으로부터 높은 수준의 격리가 필요한 작업에 가장 적합합니다.  고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.

격리 크기를 사용하면 가상 머신이 특정 서버 인스턴스에서 하나만 실행되도록 보장합니다.  현재 격리 가상 머신 제품에는 다음이 포함됩니다.
* Standard_E64is_v3
* Standard_E64i_v3
* Standard_M128ms
* Standard_GS5
* Standard_G5
* Standard_DS15_v2
* Standard_D15_v2
* Standard_F72s_v2

[여기](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-memory)에서 사용 가능한 각 격리 된 크기에 대해 자세히 알아볼 수 있습니다.