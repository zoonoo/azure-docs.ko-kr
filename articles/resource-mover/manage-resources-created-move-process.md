---
title: Azure 리소스 이동에서 VM 이동 프로세스 중에 만들어진 리소스를 관리 합니다.
description: Azure 리소스 이동에서 VM 이동 프로세스 중에 생성 된 리소스를 관리 하는 방법을 알아봅니다.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 4ff1905eb164d004af69ce5b0df3278bf3a46884
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/10/2020
ms.locfileid: "89670515"
---
# <a name="manage-resources-created-for-the-vm-move"></a>VM 이동에 대해 만든 리소스 관리

이 문서에서는 VM 이동 프로세스를 용이 하 게 하기 위해 [Azure 리소스](overview.md) 이동 자가 명시적으로 만든 리소스를 관리 하는 방법을 설명 합니다. 

여러 지역에서 Vm을 이동한 후에는 수동으로 정리 해야 하는 리소스에서 많은 리소스가 생성 됩니다.

## <a name="delete-resources-created-for-vm-move"></a>VM 이동을 위해 만든 리소스 삭제

이동 컬렉션을 수동으로 삭제 하 고 VM 이동에 대해 만들어진 리소스를 Site Recovery 합니다.

1. Vm을 이동한 원본 지역에서 리소스 그룹의 리소스를 검토 ```RegionMoveRG-<sourceregion>-<target-region>``` 합니다.
2. 이동 컬렉션의 VM 및 다른 모든 원본 리소스가 이동/삭제 되었는지 확인 합니다. 이렇게 하면 보류 중인 리소스를 사용 하지 않습니다.
2. 이러한 리소스를 삭제 합니다.

    - 이동 컬렉션 이름은 ```movecollection-<sourceregion>-<target-region>``` 입니다.
    - 캐시 저장소 계정 이름은입니다. ```resmovecache<guid>```
    - 자격 증명 모음 이름은 ```ResourceMove-<sourceregion>-<target-region>-GUID``` 입니다.

## <a name="next-steps"></a>다음 단계

리소스 이동 기를 사용 하 여 [VM을](tutorial-move-region-virtual-machines.md) 다른 지역으로 이동 해 보세요.