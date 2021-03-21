---
title: 파일 포함
description: 파일 포함
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7151c110fd50f7485aa0b130832aace4f3143ad9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101750975"
---
- 이 기능은 현재 premium Ssd에 대해서만 지원 됩니다.
- 디스크의 계층을 변경 하려면 먼저 VM의 할당을 취소 하거나 실행 중인 VM에서 디스크를 분리 해야 합니다.
- P60, P70 및 P80 성능 계층은 4096 GiB 보다 큰 디스크 에서만 사용할 수 있습니다.
- 디스크의 성능 계층은 12 시간 마다 한 번만 다운 그레이드할 수 있습니다.

## <a name="change-performance-tier-without-downtime-preview"></a>가동 중지 시간 없이 성능 계층 변경 (미리 보기)

일반적으로 VM의 할당을 취소 하거나 디스크를 분리 하 여 성능 계층을 변경 해야 합니다. 그러나이 미리 보기 기능을 사용 하도록 설정 하면 VM의 할당을 취소 하거나 디스크를 분리 하 여 계층을 변경할 필요가 없습니다. [여기](https://aka.ms/liveperftiersignup)에서 미리 보기에 등록할 수 있습니다.

미리 보기에는 다음과 같은 제한 사항이 있습니다.
- EastUS2EUAP 지역 에서만 사용할 수 있습니다.
- 현재 공유 디스크에 사용할 수 없음
- `2020-12-01`가동 중지 시간 없이 성능 계층을 변경 하려면 API와 함께 Azure Resource Manager 템플릿을 사용 해야 합니다.