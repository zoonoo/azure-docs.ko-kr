---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 06/28/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5df0fe23183b53f13d6c6545e248724324434cde
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2019
ms.locfileid: "69029825"
---
스토리지 계정의 복제 옵션으로는 다음과 같은 것이 있습니다.

* [LRS (로컬 중복 저장소)](../articles/storage/common/storage-redundancy-lrs.md): 단순 하 고 저렴 한 복제 전략 데이터는 주 지역 내에서 동기적으로 세 번 복제 됩니다.
* [ZRS(영역 중복 스토리지)](../articles/storage/common/storage-redundancy-zrs.md): 고가용성이 필요한 시나리오에 대 한 복제. 데이터는 주 지역에 있는 세 개의 Azure 가용성 영역에서 동기적으로 복제 됩니다.
* [GRS(지역 중복 스토리지)](../articles/storage/common/storage-redundancy-grs.md): 지역 가동 중단 으로부터 보호 하는 지역 간 복제 데이터는 주 지역에서 동기적으로 세 번 복제 된 다음 보조 지역에 비동기적으로 복제 됩니다. 보조 지역의 데이터에 대 한 읽기 액세스의 경우 읽기 액세스 지역 중복 저장소 (RA-GRS)를 사용 하도록 설정 합니다.
* [GZRS (지역 중복 저장소) (미리 보기)](../articles/storage/common/storage-redundancy-gzrs.md): 고가용성 및 최대 내구성이 모두 필요한 시나리오에 대 한 복제. 데이터는 주 지역에 있는 세 개의 Azure 가용성 영역에서 동기적으로 복제 된 다음 보조 지역에 비동기적으로 복제 됩니다. 보조 지역의 데이터에 대 한 읽기 액세스의 경우 읽기 액세스 지리적 영역 중복 저장소 (RA-GZRS)를 사용 하도록 설정 합니다.
