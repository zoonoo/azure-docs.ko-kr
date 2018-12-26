---
title: 포함 파일
description: 포함 파일
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 00889717e0c22477b9933725bccc7de05c82bc4f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454497"
---
| **공급업체** | **장치 패밀리** | **펌웨어 버전** |
| --- | --- | --- |
|시스코 | ISR| IOS 15.1(미리 보기)|
|시스코 | ASA | ASA (*) RouteBased(IKEv2- BGP 없음), 9.8 미만 ASA  |
|시스코 | ASA | ASA RouteBased(IKEv2- BGP 없음), 9.8 이상 ASA  |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) 필수: NarrowAzureTrafficSelectors(UsePolicyBasedTrafficSelectors 옵션 사용 설정) 및 CustomAzurePolicies(IKE/IPsec)
>
