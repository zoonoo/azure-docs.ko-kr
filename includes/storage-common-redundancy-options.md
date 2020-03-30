---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 01/14/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 73c2b742ede21a4e86d717d994f8ebc4f16389c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77157224"
---
저장소 계정에 대한 중복 옵션은 다음과 같습니다.

* LRS(로컬 중복 저장소): 간단하고 저렴한 중복 전략입니다. 데이터는 기본 영역 내에서 동기적으로 세 번 복사됩니다.
* ZRS(영역 중복 저장소): 고가용성이 필요한 시나리오에 대한 중복성입니다. 데이터는 기본 리전의 세 Azure 가용성 영역에서 동기적으로 복사됩니다.
* 지리적 중복 저장소(GRS): 지역 간 중복성으로 지역 정전으로부터 보호합니다. 데이터는 기본 리전에서 동기적으로 세 번 복사된 다음 보조 영역에 비동기적으로 복사됩니다. 보조 지역의 데이터에 대한 읽기 액세스를 위해 읽기 액세스 지리적 중복 저장소(RA-GRS)를 사용하도록 설정합니다.
* 지오 존 중복 스토리지(GZRS) (미리 보기): 고가용성과 최대 내구성이 모두 필요한 시나리오에 대한 중복성. 데이터는 기본 지역의 세 Azure 가용성 영역에서 동기적으로 복사된 다음 보조 지역으로 비동기적으로 복사됩니다. 보조 지역의 데이터에 대한 읽기 액세스를 위해 읽기 액세스 지리적 영역 중복 저장소(RA-GZRS)를 사용하도록 설정합니다.

Azure 저장소의 중복 옵션에 대한 자세한 내용은 [Azure 저장소 중복을](../articles/storage/common/storage-redundancy.md)참조하십시오.
