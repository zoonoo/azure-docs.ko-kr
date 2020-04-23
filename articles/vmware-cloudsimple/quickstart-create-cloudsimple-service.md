---
title: '빠른 시작: VMware 클라우드 단순 서비스 만들기'
titleSuffix: Azure VMware Solution by CloudSimple
description: CloudSimple 서비스 생성, 노드 구매 및 노드 예약 방법 알아보기
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868027"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>빠른 시작 - CloudSimple 서비스로 Azure VMware 솔루션 만들기

시작하려면 Azure 포털에서 CloudSimple별로 Azure VMware 솔루션을 만듭니다.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>클라우드에 의한 VM웨어 솔루션 간이 - 서비스 개요

CloudSimple 서비스를 사용하면 CloudSimple별로 Azure VMware 솔루션을 사용할 수 있습니다.  서비스를 만들면 노드를 프로비전하고 노드를 예약하며 프라이빗 클라우드를 만들 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 리전에 CloudSimple 서비스를 추가합니다.  서비스는 CloudSimple에 의해 Azure VMware 솔루션의 에지 네트워크를 정의합니다.  이 에지 네트워크는 VPN, ExpressRoute 및 개인 클라우드에 대한 인터넷 연결을 포함하는 서비스에 사용됩니다.

CloudSimple 서비스를 추가하려면 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 에지 네트워크를 만들 때 사용되며 /28 CIDR 블록이 필요합니다. 게이트웨이 서브넷 주소 공간은 고유해야 합니다. 온-프레미스 네트워크 주소 공간 또는 Azure 가상 네트워크 주소 공간과 겹칠 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전에

게이트웨이 서브넷에 /28 CIDR 블록을 할당합니다.  CloudSimple 서비스에 따라 게이트웨이 서브넷이 필요하며 게이트웨이 서브넷이 생성된 리전에 고유합니다. 게이트웨이 서브넷은 CloudSimple 에지 네트워크 서비스에서 Azure VMware 솔루션에 사용되며 /28 CIDR 블록이 필요합니다. 게이트웨이 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다.  CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다.

[네트워킹 필수 구성 조건](cloudsimple-network-checklist.md)검토 . 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스**를 선택합니다.
2. 클라우드 **단순 서비스를**검색합니다.

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

## <a name="provision-nodes"></a>노드 프로비전

CloudSimple 프라이빗 클라우드 환경에 대한 종량제 용량을 설정하려면 Azure Portal에서 노드를 먼저 프로비전합니다.

1. **모든 서비스**를 선택합니다.
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
12. **검토 + 만들기**를 선택합니다.
13. 설정을 검토합니다. 설정을 수정하려면 이전 을 **클릭합니다.**
14. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드 만들기 및 환경 구성](quickstart-create-private-cloud.md)
* [클라우드심플 서비스에](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service) 대해 자세히 알아보기
