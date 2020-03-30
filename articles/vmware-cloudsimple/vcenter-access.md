---
title: 클라우드에 의한 Azure VMware 솔루션 간단함 - 액세스 vSphere 클라이언트
description: 프라이빗 클라우드의 vCenter에 액세스하는 방법을 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022667"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>프라이빗 클라우드 vCenter 포털 액세스

Azure 포털 또는 CloudSimple 포털에서 프라이빗 클라우드 vCenter 포털을 시작할 수 있습니다.  vCenter 포털을 사용하면 프라이빗 클라우드에서 VMware 인프라를 관리할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

vCenter 포털에 액세스하려면 네트워크 연결을 설정해야 하며 DNS 이름 확인을 사용하도록 설정해야 합니다.  아래 옵션 중 한 가지를 사용하여 프라이빗 클라우드에 대한 네트워크 연결을 설정할 수 있습니다.

* [익스프레스루트를 사용하여 온-프레미스에서 클라우드로 연결](on-premises-connection.md)
* [클라우드단순 프라이빗 클라우드에 VPN 연결 구성](set-up-vpn.md)

프라이빗 클라우드 VMware 인프라 구성 요소의 DNS 이름 확인을 설정하려면 [온-프레미스 워크스테이션에서 사설 클라우드 vCenter 액세스에 대한 이름 확인을 위해 DNS 구성을](on-premises-dns-setup.md) 참조하십시오.

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="access-vcenter-from-azure-portal"></a>Azure 포털에서 액세스 vCenter

Azure 포털에서 프라이빗 클라우드의 vCenter 포털을 시작할 수 있습니다.

1. **모든 서비스를**선택합니다.

2. 클라우드 **간단한 서비스를**검색합니다.

3. 연결하려는 프라이빗 클라우드의 CloudSimple 서비스를 선택합니다.

4. **개요** 페이지에서 **VMware 프라이빗 클라우드 보기를** 클릭합니다.

    ![클라우드 단순 서비스 개요](media/cloudsimple-service-overview.png)

5. 프라이빗 클라우드 목록에서 프라이빗 클라우드를 선택하고 **vSphere 클라이언트 시작을**클릭합니다.

    ![vSphere 클라이언트 시작](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>클라우드심플 포털에서 vCenter 에 액세스

CloudSimple 포털에서 프라이빗 클라우드의 vCenter 포털을 시작할 수 있습니다.

1. [CloudSimple 포털에](access-cloudsimple-portal.md)액세스합니다.

2. **리소스에서** 액세스하려는 프라이빗 클라우드를 선택하고 **시작 vSphere 클라이언트를**클릭합니다.

    ![vSphere 클라이언트 시작 - 리소스](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 프라이빗 클라우드의 요약 화면에서 vCenter 포털을 시작할 수도 있습니다.

    ![출시 vSphere 클라이언트 - 요약](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>다음 단계

* [프라이빗 클라우드를 위한 VLAN/서브넷 생성 및 관리](create-vlan-subnet.md)
* [VMware vCenter의 클라우드심플 프라이빗 클라우드 권한 모델](learn-private-cloud-permissions.md)