---
title: 고객 관리형 키를 통해 미사용 Azure 디바이스 프로비저닝 서비스 데이터 암호화 | Microsoft Docs
description: 디바이스 프로비저닝 서비스에 대해 고객 관리형 키를 통해 미사용 데이터 암호화
author: wesmc7777
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: wesmc
ms.openlocfilehash: 1cc8afadb61d1684b16893d0eff23af227828eb1
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2021
ms.locfileid: "114727086"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>디바이스 프로비저닝 서비스에 대해 고객 관리형 키를 통해 미사용 데이터 암호화

## <a name="overview"></a>개요

DPS (디바이스 프로비저닝 서비스)는 CMK(고객 관리형 키)(BYOK(Bring Your Own Key)라고도 함)를 사용하여 미사용 데이터의 암호화를 지원합니다. DPS는 데이터 센터에 기록된 미사용 및 전송 중인 데이터를 암호화하고, 액세스할 때 이를 해독합니다. 기본값으로 DPS는 Microsoft 관리 키를 사용하여 미사용 데이터를 암호화합니다. CMK를 사용하면 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 통해 관리되는 키 암호화 키를 사용하여 미사용 데이터를 암호화 하도록 선택하여 기본 플랫폼 암호화 위에 추가 암호화 계층을 사용할 수 있습니다. 이를 통해 키를 만들고, 회전하고, 비활성화하고, 취소할 수 있는 유연성을 가질 수 있습니다. DPS에 CMK가 구성된다면, 데이터를 보호하는 두 계층의 보호에 이중 암호화를 사용하도록 설정하는 것을 의미합니다. 

이 기능을 사용하려면 새 DPS를 만들어야 합니다. 이 기능을 사용해 보려면 [Microsoft 고객 지원팀](https://azure.microsoft.com/support/create-ticket/)에 문의 하세요. Microsoft 지원에 문의할 때 회사 이름 및 구독 ID를 알려주세요.


## <a name="next-steps"></a>다음 단계

* [Device Provisioning Service에 대해 자세히 알아보기](./index.yml)

* [Azure Key Vault에 대해 자세히 알아보기](../key-vault/general/overview.md)