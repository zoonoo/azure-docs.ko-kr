---
title: 고객 관리 키를 통해 미사용 Azure 장치 프로 비전 서비스 데이터 암호화 | Microsoft Docs
description: 장치 프로 비전 서비스에 대 한 고객 관리 키를 사용 하 여 미사용 데이터 암호화
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675144"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>장치 프로 비전 서비스에 대 한 고객 관리 키를 사용 하 여 미사용 데이터 암호화

## <a name="overview"></a>개요

DPS (장치 프로 비전 서비스)는 CMK (고객이 관리 하는 키)를 사용 하 여 미사용 데이터의 암호화를 지원 합니다 (BYOK). DPS는 미사용 데이터 및 전송 중인 데이터의 암호화를 제공 합니다. 기본적으로 DPS는 Microsoft 관리 키를 사용 하 여 데이터를 암호화 합니다. CMK 지원을 통해 고객은 이제 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 사용 하 여 고객이 관리 하는 키 암호화 키를 사용 하 여 미사용 데이터를 암호화 하도록 선택할 수 있습니다.

이 기능을 사용 하려면 미국 동부, 미국 서 부 2 또는 남부 중부 미국 중 한 곳에 새 DPS를 만들어야 합니다. 이 기능을 사용해 보려면 [Microsoft 지원](https://azure.microsoft.com/support/create-ticket/)에 문의 하세요. Microsoft 지원에 문의할 때 회사 이름 및 구독 ID를 공유 하세요.

## <a name="next-steps"></a>다음 단계

* [장치 프로 비전 서비스에 대 한 자세한 정보](https://docs.microsoft.com/azure/iot-dps/)

* [Azure Key Vault에 대 한 자세한 정보](https://docs.microsoft.com/azure/key-vault/key-vault-overview)