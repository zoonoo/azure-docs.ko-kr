---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 01/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 5658b68081fae8dab528030f7fc1cd6fe4935730
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "102178362"
---
- 512 GiB 미만의 프리미엄 SSD에서 주문형 버스트를 사용 하도록 설정할 수 없습니다. Premium Ssd에서 512 GiB는 항상 신용 기반 버스트를 사용 합니다.
- 주문형 버스트는 프리미엄 Ssd 에서만 지원 됩니다. 주문형 버스트를 사용 하는 프리미엄 SSD가 다른 디스크 유형으로 전환 된 경우 디스크 버스트를 사용할 수 없습니다.
- 주문형 버스트는 성능 계층이 변경 될 때 자동으로 사용 하지 않도록 설정 됩니다. 성능 계층을 변경 하 되 디스크 버스트를 유지 하지 않으려면 사용 하지 않도록 설정 해야 합니다.
- 주문형 버스트는 디스크가 VM에서 분리 되거나 VM이 중지 된 경우에만 사용할 수 있습니다. 주문형 버스트를 사용 하도록 설정한 후 12 시간 동안 사용 하지 않도록 설정할 수 있습니다.