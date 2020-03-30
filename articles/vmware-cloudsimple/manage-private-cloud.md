---
title: 클라우드단순 프라이빗 클라우드로 Azure VMware 솔루션 관리
description: CloudSimple 프라이빗 클라우드 리소스 및 활동을 관리하는 데 사용할 수 있는 기능에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 620c0226d3aca907352658ebbe1b94c7673d91cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014830"
---
# <a name="manage-private-cloud-resources-and-activity"></a>프라이빗 클라우드 리소스 및 활동 관리

프라이빗 클라우드는 CloudSimple 포털에서 관리됩니다.  CloudSimple 포털의 상태, 사용 가능한 리소스, 프라이빗 클라우드의 활동 및 기타 설정을 확인합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

에서 [https://portal.azure.com](https://portal.azure.com)Azure 포털에 로그인합니다.

## <a name="access-the-cloudsimple-portal"></a>CloudSimple 포털 액세스

[CloudSimple 포털에 액세스합니다.](access-cloudsimple-portal.md)

## <a name="view-the-list-of-private-clouds"></a>프라이빗 클라우드 목록 보기

**리소스** 페이지의 **비공개 클라우드** 탭에는 구독의 모든 비공개 클라우드가 나열됩니다. 정보에는 이름, vSphere 클러스터 수, 위치, 프라이빗 클라우드의 현재 상태 및 리소스 정보가 포함됩니다.

![프라이빗 클라우드 페이지](media/manage-private-cloud.png)

추가 정보 및 작업을 보려면 프라이빗 클라우드를 선택합니다.

## <a name="private-cloud-summary"></a>프라이빗 클라우드 요약

선택한 프라이빗 클라우드에 대한 포괄적인 요약을 확인합니다.  요약 페이지에는 프라이빗 클라우드에 배포된 DNS 서버가 포함됩니다.  온-프레미스 DNS 서버에서 프라이빗 클라우드 DNS 서버로 DNS 전달을 설정할 수 있습니다.  DNS 전달에 대한 자세한 내용은 [온-프레미스에서 프라이빗 클라우드 vCenter의 이름 확인에 대한 DNS 구성을](https://docs.azure.cloudsimple.com/on-premises-dns-setup/)참조하십시오.

![프라이빗 클라우드 요약](media/private-cloud-summary.png)

### <a name="available-actions"></a>사용 가능한 작업

* [vSphere 클라이언트를 시작합니다.](https://docs.azure.cloudsimple.com/vsphere-access/) 이 프라이빗 클라우드의 경우 vCenter에 액세스합니다.
* [노드를 구입합니다.](create-nodes.md) 이 프라이빗 클라우드에 노드를 추가합니다.
* [을 확장합니다.](expand-private-cloud.md) 이 프라이빗 클라우드에 노드를 추가합니다.
* **새로 고침**. 이 페이지의 정보를 업데이트합니다.
* **삭제합니다.** 언제든지 프라이빗 클라우드를 삭제할 수 있습니다. **삭제하기 전에 모든 시스템과 데이터를 백업했는지 확인하십시오.** 프라이빗 클라우드를 삭제하면 모든 VM, vCenter 구성 및 데이터가 삭제됩니다. 선택한 프라이빗 클라우드의 요약 섹션에서 **삭제를** 클릭합니다. 삭제 후 모든 프라이빗 클라우드 데이터는 안전하고 규정을 준수하는 삭제 프로세스에서 삭제됩니다.
* [vSphere 권한 변경](escalate-private-cloud-privileges.md).  이 프라이빗 클라우드에서 권한을 에스컬레이션합니다.

## <a name="private-cloud-vlanssubnets"></a>프라이빗 클라우드 VLANS/서브넷

선택한 프라이빗 클라우드에 대해 정의된 VLAN/서브넷 목록을 봅니다.  이 목록에는 프라이빗 클라우드를 만들 때 생성된 VLAN/서브넷 관리가 포함됩니다.

![프라이빗 클라우드 - VLAN/서브넷](media/private-cloud-vlans-subnets.png) 

### <a name="available-actions"></a>사용 가능한 작업

* [VLANS/서브넷을 추가합니다.](https://docs.azure.cloudsimple.com/create-vlan-subnet/) 이 프라이빗 클라우드에 VLAN/하위 집합을 추가합니다.

다음 작업을 위해 VLAN/서브넷 선택
* [방화벽 테이블을 연결합니다.](https://docs.azure.cloudsimple.com/firewall/) 이 프라이빗 클라우드에 방화벽 테이블을 연결합니다.
* **편집**
* **삭제(사용자** 정의 VLAN/서브넷만 해당)

## <a name="private-cloud-activity"></a>프라이빗 클라우드 활동

선택한 프라이빗 클라우드에 대한 다음 정보를 봅니다.  활동 정보는 선택한 프라이빗 클라우드에 대한 모든 활동의 필터링된 목록입니다.  이 페이지에는 최대 25개의 최근 활동이 표시됩니다.

* 최근 경고
* 최근 이벤트
* 최근 작업
* 최근 감사

![프라이빗 클라우드 - 활동](media/private-cloud-activity.png)

## <a name="cloud-racks"></a>클라우드 랙

클라우드 랙은 프라이빗 클라우드의 구성 요소입니다. 각 랙은 용량 단위를 제공합니다. CloudSimple은 프라이빗 클라우드를 만들거나 확장할 때 선택한 항목에 따라 클라우드 랙을 자동으로 구성합니다.  각각 할당된 프라이빗 클라우드를 포함하여 클라우드 랙의 전체 목록을 봅니다.

![프라이빗 클라우드 - 클라우드 랙](media/private-cloud-cloudracks.png)

## <a name="vsphere-management-network"></a>vSphere 관리 네트워크

현재 프라이빗 클라우드에서 구성된 VMware 관리 리소스 및 가상 시스템 목록입니다. 정보에는 소프트웨어 버전, 정규화된 도메인 이름(FQDN) 및 리소스의 IP 주소가 포함됩니다.

![프라이빗 클라우드 - vSphere 관리 네트워크](media/private-cloud-vsphere-management-network.png)

## <a name="next-steps"></a>다음 단계

* [Azure에서 VMware VM 사용](quickstart-create-vmware-virtual-machine.md)
* [프라이빗 클라우드에](cloudsimple-private-cloud.md) 대해 자세히 알아보기