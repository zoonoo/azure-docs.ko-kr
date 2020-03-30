---
title: 클라우드간이별 Azure VMware 솔루션 - 클라우드 간편 서비스 만들기
description: Azure 포털에서 CloudSimple 서비스를 만드는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024826"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>클라우드단순 서비스로 Azure VMware 솔루션 만들기

CloudSimple별 Azure VMware 솔루션을 시작하려면 Azure 포털에서 CloudSimple 서비스별 Azure VMware 솔루션을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

게이트웨이 서브넷에 /28 CIDR 블록을 할당합니다. CloudSimple 서비스에 따라 게이트웨이 서브넷이 필요하며 게이트웨이 서브넷이 생성된 리전에 고유합니다. 게이트웨이 서브넷은 에지 네트워크 서비스에 사용되며 /28 CIDR 블록이 필요합니다. 게이트웨이 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure 포털에](https://portal.azure.com)로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스를**선택합니다.
2. 클라우드 **간단한 서비스를**검색합니다.
    ![검색 클라우드심플 서비스](media/create-cloudsimple-service-search.png)
3. **클라우드 단순 서비스를**선택합니다.
4. **추가를** 클릭하여 새 서비스를 만듭니다.
    ![클라우드 심플 서비스 추가](media/create-cloudsimple-service-add.png)
5. CloudSimple 서비스를 만들 구독을 선택합니다.
6. 서비스에 대한 리소스 그룹을 선택합니다. 새 리소스 그룹을 추가하려면 **새 만들기를 클릭합니다.**
7. 이름을 입력하여 서비스를 식별합니다.
8. 서비스 게이트웨이에 대한 CIDR을 입력합니다. 온-프레미스 서브넷, Azure 서브넷 또는 계획된 CloudSimple 서브넷과 겹치지 않는 /28 서브넷을 지정합니다. 서비스를 만든 후에는 CIDR을 변경할 수 없습니다.

    ![클라우드 간이 서비스 만들기](media/create-cloudsimple-service.png)
9. **확인**을 클릭합니다.

서비스가 만들어지고 서비스 목록에 추가됩니다.

## <a name="next-steps"></a>다음 단계

* [노드 프로비전](create-nodes.md) 방법 알아보기
* [프라이빗 클라우드를 만드는](create-private-cloud.md) 방법 알아보기
* [프라이빗 클라우드 환경을 구성하는](quickstart-create-private-cloud.md) 방법 알아보기
