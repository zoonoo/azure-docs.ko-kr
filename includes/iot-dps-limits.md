---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: fb77682bce0d9faef8f9ac8197afd8751560d515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224562"
---
다음 표에는 Azure IoT Hub Device Provisioning Service 리소스에 적용되는 제한이 나열되어 있습니다.

| 리소스 | 제한 |
| --- | --- |
| Azure 구독당 최대 디바이스 프로비저닝 서비스 | 10 |
| 최대 등록(enrollment) 수 | 1,000,000 |
| 최대 등록 수 | 1,000,000 |
| 최대 등록(enrollment) 그룹 수 | 100 |
| 최대 CA 수 | 25 |
| 연결된 최대 IoT 허브 수 | 50 |
| 최대 메시지 크기 | 96KB|


> [!NOTE]
> 구독의 인스턴스 수를 늘리려면 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

> [!NOTE]
> 프로비저닝 서비스의 등록 수를 늘리려면 [Microsoft 지원](https://azure.microsoft.com/support/options/)에 문의하세요.

다음 할당량이 초과되면 디바이스 프로비저닝 서비스 요청을 제한합니다.

| 제한 | 단위당 값 |
| --- | --- |
| 작업 | 200/분/서비스 |
| 디바이스 등록 | 200/분/서비스 |
| 디바이스 폴링 작업 | 5/10초/디바이스 |
