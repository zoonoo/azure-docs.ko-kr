---
title: CloudSimple 사설 클라우드를 통해 Azure의 VMware 솔루션 축소
description: CloudSimple 사설 클라우드를 축소 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544518"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드를 축소 합니다.

CloudSimple 사설 클라우드를 동적으로 축소할 수 있는 유연성을 제공 합니다.  사설 클라우드 하나 이상의 vSphere 클러스터로 구성 됩니다. 각 클러스터는 3 ~ 16 노드를 가질 수 있습니다. 사설 클라우드를 축소 하는 동안 기존 클러스터에서 노드를 제거 하거나 전체 클러스터를 삭제 합니다. 

## <a name="before-you-begin"></a>시작하기 전에

사설 클라우드의 축소에 대 한 다음 조건이 충족 되어야 합니다.  관리 클러스터 (첫 번째 클러스터) 만든 사설 클라우드를 만들 때 삭제할 수 없습니다.

* VSphere 클러스터에 3 개의 노드가 있어야 합니다.  3 개의 노드가 있는 클러스터는 축소할 수 없습니다.
* 사용 된 총 저장소 클러스터의 축소 후 총 용량을 넘지 않아야 합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="shrink-a-private-cloud"></a>사설 클라우드를 축소 합니다. 

1. [CloudSimple 포털에 액세스할](access-cloudsimple-portal.md)합니다.

2. 엽니다는 **리소스** 페이지입니다.

3. 사설 클라우드 작게 축소 하려면 클릭

4. 요약 페이지에서 클릭 **축소**합니다.

    ![사설 클라우드를 축소 합니다.](media/shrink-private-cloud.png)

5. 축소 하거나 삭제 하려는 클러스터를 선택 합니다. 

    ![사설 클라우드-선택 클러스터를 축소 합니다.](media/shrink-private-cloud-select-cluster.png)

6. 선택 **하나의 노드를 제거할** 하거나 **전체 클러스터를 삭제**합니다. 

7. 클러스터 용량 확인

8. 클릭 **제출** 사설 클라우드를 축소 합니다.

사설 클라우드의 축소 시작합니다.  작업 진행률을 모니터링할 수 있습니다.  축소는 vSAN에 재 동기화 되어야 하는 데이터에 따라 몇 시간이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware Vm 사용](quickstart-create-vmware-virtual-machine.md)
* 자세한 내용은 [사설 클라우드](cloudsimple-private-cloud.md)