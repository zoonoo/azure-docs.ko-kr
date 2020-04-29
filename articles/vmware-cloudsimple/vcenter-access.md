---
title: CloudSimple 액세스 vSphere 클라이언트에의 한 Azure VMware 솔루션
description: 사설 클라우드의 vCenter에 액세스 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 18d9463bc512257034860e1188372879524924f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77022667"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>사설 클라우드 vCenter 포털 액세스

Azure Portal 또는 CloudSimple 포털에서 사설 클라우드 vCenter 포털을 시작할 수 있습니다.  vCenter portal을 사용 하 여 사설 클라우드에서 VMware 인프라를 관리할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

VCenter 포털에 액세스 하려면 네트워크 연결을 설정 하 고 DNS 이름 확인을 사용 하도록 설정 해야 합니다.  아래 옵션 중 하나를 사용 하 여 사설 클라우드에 대 한 네트워크 연결을 설정할 수 있습니다.

* [Express 경로를 사용 하 여 온-프레미스에서 CloudSimple로 연결](on-premises-connection.md)
* [CloudSimple 사설 클라우드에 대 한 VPN 연결 구성](set-up-vpn.md)

사설 클라우드 VMware 인프라 구성 요소에 대 한 DNS 이름 확인을 설정 하려면 [온-프레미스 워크스테이션에서 사설 클라우드 vCenter 액세스를 위한 이름 확인을 위해 Dns 구성](on-premises-dns-setup.md) 을 참조 하세요.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="access-vcenter-from-azure-portal"></a>Azure Portal에서 vCenter 액세스

Azure Portal에서 사설 클라우드의 vCenter 포털을 시작할 수 있습니다.

1. **모든 서비스**를 선택합니다.

2. **Cloudsimple 서비스**를 검색 합니다.

3. 연결 하려는 사설 클라우드의 CloudSimple 서비스를 선택 합니다.

4. **개요** 페이지에서 **VMware 사설 클라우드 보기** 를 클릭 합니다.

    ![CloudSimple 서비스 개요](media/cloudsimple-service-overview.png)

5. 사설 클라우드 목록에서 사설 클라우드를 선택 하 고 **vSphere 클라이언트 시작**을 클릭 합니다.

    ![VSphere 클라이언트 시작](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>CloudSimple 포털에서 vCenter 액세스

CloudSimple 포털에서 사설 클라우드의 vCenter 포털을 시작할 수 있습니다.

1. [Cloudsimple 포털](access-cloudsimple-portal.md)에 액세스 합니다.

2. **리소스** 에서 액세스 하려는 사설 클라우드를 선택 하 고 **Vsphere 클라이언트 시작**을 클릭 합니다.

    ![VSphere 클라이언트 리소스를 시작 합니다.](media/cloudsimple-portal-resources-launch-vcenter.png)

3. 사설 클라우드의 요약 화면에서 vCenter 포털을 시작할 수도 있습니다.

    ![VSphere 클라이언트 시작-요약](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>다음 단계

* [사설 클라우드에 대 한 Vlan/서브넷 만들기 및 관리](create-vlan-subnet.md)
* [VMware vCenter의 CloudSimple 사설 클라우드 권한 모델](learn-private-cloud-permissions.md)