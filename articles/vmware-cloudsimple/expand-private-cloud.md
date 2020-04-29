---
title: CloudSimple 사설 클라우드를 통해 Azure VMware 솔루션 확장
description: 기존 CloudSimple 사설 클라우드를 확장 하 여 기존 또는 새 클러스터에 용량을 추가 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77025302"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드 확장

CloudSimple은 사설 클라우드를 동적으로 확장할 수 있는 유연성을 제공 합니다. 더 작은 구성으로 시작한 다음 더 높은 용량이 필요한 경우 확장할 수 있습니다. 또는 현재 요구 사항에 따라 사설 클라우드를 만든 다음 소비 증가에 따라 확장할 수 있습니다.

사설 클라우드는 하나 이상의 vSphere 클러스터로 구성 됩니다. 각 클러스터에는 3 ~ 16 개의 노드가 있을 수 있습니다.  사설 클라우드를 확장할 때 기존 클러스터에 노드를 추가 하거나 새 클러스터를 만듭니다. 기존 클러스터를 확장 하려면 추가 노드가 기존 노드와 동일한 유형 (SKU) 이어야 합니다. 새 클러스터를 만드는 경우 노드의 형식이 다를 수 있습니다. 사설 클라우드 제한에 대 한 자세한 내용은 [Cloudsimple 사설 클라우드 개요](cloudsimple-private-cloud.md) 문서의 제한 섹션을 참조 하세요.

VCenter의 기본 **데이터 센터** 를 사용 하 여 사설 클라우드를 만듭니다.  각 데이터 센터는 최상위 관리 엔터티로 사용 됩니다.  새 클러스터의 경우 CloudSimple은 기존 데이터 센터에 추가 하거나 새 데이터 센터를 만들 수 있는 옵션을 제공 합니다.

새 클러스터 구성의 일부로 CloudSimple은 VMware 인프라를 구성 합니다.  설정에는 vSAN 디스크 그룹에 대 한 저장소 설정, VMware 고가용성 및 DRS (분산 리소스 스케줄러)가 포함 됩니다.

사설 클라우드는 여러 번 확장할 수 있습니다. 확장은 전체 노드 제한 내에 유지 되는 경우에만 수행할 수 있습니다. 사설 클라우드를 확장할 때마다 기존 클러스터에 추가 하거나 새로 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

사설 클라우드를 확장 하려면 먼저 노드를 프로 비전 해야 합니다.  노드에 프로 비전 하는 방법에 대 한 자세한 내용은 [CloudSimple을 사용한 VMware 솔루션에 대 한 노드 프로 비전-Azure](create-nodes.md) 문서를 참조 하세요.  새 클러스터를 만들려면 동일한 SKU의 사용 가능한 노드가 세 개 이상 있어야 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="expand-a-private-cloud"></a>프라이빗 클라우드 확장

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md)합니다.

2. **리소스** 페이지를 열고 확장할 사설 클라우드를 선택 합니다.

3. 요약 섹션에서 **확장**을 클릭 합니다.

    ![사설 클라우드 확장](media/resources-expand-private-cloud.png)

4. 기존 클러스터를 확장할지 아니면 새 vSphere 클러스터를 만들지를 선택 합니다. 변경 작업을 수행 하면 페이지의 요약 정보가 업데이트 됩니다.

    * 기존 클러스터를 확장 하려면 **기존 클러스터 확장**을 클릭 합니다. 확장할 클러스터를 선택 하 고 추가할 노드 수를 입력 합니다. 각 클러스터에는 최대 16 개의 노드가 있을 수 있습니다.
    * 새 클러스터를 추가 하려면 **새 클러스터 만들기**를 클릭 합니다. 클러스터의 이름을 입력합니다. 기존 데이터 센터를 선택 하거나 이름을 입력 하 여 새 데이터 센터를 만듭니다. 노드 형식을 선택 합니다. 새 vSphere 클러스터를 만들 때 다른 노드 유형을 선택할 수 있지만 기존 vSphere 클러스터를 확장할 때는 선택할 수 없습니다. 노드 수를 선택 합니다. 각 새 클러스터에는 세 개 이상의 노드가 있어야 합니다.

    ![사설 클라우드 확장-노드 추가](media/resources-expand-private-cloud-add-nodes.png)

5. **전송** 을 클릭 하 여 사설 클라우드를 확장 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [사설 클라우드에](cloudsimple-private-cloud.md) 대 한 자세한 정보