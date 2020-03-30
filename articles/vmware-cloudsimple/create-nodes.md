---
title: 클라우드심플 - Azure를 통해 VM웨어 솔루션에 대한 프로비전 노드
description: CloudSimple 배포를 통해 VMWare에 노드를 추가하는 방법 알아보기
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024809"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>클라우드단순에 의한 Azure VMware 솔루션용 프로비전 노드

Azure 포털에서 노드를 프로비전합니다. 그런 다음 CloudSimple 프라이빗 클라우드 환경에 대해 종량제 용량을 설정할 수 있습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드에 노드 추가

1. **모든 서비스를**선택합니다.
2. 클라우드 **단순 노드 검색**.

   ![클라우드 단순 노드 검색](media/create-cloudsimple-node-search.png)

3. **클라우드 단순 노드를 선택합니다.**
4. 노드를 만들려면 **추가를** 클릭합니다.

    ![클라우드 심플 노드 추가](media/create-cloudsimple-node-add.png)

5. CloudSimple 노드를 프로비전할 구독을 선택합니다.
6. 노드에 대한 리소스 그룹을 선택합니다. 새 리소스 그룹을 추가하려면 **새 만들기를 클릭합니다.**
7. 접두사를 입력하여 노드를 식별합니다.
8. 노드 리소스의 위치를 선택합니다.
9. 노드 리소스를 호스트할 전용 위치를 선택합니다.
10. 노드 [유형을 선택합니다.](cloudsimple-node.md)
11. 프로비전할 노드 수를 선택합니다.
12. **검토 + 만들기를**선택합니다.
13. 설정을 검토합니다. 설정을 수정하려면 이전 을 **클릭합니다.**
14. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 만들기](create-private-cloud.md)
