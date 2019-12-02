---
title: 포함 파일
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: f846d75833b3a796e24fd23c5f841ea24a8d1876
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2019
ms.locfileid: "74665912"
---
Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 저장소 계정 수준에서 고객이 관리 하는 키를 제공할 수 있습니다.

고객 관리 키는 Azure Key Vault에 저장 되어야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. 스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. 암호화 및 키 관리 Azure Storage에 대 한 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](../articles/storage/common/storage-service-encryption.md)를 참조 하세요. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../articles/key-vault/key-vault-overview.md)
