---
title: '빠른 시작: VMware CloudSimple 서비스 만들기'
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 서비스를 만들고, 노드를 구매하고, 노드를 예약하는 방법을 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 14df0f131aaef8a4c24e2d1eb242a9b440e7c7b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86507593"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>빠른 시작 - Azure VMware Solution by CloudSimple 만들기

시작하려면 Azure Portal에서 Azure VMware Solution by CloudSimple을 만듭니다.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple - 서비스 개요

CloudSimple 서비스에서는 Azure VMware Solution by CloudSimple을 사용할 수 있습니다.  이 서비스를 만들면 노드를 프로비저닝하고, 노드를 예약하고, 프라이빗 클라우드를 만들 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에 CloudSimple 서비스를 추가합니다.  이 서비스는 Azure VMware Solution by CloudSimple의 경계 네트워크를 정의합니다.  이 경계 네트워크는 VPN, ExpressRoute 및 프라이빗 클라우드에 대한 인터넷 연결을 포함하는 서비스에 사용됩니다.

CloudSimple 서비스를 추가하려면 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 경계 네트워크를 만들 때 사용되며 /28 CIDR 블록이 필요합니다. 게이트웨이 서브넷 주소 공간은 고유해야 합니다. 온-프레미스 네트워크 주소 공간 또는 Azure 가상 네트워크 주소 공간과 겹칠 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

게이트웨이 서브넷에 /28 CIDR 블록을 할당합니다.  게이트웨이 서브넷은 CloudSimple 서비스별로 필요하며 생성된 지역에 대해 고유합니다. 게이트웨이 서브넷은 Azure VMware Solution by CloudSimple 경계 네트워크 서비스에 사용되며 /28 CIDR 블록이 필요합니다. 게이트웨이 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다.  CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다.

[네트워킹 필수 구성 요소](cloudsimple-network-checklist.md)를 검토합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스** 를 선택합니다.
2. **CloudSimple 서비스** 를 검색합니다.

    ![CloudSimple 서비스 검색](media/create-cloudsimple-service-search.png)

3. **CloudSimple 서비스** 를 선택합니다.
4. **추가** 를 클릭하여 새 서비스를 만듭니다.

    ![CloudSimple 서비스 추가](media/create-cloudsimple-service-add.png)

5. CloudSimple 서비스를 만들려는 구독을 선택합니다.
6. 서비스에 대한 리소스 그룹을 선택합니다. 새 리소스 그룹을 추가하려면 **새로 만들기** 를 클릭합니다.
7. 서비스를 식별하는 이름을 입력합니다.
8. 서비스 게이트웨이의 CIDR을 입력합니다. 온-프레미스 서브넷, Azure 서브넷 또는 계획된 CloudSimple 서브넷과 겹치지 않는 /28 서브넷을 지정합니다. 서비스가 생성된 후에는 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)

9. **확인** 을 클릭합니다.

서비스가 만들어지고 서비스 목록에 추가됩니다.

## <a name="provision-nodes"></a>노드 프로비전

CloudSimple 프라이빗 클라우드 환경에 대한 종량제 용량을 설정하려면 먼저 Azure Portal에 노드를 프로비저닝합니다.

1. **모든 서비스** 를 선택합니다.
2. **CloudSimple 노드** 를 검색합니다.

    ![CloudSimple 노드 검색](media/create-cloudsimple-node-search.png)

3. **CloudSimple 노드** 를 선택합니다.
4. **추가** 를 클릭하여 노드를 만듭니다.

    ![CloudSimple 노드 추가](media/create-cloudsimple-node-add.png)

5. CloudSimple 노드를 프로비저닝할 구독을 선택합니다.
6. 노드에 대한 리소스 그룹을 선택합니다. 새 리소스 그룹을 추가하려면 **새로 만들기** 를 클릭합니다.
7. 노드를 식별할 접두사를 입력합니다.
8. 노드 리소스 위치를 선택합니다.
9. 노드 리소스를 호스트할 전용 위치를 선택합니다.
10. [노드 형식](cloudsimple-node.md)을 선택합니다.
11. 프로비저닝할 노드 수를 선택합니다.
12. **검토 + 생성** 를 선택합니다.
13. 설정을 검토합니다. 설정을 수정하려면 **이전** 을 클릭합니다.
14. **만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 만들기 및 환경 구성](quickstart-create-private-cloud.md)
* [CloudSimple 서비스](./cloudsimple-service.md)에 대해 자세히 알아보기
