---
title: V m/서브넷 만들기-CloudSimple 별 Azure VMware 솔루션
description: CloudSimple의 Azure VMware 솔루션-사설 클라우드의 Vlan/서브넷을 만들고 관리 하는 방법을 설명 하 고 방화벽 규칙을 적용 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c0160513eb9abca54adbc3819b982348dc202c90
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565998"
---
# <a name="create-and-manage-vlanssubnets-for-your-private-clouds"></a>사설 클라우드에 대 한 Vlan/서브넷 만들기 및 관리

네트워크 페이지에서 v m/서브넷 탭을 열어 사설 클라우드의 Vlan/서브넷을 만들고 관리 합니다. VLAN/서브넷을 만든 후에는 방화벽 규칙을 적용할 수 있습니다.

## <a name="create-a-vlansubnet"></a>VLAN/서브넷 만들기

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md) 하 고 측면 메뉴에서 **네트워크** 를 선택 합니다.
2. **Vlan/서브넷**을 선택 합니다.
3. **VLAN/서브넷 만들기**를 클릭 합니다.

    ![VLAN/서브넷 페이지](media/vlan-subnet-page.png)

4. 새 VLAN/서브넷에 대 한 사설 클라우드를 선택 합니다.
5. VLAN ID를 입력 하십시오.
6. 서브넷 이름을 입력 합니다.
7. VLAN (서브넷)에서 라우팅을 사용 하도록 설정 하려면 서브넷 CIDR 범위를 지정 합니다. CIDR 범위가 온-프레미스 서브넷, Azure 서브넷 또는 게이트웨이 서브넷과 겹치지 않는지 확인 합니다.
8. **제출**을 클릭합니다.

    ![VLAN/서브넷 만들기](media/create-new-vlan-subnet-details.png)


> [!IMPORTANT]
> 사설 클라우드 당 30 Vlan의 할당량이 있습니다. [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)하 여 이러한 제한을 늘릴 수 있습니다.

## <a name="use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere"></a>VLAN 정보를 사용 하 여 vSphere 분산 포트 그룹 설정

VSphere 분산 포트 그룹을 만들려면 <a href="https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf" target="_blank">Vsphere 네트워킹 가이드</a>의 VMware 항목 ' 분산 포트 그룹 추가 '에 설명 된 지침을 따르세요. 분산 포트 그룹을 설정 하는 경우 CloudSimple 구성에서 VLAN 정보를 제공 합니다.

![분산 포트 그룹](media/distributed-port-group.png)

## <a name="select-a-firewall-table"></a>방화벽 테이블 선택

방화벽 테이블 및 관련 규칙은 **네트워크 > 방화벽 테이블** 페이지에 정의 되어 있습니다. 사설 클라우드의 v m/서브넷에 적용할 방화벽 테이블을 선택 **하려면 vlan/서브넷 페이지에서** **방화벽 테이블 첨부 파일** 을 클릭 합니다. 방화벽 테이블 설정 및 규칙 정의에 대 한 지침은 [방화벽 테이블](firewall.md) 을 참조 하세요.

![방화벽 테이블 링크](media/vlan-subnet-firewall-link.png)

> [!NOTE]
> 서브넷은 하나의 방화벽 테이블에 연결할 수 있습니다. 방화벽 테이블은 여러 서브넷에 연결할 수 있습니다.

## <a name="edit-a-vlansubnet"></a>VLAN/서브넷 편집

VLAN/서브넷에 대 한 설정을 편집 하려면 **vlan/** 서브넷 페이지에서 해당 설정을 선택 하 고 **편집** 아이콘을 클릭 합니다. 변경을 수행 하 고 **Submet**을 클릭 합니다.

## <a name="delete-a-vlansubnet"></a>VLAN/서브넷 삭제

V m/서브넷을 삭제 하려면 **vlan/** 서브넷 페이지에서 선택 하 고 **삭제** 아이콘을 클릭 합니다. **삭제** 를 클릭 하 여 확인 합니다.
