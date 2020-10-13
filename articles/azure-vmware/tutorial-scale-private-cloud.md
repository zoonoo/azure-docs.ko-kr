---
title: 자습서 - 프라이빗 클라우드 크기 조정
description: 이 자습서에서는 Azure Portal을 사용하여 Azure VMware Solution 프라이빗 클라우드의 크기를 조정합니다.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: e1226eb98607a34869bda1f998c7cecea2e50919
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91254417"
---
# <a name="tutorial-scale-an-azure-vmware-solution-private-cloud"></a>자습서: Azure VMware Solution 프라이빗 클라우드 크기 조정

Azure VMware Solution 프라이빗 클라우드 환경을 최대한 활용하려면 계획된 워크로드에 필요한 사항을 반영하도록 클러스터와 호스트 크기를 조정합니다. 애플리케이션 워크로드에 필요한 만큼 클러스터 수와 프라이빗 클라우드의 호스트 수를 스케일링할 수 있습니다. 특정 서비스에 대한 성능 및 가용성 제한은 Azure VMware Solution 프라이빗 클라우드 환경 내에서 사례별로 해결해야 합니다. 클러스터 및 호스트 제한은 [프라이빗 클라우드 개념](concepts-private-clouds-clusters.md) 문서에 제공됩니다.

이 자습서에서는 Azure Portal을 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 기존 프라이빗 클라우드에 클러스터 추가
> * 기존 클러스터에 호스트 추가

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 프라이빗 클라우드가 필요합니다. 프라이빗 클라우드를 아직 만들지 않은 경우 [프라이빗 클라우드 만들기 자습서](tutorial-create-private-cloud.md)를 사용하여 프라이빗 클라우드를 만들고 Azure에서 VMware 프라이빗 클라우드에 대한 네트워킹을 구성하여 필요한 가상 네트워크를 설정합니다.

## <a name="add-a-new-cluster"></a>새 클러스터 추가

1. 기존 프라이빗 클라우드의 개요 페이지의 **관리**에서 **프라이빗 클라우드 스케일링**을 선택합니다. 다음으로 **+ 클러스터 추가**를 선택합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss2-select-add-cluster.png" alt-text="클러스터 추가 선택" border="true":::

1. **클러스터 추가** 페이지에서 슬라이더를 사용하여 호스트 수를 선택합니다. **저장**을 선택합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss3-configure-new-cluster.png" alt-text="클러스터 추가 선택" border="true":::

   새 클러스터의 배포가 시작됩니다.

## <a name="scale-a-cluster"></a>클러스터 크기 조정 

1. 기존 프라이빗 클라우드의 개요 페이지에서 **프라이빗 클라우드 스케일링**을 선택하고 연필 아이콘을 선택하여 클러스터를 편집합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss4-select-scale-private-cloud-2.png" alt-text="클러스터 추가 선택" border="true":::

1. **클러스터 편집** 페이지에서 슬라이더를 사용하여 호스트 수를 선택합니다. **저장**을 선택합니다.

   :::image type="content" source="./media/tutorial-scale-private-cloud/ss5-scale-cluster.png" alt-text="클러스터 추가 선택" border="true":::

   클러스터에 호스트 추가가 시작됩니다.

## <a name="next-steps"></a>다음 단계

다른 Azure VMware Solution 프라이빗 클라우드가 필요한 경우 동일한 네트워킹 전제 조건, 클러스터 및 호스트 제한을 따라 [다른 프라이빗 클라우드를 만듭니다](tutorial-create-private-cloud.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
