---
title: CloudSimple 빠른 시작 별 Azure VMware 솔루션-서비스 만들기
description: CloudSimple 서비스를 만들고, 노드를 구매 하 고, 노드를 예약 하는 방법에 대해 알아봅니다.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 39395c9d082bc40836c14ee4c0d328341f96edca
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2019
ms.locfileid: "71816107"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>빠른 시작-CloudSimple service로 Azure VMware 솔루션 만들기

시작 하려면 Azure Portal에서 CloudSimple로 Azure VMware 솔루션을 만듭니다.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>CloudSimple 서비스의 VMware 솔루션 개요

CloudSimple 서비스를 사용 하면 CloudSimple로 Azure VMware 솔루션을 사용할 수 있습니다.  서비스를 만들면 노드를 프로 비전 하 고, 노드를 예약 하 고, 사설 클라우드를 만들 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에 CloudSimple 서비스를 추가 합니다.  서비스는 CloudSimple로 Azure VMware 솔루션의에 지 네트워크를 정의 합니다.  이 edge 네트워크는 VPN, Express 경로 및 사설 클라우드에 대 한 인터넷 연결을 포함 하는 서비스에 사용 됩니다.

CloudSimple 서비스를 추가 하려면 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은에 지 네트워크를 만들 때 사용 되며/28 CIDR 블록이 필요 합니다. 게이트웨이 서브넷 주소 공간은 고유 해야 합니다. 온-프레미스 네트워크 주소 공간 또는 Azure 가상 네트워크 주소 공간과 겹칠 수 없습니다.

## <a name="before-you-begin"></a>시작하기 전 주의 사항

게이트웨이 서브넷에 대 한/28 CIDR 블록을 할당 합니다.  게이트웨이 서브넷은 CloudSimple 서비스 마다 필요 하며 생성 된 지역에 대해 고유 합니다. 게이트웨이 서브넷은 CloudSimple edge network services에서 Azure VMware 솔루션에 사용 되며/28 CIDR 블록이 필요 합니다. 게이트웨이 서브넷 주소 공간은 고유 해야 합니다. CloudSimple 환경과 통신 하는 네트워크와 겹치지 않아야 합니다.  CloudSimple과 통신 하는 네트워크에는 온-프레미스 네트워크 및 Azure virtual network가 포함 됩니다.

[네트워킹 필수 구성 요소](cloudsimple-network-checklist.md)를 검토 합니다. 

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 서비스**를 검색 합니다.

    ![CloudSimple Service 검색](media/create-cloudsimple-service-search.png)

3. **Cloudsimple 서비스**를 선택 합니다.
4. **추가** 를 클릭 하 여 새 서비스를 만듭니다.

    ![CloudSimple Service 추가](media/create-cloudsimple-service-add.png)

5. CloudSimple 서비스를 만들려는 구독을 선택 합니다.
6. 서비스에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 **새로 만들기**를 클릭 합니다.
7. 서비스를 식별 하는 이름을 입력 합니다.
8. 서비스 게이트웨이의 CIDR을 입력 합니다. 온-프레미스 서브넷, Azure 서브넷 또는 계획 된 CloudSimple 서브넷과 겹치지 않는/28 서브넷을 지정 합니다. 서비스를 만든 후에는 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)

9. **확인**을 클릭합니다.

서비스가 만들어지고 서비스 목록에 추가 됩니다.

## <a name="provision-nodes"></a>노드 프로비전

CloudSimple 사설 클라우드 환경에 대 한 종 량 제 용량을 설정 하려면 먼저 Azure Portal에 노드를 프로 비전 합니다.

1. **모든 서비스**를 선택합니다.
2. **Cloudsimple 노드**를 검색 합니다.

    ![CloudSimple 노드 검색](media/create-cloudsimple-node-search.png)

3. **Cloudsimple 노드**를 선택 합니다.
4. **추가** 를 클릭 하 여 노드를 만듭니다.

    ![CloudSimple 노드 추가](media/create-cloudsimple-node-add.png)

5. CloudSimple 노드를 프로 비전 할 구독을 선택 합니다.
6. 노드에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 **새로 만들기**를 클릭 합니다.
7. 노드를 식별 하는 접두사를 입력 합니다.
8. 노드 리소스의 위치를 선택 합니다.
9. 노드 리소스를 호스트할 전용 위치를 선택 합니다.
10. 노드 형식을 선택 합니다. [CS28 또는 CS36 옵션](cloudsimple-node.md)을 선택할 수 있습니다. 후자 옵션은 최대 계산 및 메모리 용량을 포함 합니다.
11. 프로 비전 할 노드 수를 선택 합니다.
12. **검토 + 만들기**를 선택합니다.
13. 설정을 검토 합니다. 설정을 수정 하려면 **이전**을 클릭 합니다.
14. **만들기**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [사설 클라우드 만들기 및 환경 구성](quickstart-create-private-cloud.md)
* [Cloudsimple service](https://docs.azure.cloudsimple.com/cloudsimple-service) 에 대 한 자세한 정보
