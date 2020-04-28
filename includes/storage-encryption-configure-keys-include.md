---
title: 파일 포함
description: 포함 파일
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: 694501fdaaaa92e898f4973838d86343e29144e3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "74895279"
---
Azure Storage는 미사용 저장소 계정의 모든 데이터를 암호화 합니다. 기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대 한 추가 제어를 위해 blob 및 파일 데이터의 암호화에 사용할 고객 관리 키를 제공할 수 있습니다.

고객 관리 키는 Azure Key Vault에 저장 되어야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성할 수 있습니다. 스토리지 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. 암호화 및 키 관리 Azure Storage에 대 한 자세한 내용은 [미사용 데이터에 대 한 Azure Storage 암호화](../articles/storage/common/storage-service-encryption.md)를 참조 하세요. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 항목](../articles/key-vault/key-vault-overview.md) 을 참조 하세요.
