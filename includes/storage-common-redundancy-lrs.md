---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 09/23/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 8908ded31b96aac50db1fc25e92c2c0a8e960453
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219040"
---
LRS (로컬 중복 저장소)는 단일 데이터 센터 내에서 데이터를 세 번 복제 합니다. LRS는 지정 된 연도 동안 최소 99.999999999% (11 9)의 개체 내 구성을 제공 합니다. LRS는 가장 저렴한 복제 옵션이며 다른 옵션에 비해 내구성이 가장 낮습니다.

데이터 센터 수준 재해 (예: 화재 또는 홍수)가 발생 하면 LRS를 사용 하는 저장소 계정의 모든 복제본이 손실 되거나 복구할 수 없을 수 있습니다. 이러한 위험을 완화 하기 위해 Microsoft에서는 ZRS (영역 중복 저장소), GRS (지역 중복 저장소) 또는 GZRS (지역 중복 저장소)를 사용 하는 것이 좋습니다.

LRS 저장소 계정에 대 한 쓰기 요청은 3 개의 모든 복제본에 데이터가 기록 된 후에만 성공적으로 반환 됩니다.

다음 시나리오에서는 LRS를 사용할 수 있습니다.

* 데이터 손실이 발생하더라도 쉽게 재구성할 수 있는 데이터를 애플리케이션에 저장하는 경우 LRS를 선택할 수 있습니다.
* 일부 응용 프로그램은 데이터 관리 요구 사항으로 인해 국가/지역 내 에서만 데이터를 복제 하도록 제한 됩니다. 경우에 따라 GRS 계정에 대해 데이터가 복제 되는 쌍을 이루는 지역이 다른 국가/지역에 있을 수 있습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [Azure 지역](https://azure.microsoft.com/regions/)을 참조하세요.
