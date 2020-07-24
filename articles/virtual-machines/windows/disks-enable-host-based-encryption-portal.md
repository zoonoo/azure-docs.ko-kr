---
title: 호스트 Azure Portal 관리 디스크에서 암호화를 사용 하 여 종단 간 암호화 사용
description: 호스트의 암호화를 사용 하 여 Azure managed disks에서 종단 간 암호화를 사용 하도록 설정 합니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136225"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>호스트 Azure Portal에서 암호화를 사용 하 여 종단 간 암호화 사용

호스트에서 암호화를 사용 하도록 설정 하면 VM 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. 호스트의 암호화 및 기타 관리 되는 디스크 암호화 유형에 대 한 개념 정보는 호스트의 [암호화-VM 데이터에 대 한 종단 간 암호화](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)를 참조 하세요.

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)