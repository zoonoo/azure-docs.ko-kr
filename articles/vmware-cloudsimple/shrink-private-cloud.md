---
title: CloudSimple 사설 클라우드에서 Azure VMware 솔루션 축소
description: CloudSimple 사설 클라우드를 축소 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1ae2f87a3719853f4a91cb8ba801be6d578597d3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825676"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드 축소

CloudSimple은 사설 클라우드를 동적으로 축소 하는 유연성을 제공 합니다.  사설 클라우드는 하나 이상의 vSphere 클러스터로 구성 됩니다. 각 클러스터에는 3 ~ 16 개의 노드가 있을 수 있습니다. 사설 클라우드를 축소 하는 경우 기존 클러스터에서 노드를 제거 하거나 전체 클러스터를 삭제 합니다. 

## <a name="before-you-begin"></a>시작하기 전에

사설 클라우드의 축소에 대해 다음 조건을 충족 해야 합니다.  사설 클라우드를 만들 때 만든 관리 클러스터 (첫 번째 클러스터)를 삭제할 수 없습니다.

* VSphere 클러스터에는 3 개의 노드가 있어야 합니다.  노드가 세 개만 있는 클러스터는 축소할 수 없습니다.
* 사용 된 총 저장소는 클러스터 축소 후 총 용량을 초과 하면 안 됩니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="shrink-a-private-cloud"></a>프라이빗 클라우드 축소

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.

2. **리소스** 페이지를 엽니다.

3. 축소할 사설 클라우드를 클릭 합니다.

4. 요약 페이지에서 **축소**를 클릭 합니다.

    ![사설 클라우드 축소](media/shrink-private-cloud.png)

5. 축소 하거나 삭제 하려는 클러스터를 선택 합니다. 

    ![사설 클라우드 축소-클러스터 선택](media/shrink-private-cloud-select-cluster.png)

6. **단일 노드 제거** 또는 **전체 클러스터 삭제**를 선택 합니다. 

7. 클러스터 용량 확인

8. **전송** 을 클릭 하 여 사설 클라우드를 축소 합니다.

사설 클라우드의 축소를 시작 합니다.  작업의 진행률을 모니터링할 수 있습니다.  축소 프로세스는 데이터에 따라 몇 시간이 걸릴 수 있으며이는 vSAN에서 resynced 해야 합니다.

> [!NOTE]
> 데이터 센터에서 마지막 또는 유일한 클러스터를 삭제 하 여 사설 클라우드를 축소 하는 경우에는 데이터 센터가 삭제 되지 않습니다.  


## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* [사설 클라우드에](cloudsimple-private-cloud.md) 대 한 자세한 정보
