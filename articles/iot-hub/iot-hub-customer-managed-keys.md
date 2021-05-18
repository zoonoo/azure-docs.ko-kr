---
title: 고객 관리형 키를 통해 미사용 Azure IoT Hub 데이터 암호화 | Microsoft Docs
description: IoT Hub에 대해 고객 관리형 키를 사용하여 미사용 데이터 암호화
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142200"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>IoT Hub에 대해 고객 관리형 키를 사용하여 미사용 데이터 암호화

IoT Hub는 CMK(고객 관리형 키)(BYOK(Bring Your Own Key)라고도 함)를 사용하여 미사용 데이터의 암호화를 지원합니다. Azure IoT Hub는 데이터 센터에 기록된 미사용 및 전송 중인 데이터를 암호화하고, 액세스할 때 암호를 해독합니다. 기본값으로 IoT Hub는 Microsoft 관리형 키를 사용하여 미사용 데이터를 암호화합니다. CMK를 사용하면 기본 암호화 위에 다른 암호화 계층이 추가되며 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 통해 관리되는 키 암호화 키를 사용하여 미사용 데이터를 암호화하도록 선택할 수 있습니다. 이렇게 하면 유연성 있게 키를 만들고, 회전하고, 사용하지 않도록 설정하고, 철회할 수 있습니다. IoT Hub에 대해 BYOK가 구성된 경우 두 번째 보호 계층을 사용하면서 Azure Key Vault를 통해 암호화 키를 제어할 수 있는 이중 암호화도 제공합니다.

이 기능을 사용하려면 새 IoT Hub(기본 또는 표준 계층)를 만들어야 합니다. 기능을 사용해 보려면 [Microsoft 지원](https://azure.microsoft.com/support/create-ticket/)에 문의하세요. Microsoft 지원에 문의할 때 회사 이름과 구독 ID를 알려주세요.


## <a name="next-steps"></a>다음 단계

* [IoT Hub에 대한 자세한 정보](./about-iot-hub.md)

* [Azure Key Vault에 대해 자세히 알아보기](../key-vault/general/overview.md)