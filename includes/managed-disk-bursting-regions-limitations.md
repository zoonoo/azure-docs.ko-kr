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
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102178362"
---
- 512GiB 미만의 프리미엄 SSD에서는 주문형 버스팅을 사용하도록 설정할 수 없습니다. 512GiB 미만의 프리미엄 SSD는 항상 크레딧 기반 버스팅을 사용합니다.
- 주문형 버스팅은 프리미엄 SSD에서만 지원됩니다. 주문형 버스팅을 사용하도록 설정된 프리미엄 SSD가 다른 디스크 유형으로 전환되면 디스크 버스팅을 사용할 수 없습니다.
- 주문형 버스팅은 성능 계층이 변경될 때 자동으로 사용 중지되지 않습니다. 성능 계층을 변경하되 디스크 버스팅을 유지하지 않으려면 디스크 버스팅을 사용하지 않도록 설정해야 합니다.
- 주문형 버스팅은 디스크가 VM에서 분리되거나 VM이 중지된 경우에만 사용하도록 설정할 수 있습니다. 주문형 버스팅은 사용하도록 설정한 후 12시간이 지나야 사용하지 않도록 설정할 수 있습니다.