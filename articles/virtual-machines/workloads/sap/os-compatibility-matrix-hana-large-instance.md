---
title: SAP HANA (대량 인스턴스)에 대 한 운영 체제 호환성 매트릭스 | Microsoft Docs
description: 호환성 매트릭스는 서로 다른 하드웨어 유형 (많은 인스턴스)을 사용 하는 서로 다른 운영 체제 버전의 호환성을 나타냅니다.
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675633"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>HANA Large Instances에 대해 호환 되는 운영 체제

## <a name="hana-large-instance-type-i"></a>HANA Large Instance 유형 I     
  | 운영 체제 | 가용성        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | 더 이상 제공되지 않음 | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | 사용 가능           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>영구적 메모리 Sku
  | 운영 체제 | 가용성 | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | 사용 가능    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>HANA Large Instance 유형 II     
  |  운영 체제       | 가용성        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | 더 이상 제공되지 않음 | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | 사용 가능           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>관련 문서

- [사용 가능한 sku](hana-available-skus.md) 에 대 한 자세한 내용을 보려면
- [운영 체제 업그레이드](os-upgrade-hana-large-instance.md) 에 대해 알고 있어야 합니다.
  

  
  
