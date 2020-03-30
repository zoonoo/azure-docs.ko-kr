---
title: 고객 관리 키를 통해 미사용 Azure IoT Hub 데이터 암호화| 마이크로 소프트 문서
description: IoT Hub를 위한 고객 관리 키로 미사용 데이터 암호화
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370579"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>IoT Hub를 위한 고객 관리 키로 미사용 데이터 암호화

IoT Hub는 AZURE IoT Hub에 대한 사용자 고유의 키 가져오기(BYOK)라고도 하는 CMK(고객 관리 키)를 사용하여 미사용 데이터의 암호화를 지원합니다. Azure IoT Hub는 미사용 및 전송 중 데이터의 암호화를 제공합니다. 기본적으로 IoT Hub는 Microsoft에서 관리하는 키를 사용하여 데이터를 암호화합니다. CMK 지원을 통해 고객은 이제 [Azure Key Vault를](https://azure.microsoft.com/services/key-vault/)사용하여 고객이 관리하는 키 암호화 키를 사용하여 미사용 데이터를 암호화할 수 있습니다.

이 기능을 사용하려면 미국 동부, 미국 서부 2, 미국 중남부 또는 미국 정부 중 하나에 새로운 IoT Hub(기본 또는 표준 계층)를 생성해야 합니다. 이 기능을 사용하려면 [Microsoft 지원을](https://azure.microsoft.com/support/create-ticket/)통해 문의하십시오. Microsoft 지원에 문의할 때 회사 이름과 멤버쉽 ID를 공유합니다.

## <a name="next-steps"></a>다음 단계

* [IoT 허브에 대해 자세히 알아보기](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
