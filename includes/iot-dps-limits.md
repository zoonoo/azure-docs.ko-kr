---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 610f5fcf80db8ffa0c7207d459b98087cbb4773b
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2019
ms.locfileid: "66455192"
---
다음 표에서 Azure IoT Hub Device Provisioning Service 리소스에 적용 되는 제한을 나열 합니다.

| Resource | 제한 |
| --- | --- |
| Azure 구독 당 서비스를 프로 비전 하는 최대 장치 | 10 |
| 최대 등록(enrollment) 수 | 1,000,000 |
| 최대 등록 수 | 1,000,000 |
| 최대 등록(enrollment) 그룹 수 | 100 |
| 최대 CA 수 | 25 |
| 메시지의 최대 크기 | 96KB|

> [!NOTE]
> 구독에는 인스턴스 수를 늘리려면에 문의 [Microsoft 지원](https://azure.microsoft.com/support/options/)합니다.

> [!NOTE]
> 등록 및 프로 비전 서비스에서 등록 수를 늘리려면에 문의 [Microsoft 지원](https://azure.microsoft.com/support/options/)합니다.

다음 할당량이 초과 되 면 Device Provisioning Service가 요청을 제한 합니다.

| 제한 | 단위당 값 |
| --- | --- |
| 작업 | 200/분/서비스 |
| 디바이스 등록 | 200/분/서비스 |
| 디바이스 폴링 작업 | 5/10 초/장치 |
