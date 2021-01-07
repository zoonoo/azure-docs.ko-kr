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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "77157224"
---
스토리지 계정의 중복 옵션으로는 다음과 같은 것이 있습니다.

* LRS(로컬 중복 스토리지): 간단하고 저렴한 중복도 전략입니다. 데이터는 주 지역 내에서 동기적으로 세 번 복사됩니다.
* ZRS(영역 중복 스토리지): 고가용성이 필요한 시나리오를 위한 중복도. 데이터는 주 지역에 있는 3개의 Azure 가용성 영역에서 동기적으로 복사됩니다.
* GRS(지역 중복 스토리지): 지역 간 중복도를 통해 지역 중단으로부터 보호합니다. 데이터는 주 지역에서 동기적으로 세 번 복사된 다음, 보조 지역에 비동기적으로 복사됩니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GRS(읽기 액세스 지역 중복 스토리지)를 사용합니다.
* GZRS(지역 영역 중복 스토리지)(미리 보기): 고가용성 및 최대 내구성이 모두 필요한 시나리오를 위한 중복도 데이터는 주 지역의 3개의 Azure 가용성 영역에서 동기적으로 복사된 다음, 보조 지역에 비동기적으로 복사됩니다. 보조 지역에 대한 읽기 액세스의 경우 RA-GZRS(읽기 액세스 지역 영역 중복 스토리지)를 사용합니다.

Azure Storage의 중복 옵션에 대한 자세한 내용은 [Azure Storage 중복성](../articles/storage/common/storage-redundancy.md)을 참조하세요.
