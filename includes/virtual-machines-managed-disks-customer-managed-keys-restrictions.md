---
title: 파일 포함
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 07/10/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c303527c7411ead585e70fc2e31db4dd2d35e996
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86259833"
---
- 2048 비트, 3072 비트 및 4096 비트 크기의 [소프트웨어 및 HSM RSA 키](../articles/key-vault/keys/about-keys.md) 만 지원 되며 다른 키 또는 크기는 지원 되지 않습니다.
    - [HSM](../articles/key-vault/keys/hsm-protected-keys.md) 키에는 Azure 주요 자격 증명 모음의 **프리미엄** 계층이 필요 합니다.
- 서버 쪽 암호화 및 고객 관리형 키를 사용하여 암호화된 사용자 지정 이미지에서 만든 디스크는 동일한 고객 관리형 키를 사용하여 암호화해야 하며 동일한 구독에 있어야 합니다.
- 서버 쪽 암호화 및 고객 관리형 키로 암호화된 디스크에서 만든 스냅샷은 동일한 고객 관리형 키를 사용하여 암호화해야 합니다.
- 고객 관리형 키와 관련된 모든 리소스(Azure Key Vault, 디스크 암호화 집합, VM, 디스크 및 스냅샷)는 동일한 구독 및 지역에 있어야 합니다.
- 고객 관리형 키를 사용하여 암호화된 디스크, 스냅샷 및 이미지는 다른 구독으로 이동할 수 없습니다.
- 또한 고객 관리형 키를 사용하여 암호화된 관리 디스크는 Azure Disk Encryption으로 암호화할 수 없습니다.
- 구독 당 지역 당 최대 50 개의 디스크 암호화 집합을 만들 수 있습니다.
- 공유 이미지 갤러리에서 고객 관리형 키를 사용하는 방법에 대한 자세한 정보는 [미리 보기: 이미지 암호화를 위해 고객 관리형 키 사용](../articles/virtual-machines/image-version-encryption.md)을 참조하세요.
