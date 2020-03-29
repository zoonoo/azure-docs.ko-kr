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
ms.openlocfilehash: 2a348827b1c992e0ef0a4592cc0b9c5c0fa0ca19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372405"
---
## <a name="about-cognitive-services-encryption"></a>코그너티브 서비스 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 준수 [256비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용하여 암호화되고 해독됩니다. 암호화 및 암호 해독은 투명하므로 암호화 및 액세스가 관리됩니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 나 응용 프로그램을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 고객 관리 키를 지원하는 가격 책정 계층을 사용하는 경우 다음 이미지와 같이 [Azure 포털의](https://portal.azure.com)암호화 섹션에서 리소스에 대한 **암호화** 설정을 볼 수 있습니다.

![암호화 설정 보기](../articles/cognitive-services/media/cognitive-services-encryption/encryptionblade.png)

Microsoft에서 관리하는 암호화 키만 지원하는 구독의 경우 **암호화** 섹션이 없습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure 키 볼트를 사용할 수 있는 고객 관리 키

자신의 키로 구독을 관리하는 옵션도 있습니다. BYOK(사용자 고유의 키 가져오기)라고도 하는 CMK(고객 관리 키)는 액세스 제어를 생성, 회전, 비활성화 및 해지할 수 있는 유연성을 제공합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.
