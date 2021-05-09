---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "89069915"
---
## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화 및 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 자동으로 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK(고객 관리형 키)라고 하는 자체 키를 사용하여 구독을 관리하는 옵션도 있습니다. CMK를 사용하면 훨씬 더 유연하게 액세스 제어를 만들고, 순환하고, 사용하지 않도록 설정하고, 철회할 수 있습니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성된 경우 이중 암호화가 제공되어 두 번째 보안 계층을 제공하는 동시에 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.