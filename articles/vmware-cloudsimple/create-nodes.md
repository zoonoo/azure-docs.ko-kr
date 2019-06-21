---
title: CloudSimple-Azure에서 VMware 솔루션에 대 한 노드를 프로 비전
description: CloudSimple 배포를 사용 하 여 vmware에 노드를 추가 하는 방법 알아보기
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165249"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>CloudSimple-Azure에서 VMware 솔루션에 대 한 노드를 프로 비전

Azure portal에서 노드를 프로 비전 합니다. 그런 다음 종 량 이동 용량 CloudSimple 사설 클라우드 환경에 설정할 수 있습니다.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>CloudSimple 사설 클라우드를 프로 비전 된 노드를 추가 합니다.

1. **모든 서비스**를 선택합니다.
2. 검색할 **CloudSimple 노드**합니다.

   ![검색 CloudSimple 노드](media/create-cloudsimple-node-search.png)

3. 선택 **CloudSimple 노드**합니다.
4. 클릭 **추가** 노드를 만들려고 합니다.

    ![CloudSimple 노드 추가](media/create-cloudsimple-node-add.png)

5. CloudSimple 노드를 프로 비전 하려는 구독을 선택 합니다.
6. 노드에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 클릭 **새로 만들기**합니다.
7. 노드를 식별 하는 접두사를 입력 합니다.
8. 노드 리소스의 위치를 선택 합니다.
9. 전용된 노드 리소스를 호스트 위치를 선택 합니다.
10. 노드 유형을 선택 합니다. 선택할 수 있습니다 합니다 [CS28 또는 CS36 옵션](cloudsimple-node.md)합니다. 후자는 최대 계산 및 메모리 용량을 포함합니다.
11. 프로 비전 하는 노드 수를 선택 합니다.
12. **검토 + 만들기**를 선택합니다.
13. 설정을 검토 합니다. 모든 설정을 수정 하려면 클릭 **이전**합니다.
14. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [사설 클라우드 만들기](https://docs.azure.cloudsimple.com/create-private-cloud/)
