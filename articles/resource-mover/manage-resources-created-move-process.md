---
title: Azure Resource Mover에서 VM 이동 프로세스 중에 만들어진 리소스를 관리합니다.
description: Azure Resource Mover에서 VM 이동 프로세스 중에 생성된 리소스를 관리하는 방법을 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: d3c4c4e86e2461ea1d05af284e724a5a2991f040
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727042"
---
# <a name="manage-resources-created-for-the-vm-move"></a>VM 이동을 위해 만든 리소스 관리

이 문서에서는 VM 이동 프로세스를 용이하게 하기 위해 [Azure Resource Mover](overview.md)가 명시적으로 만든 리소스를 관리하는 방법을 설명합니다. 

여러 지역에 걸쳐 VM을 이동한 후에는 Resource Mover에서 생성된 많은 리소스를 수동으로 삭제해야 합니다.

## <a name="delete-resources-created-for-vm-move"></a>VM 이동을 위해 만든 리소스 삭제

이동 컬렉션, VM 이동을 위해 생성된 리소스를 Site Recovery 리소스를 수동으로 삭제합니다.

1. 리소스 그룹 ```ResourceMoverRG-<sourceregion>-<target-region>-<metadataRegionShortName>```의 리소스를 검토합니다.
2. 이동 컬렉션의 VM 및 다른 모든 원본 리소스가 이동/삭제되었는지 확인합니다. 이러한 리소스를 사용하는 보류 중인 리소스가 남아 있지 않도록 확인하기 위한 절차입니다.
2. 해당 리소스를 삭제합니다.

    - 이동 컬렉션 이름은 ```movecollection-<sourceregion>-<target-region>-<metadata-region>```입니다.
    - 캐시 스토리지 계정 이름은 ```resmovecache<guid>```입니다.
    - 자격 증명 모음 이름은 ```ResourceMove-<sourceregion>-<target-region>-GUID```입니다.

## <a name="next-steps"></a>다음 단계

Resource Mover를 사용하여 다른 지역으로 [VM 이동](tutorial-move-region-virtual-machines.md)을 시도합니다.
