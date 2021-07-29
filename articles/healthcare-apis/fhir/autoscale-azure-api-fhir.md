---
title: Azure API for FHIR 자동 크기 조정
description: 이 문서에서는 Azure API for FHIR 자동 크기 조정 기능에 대해 설명합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/26/2021
ms.author: zxue
ms.openlocfilehash: cc5fd2dd91c1edc39886938d91ebb54bcbc3ef0d
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713387"
---
# <a name="autoscale-for-azure-api-for-fhir"></a>Azure API for FHIR 자동 크기 조정 

Azure API for FHIR 관리형 서비스로 사용하면 고객이 FHIR 규격 의료 데이터를 유지하여 서비스 API를 통해 안전하게 교환할 수 있습니다. 다양한 트랜잭션 워크로드를 수용하기 위해 고객은 수동 크기 조정 또는 자동 크기 조정을 사용할 수 있습니다.

## <a name="what-is-autoscale"></a>자동 크기 조정이란?

기본적으로 Azure API for FHIR 수동 크기 조정으로 설정됩니다. 이 옵션은 트랜잭션 워크로드가 알려져 있고 일관적인 경우에 잘 작동합니다. 고객은 포털을 통해 처리량을 `RU/s` 최대 10,000개까지 조정하고 요청을 제출하여 제한을 늘릴 수 있습니다. 

자동 크기 조정을 사용하면 고객이 다양한 워크로드를 실행할 수 있으며 수동 조정 없이 처리량이 `RU/s` 자동으로 확장 및 축소됩니다.

## <a name="how-to-enable-autoscale"></a>자동 크기 조정을 사용하도록 설정하는 방법

자동 크기 조정 기능을 사용하도록 설정하려면 일회성 지원 티켓을 만들어 요청할 수 있습니다. Microsoft 지원 팀은 지원 우선 순위에 따라 자동 크기 조정 기능을 사용하도록 설정합니다.

> [!NOTE]
> 자동 크기 조정 기능은 Azure Portal 사용할 수 없습니다.

## <a name="how-to-adjust-the-maximum-throughput-rus"></a>최대 처리량 RU/s를 조정하는 방법

자동 크기 조정을 사용하도록 설정하면 시스템에서 초기 값을 계산하고 `Tmax` 설정합니다. 확장성은 최대 처리량 값 또는 에 의해 `RU/s` `Tmax` 제어되며 `0.1 *Tmax` (또는 10% ) 및 사이에서 `Tmax` `Tmax RU/s` 실행됩니다. 

최대 또는 값을 늘리고 `RU/s` `Tmax` 서비스에서 지원하는 만큼 높아질 수 있습니다. 서비스가 사용 중이면 처리량이 `RU/s` `Tmax` 값으로 확장됩니다. 서비스가 유휴 상태이면 처리량이 `RU/s` 10% `Tmax` 값으로 축소됩니다.
 
최대값 또는 값을 줄일 수도 `RU/s` `Tmax` 있습니다. 최대 를 낮추면 `RU/s` 설정할 수 있는 최소값은 이며 `MAX (4000, highest max RU/s ever provisioned / 10, current storage in GB * 400)` 가장 가까운 1000으로 `RU/s` 반올림됩니다.

* **예제 1:** 1GB 데이터가 있고 프로비전된 가장 높은 `RU/s` 데이터가 10,000개입니다. 최소값은 Max (**4000**, 10,000/10, 1x400) = 4000입니다. 첫 번째 숫자 **4000이** 사용됩니다.
* **예제 2:** 20GB 데이터가 있고 프로비전된 가장 높은 `RU/s` 데이터가 100,000개입니다. 최소값은 Max(4000, **100,000/10**, 20x400) = 10,000입니다. 두 번째 **숫자인 100,000/10 =10,000이** 사용됩니다.
* **예제 3:** 80GB 데이터가 있고 프로비전된 가장 높은 RU/s는 300,000입니다. 최소값은 Max(4000, 300,000/10, **80x400**) = 32,000입니다. 세 번째 **숫자인 80x400=32,000이** 사용됩니다.

`RU/s` `Tmax` 유효한 숫자이고 10,000보다 크지 않은 경우 포털을 통해 최대값 또는 값을 조정할 수 `RU/s` 있습니다. 지원 티켓을 만들어 `Tmax` 10,000보다 큰 값을 요청할 수 있습니다.

## <a name="what-is-the-cost-impact-of-autoscale"></a>자동 크기 조정의 비용 영향은 무엇인가요?

자동 크기 조정 기능은 프로비전된 처리량 단위를 자동으로 관리하기 때문에 비용이 발생합니다. 이 비용 증가는 스토리지 및 런타임 비용에 적용되지 않습니다. 가격 책정에 대한 자세한 내용은 [Azure API for FHIR 가격 책정을 참조하세요.](https://azure.microsoft.com/pricing/details/azure-api-for-fhir/)

>[!div class="nextstepaction"]
>[Azure API for FHIR 정보](overview.md)
