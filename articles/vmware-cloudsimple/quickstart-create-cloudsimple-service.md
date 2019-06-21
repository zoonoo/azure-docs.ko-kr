---
title: Azure의 VMware 솔루션 CloudSimple 빠른 시작-에서 서비스 만들기
description: CloudSimple 서비스를 만드는 방법 알아보기 노드를 프로 비전 하 고 예약 노드
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164212"
---
# <a name="quickstart---create-service"></a>빠른 시작-서비스 만들기

시작 하려면 Azure portal에서 CloudSimple 하 여 Azure VMware 솔루션을 만듭니다.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware 솔루션 CloudSimple-서비스 개요

CloudSimple 서비스를 사용 하면 Azure CloudSimple VMware 솔루션을 사용할 수 있습니다.  서비스를 만들고 노드를 예약 하 고 사설 클라우드를 만들 노드 프로 비전 할 수 있습니다.  CloudSimple 서비스를 사용할 수 있는 각 Azure 지역에 CloudSimple 서비스를 추가 합니다.  서비스는 CloudSimple 하 여 Azure VMware 솔루션의 경계 네트워크를 정의합니다.  이 경계 네트워크는 VPN을 ExpressRoute 및 인터넷 연결 사설 클라우드를 포함 하는 서비스에 사용 됩니다.

CloudSimple 서비스를 추가 하려면 게이트웨이 서브넷을 만들어야 합니다. 게이트웨이 서브넷은 경계 네트워크를 만들 때 사용 하며 을/28 CIDR 블록입니다. 게이트웨이 서브넷 주소 공간은 고유 해야 합니다. 이 온-프레미스 네트워크 주소 공간 또는 Azure virtual network 주소 공간을 사용 하 여 겹칠 수 없습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Microsoft.VMwareCloudSimple 리소스 공급자를 사용 하도록 설정

CloudSimple 서비스에 대 한 리소스 공급자를 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. **모든 서비스**를 선택합니다.
2. 검색 및 선택 **구독**합니다.

    ![구독 선택](media/cloudsimple-service-select-subscriptions.png)

3. CloudSimple 서비스를 사용 하도록 설정 하려는 구독 선택
4. 클릭할 **리소스 공급자** 구독에 대 한
5. 사용 하 여 **Microsoft.VMwareCloudSimple** 리소스 공급자를 필터링 하려면
6. 선택 된 **Microsoft.VMwareCloudSimple** 리소스 공급자 및 클릭 **등록**

    ![리소스 공급자 등록](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스**를 선택합니다.
2. 검색할 **CloudSimple 서비스**합니다.

    ![CloudSimple 서비스 검색](media/create-cloudsimple-service-search.png)

3. 선택 **CloudSimple 서비스**합니다.
4. 클릭 **추가** 새 서비스를 만듭니다.

    ![CloudSimple 서비스 추가](media/create-cloudsimple-service-add.png)

5. CloudSimple를 만들려는 구독을 선택 합니다.
6. 서비스에 대 한 리소스 그룹을 선택 합니다. 새 리소스 그룹을 추가 하려면 클릭 **새로 만들기**합니다.
7. 서비스를 식별 하는 이름을 입력 합니다.
8. 서비스 게이트웨이 CIDR을 입력 합니다. / 28 지정에 온-프레미스 서브넷, Azure 서브넷 또는 계획 된 CloudSimple 서브넷 중 하나를 사용 하 여 겹치지 않는 서브넷입니다. 서비스를 만든 후에 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)

9. **확인**을 클릭합니다.

서비스 생성 되어 서비스 목록에 추가 합니다.

## <a name="provision-nodes"></a>노드 프로비전

종 량 이동 용량 CloudSimple 사설 클라우드 환경에 대 한을 설정 하려면 먼저 Azure portal에서 노드 프로 비전 합니다.

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

* [사설 클라우드를 만들고 환경 구성](quickstart-create-private-cloud.md)
* 자세한 내용은 [CloudSimple 서비스](https://docs.azure.cloudsimple.com/cloudsimple-service)
