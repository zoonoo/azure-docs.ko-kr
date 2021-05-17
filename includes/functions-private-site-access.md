---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 3c9679f3d66d58c7a6c847b54c84438c979ecd39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936868"
---
[Azure 프라이빗 엔드포인트](../articles/private-link/private-endpoint-overview.md)는 Azure Private Link를 기반으로 하는 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다.  프라이빗 엔드포인트는 가상 네트워크의 프라이빗 IP 주소를 사용하여 효과적으로 가상 네트워크에 서비스를 제공합니다.

[프리미엄](../articles/azure-functions/functions-premium-plan.md) 및 [App Service](../articles/azure-functions/dedicated-plan.md) 계획에서 호스팅되는 함수에 프라이빗 엔드포인트를 사용할 수 있습니다.

함수에 대한 인바운드 프라이빗 엔드포인트 연결을 만들 때 프라이빗 주소를 확인하려면 DNS 레코드도 필요합니다.  기본적으로 Azure Portal을 사용하여 프라이빗 엔드포인트를 만들 때 프라이빗 DNS 레코드가 생성됩니다.

자세히 알아보려면 [Web Apps용 프라이빗 엔드포인트 사용](../articles/app-service/networking/private-endpoint.md)을 참조하세요.