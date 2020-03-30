---
title: 클라우드단순 프라이빗 클라우드로 Azure VMware 솔루션 삭제
description: CloudSimple 프라이빗 클라우드를 삭제하는 방법을 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024758"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>클라우드단순 프라이빗 클라우드 삭제

CloudSimple은 프라이빗 클라우드를 삭제할 수 있는 유연성을 제공합니다.  프라이빗 클라우드는 하나 이상의 vSphere 클러스터로 구성됩니다. 각 클러스터에는 3~16개의 노드가 있을 수 있습니다. 프라이빗 클라우드를 삭제하면 모든 클러스터가 삭제됩니다.

## <a name="before-you-begin"></a>시작하기 전에

프라이빗 클라우드를 삭제해도 전체 프라이빗 클라우드가 삭제됩니다.  프라이빗 클라우드의 모든 구성 요소가 삭제됩니다.  데이터를 유지하려면 온-프레미스 저장소 또는 Azure 저장소에 데이터를 백업했는지 확인합니다.

프라이빗 클라우드의 구성 요소는 다음과 같습니다.

* 클라우드 심플 노드
* 가상 머신
* VLAN/서브넷
* 프라이빗 클라우드에 저장된 모든 사용자 데이터
* VLAN/서브넷에 대한 모든 방화벽 규칙 첨부 파일

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="delete-a-private-cloud"></a>프라이빗 클라우드 삭제

1. [CloudSimple 포털에 액세스합니다.](access-cloudsimple-portal.md)

2. **리소스** 페이지를 엽니다.

3. 삭제하려는 프라이빗 클라우드를 클릭합니다.

4. 요약 페이지에서 **에서 삭제를**클릭합니다.

    ![프라이빗 클라우드 삭제](media/delete-private-cloud.png)

5. 확인 페이지에서 비공개 클라우드의 이름을 입력하고 **삭제를**클릭합니다. 

    ![프라이빗 클라우드 삭제 - 확인](media/delete-private-cloud-confirm.png)

프라이빗 클라우드는 삭제로 표시됩니다.  삭제 프로세스는 3시간 후에 시작되어 프라이빗 클라우드를 삭제합니다.

> [!CAUTION]
> 노드는 프라이빗 클라우드를 삭제한 후 삭제해야 합니다.  노드가 구독에서 삭제될 때까지 노드 계량이 계속됩니다.

## <a name="next-steps"></a>다음 단계

* [노드 삭제](delete-nodes.md)
