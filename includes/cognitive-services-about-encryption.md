---
title: 파일 포함
description: 포함 파일
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372405"
---
## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256 비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용 하 여 암호화 및 암호 해독 됩니다. 암호화 및 암호 해독은 투명 합니다. 즉, 암호화 및 액세스를 관리 합니다. 데이터는 기본적으로 안전 하며 암호화를 활용 하기 위해 코드 또는 응용 프로그램을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리 하는 암호화 키를 사용 합니다. 고객 관리 키를 지 원하는 가격 책정 계층을 사용 하는 경우 다음 그림에 표시 된 것 처럼 [Azure Portal](https://portal.azure.com)의 **암호화** 섹션에서 리소스에 대 한 암호화 설정을 확인할 수 있습니다.

![암호화 설정 보기](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Microsoft에서 관리 하는 암호화 키만 지 원하는 구독의 경우에는 **암호화** 섹션이 표시 되지 않습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

사용자 고유의 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. BYOK (사용자 고유 키 사용) 라고도 하는 CMK (고객 관리 키)는 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호 하는 데 사용 되는 암호화 키를 감사할 수도 있습니다.
