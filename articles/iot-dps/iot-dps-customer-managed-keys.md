---
title: 고객 관리 키를 통해 미사용 Azure 장치 프로비저닝 서비스 데이터 암호화| 마이크로 소프트 문서
description: 장치 프로비저닝 서비스에 대한 고객 관리 키로 미사용 데이터 암호화
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675144"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>장치 프로비저닝 서비스에 대한 고객 관리 키로 미사용 데이터 암호화

## <a name="overview"></a>개요

장치 프로비저닝 서비스(DPS)는 BYOK(사용자 고유키 가져오기)라고도 하는 CMK(고객 관리 키)를 사용하여 미사용 데이터의 암호화를 지원합니다. DPS는 미사용 및 전송 중 데이터의 암호화를 제공합니다. 기본적으로 DPS는 Microsoft에서 관리하는 키를 사용하여 데이터를 암호화합니다. CMK 지원을 통해 고객은 이제 [Azure Key Vault를](https://azure.microsoft.com/services/key-vault/)사용하여 고객이 관리하는 키 암호화 키를 사용하여 미사용 데이터를 암호화할 수 있습니다.

이 기능을 사용하려면 미국 동부, 미국 서부 2 또는 미국 중남부 지역 중 하나에 새 DPS를 생성해야 합니다. 이 기능을 사용하려면 [Microsoft 지원을](https://azure.microsoft.com/support/create-ticket/)통해 문의하십시오. Microsoft 지원에 문의할 때 회사 이름과 멤버쉽 ID를 공유합니다.

## <a name="next-steps"></a>다음 단계

* [장치 프로비저닝 서비스에 대해 자세히 알아보기](https://docs.microsoft.com/azure/iot-dps/)

* [Azure 키 볼트에 대해 자세히 알아보기](https://docs.microsoft.com/azure/key-vault/key-vault-overview)