---
title: 미사용 Azure Portal 관리 디스크에서 이중 암호화 사용
description: Azure Portal를 사용 하 여 관리 디스크 데이터에 대해 미사용 이중 암호화를 사용 하도록 설정 합니다.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b244ef36e4e1a15327c053402b28ee2272fc71f0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136215"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure Portal-managed disks에서 휴지 상태의 이중 암호화 사용

Azure 디스크 저장소는 관리 디스크에 대 한 미사용 암호화를 지원 합니다. 휴지 상태의 이중 암호화 및 기타 관리 되는 디스크 암호화 유형에 대 한 개념 정보는 디스크 암호화 문서의 [휴지 상태의 이중 암호화](disk-encryption.md#double-encryption-at-rest) 섹션을 참조 하세요.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>다음 단계

- [Azure PowerShell-서버 쪽 암호화 관리 디스크를 사용 하 여 고객 관리 키를 사용 하도록 설정 합니다.](disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)