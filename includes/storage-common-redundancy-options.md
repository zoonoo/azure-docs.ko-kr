---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/26/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: b965fea3d4f166b1a801dda7cafd8e4190790c68
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45739507"
---
저장소 계정의 복제 옵션으로는 다음과 같은 것이 있습니다.

* [LRS(로컬 중복 저장소)](../articles/storage/common/storage-redundancy-lrs.md): 간단하고 저렴한 복제 전략입니다. 데이터는 단일 저장소 배율 단위 내에서 복제됩니다.
* [ZRS(영역 중복 저장소)](../articles/storage/common/storage-redundancy-zrs.md): 높은 가용성과 지속성을 제공하는 복제입니다. 데이터는 세 개의 가용성 영역에서 동기적으로 복제됩니다. 
* [GRS(지역 중복 저장소)](../articles/storage/common/storage-redundancy-grs.md): 지역 전체가 사용 중단되는 일이 없도록 보호하는 지역 간 복제입니다.
* [RA-GRS(읽기 액세스 지역 중복 저장소)](../articles/storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage): 복제본에 대한 읽기 권한이 있는 지역 간 복제입니다.