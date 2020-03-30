---
title: 클라우드에 의한 Azure VMware 솔루션간단함 - 방화벽 테이블 및 규칙 설정
description: 서브넷 및 VLAN의 트래픽을 제한하기 위해 Private Cloud 방화벽 테이블 및 규칙을 설정하는 방법에 대해 설명합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244668"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>사설 클라우드에 대한 방화벽 테이블 및 규칙 설정

방화벽 테이블과 관련 규칙을 사용하면 특정 서브넷 및 VLAN에 적용할 트래픽 제한을 지정할 수 있습니다.

* 서브넷은 하나의 방화벽 테이블과 연결할 수 있습니다.
* 방화벽 테이블은 여러 서브넷과 연결할 수 있습니다.

## <a name="add-a-new-firewall-table"></a>새 방화벽 테이블 추가

1. [CloudSimple 포털에 액세스하고](access-cloudsimple-portal.md) 사이드 메뉴에서 **네트워크를** 선택합니다.
2. **방화벽 테이블을 선택합니다.**
3. **방화벽 테이블 만들기를**선택합니다.

    ![VLAN/서브넷 페이지](media/firewall-tables-page.png)

4. 테이블의 이름을 입력합니다.
5. 테이블에 대한 기본 규칙이 나열됩니다. **새 규칙 만들기를** 클릭하여 추가 규칙을 만듭니다. 자세한 내용은 다음 절차를 참조하십시오.
6. **완료를** 클릭하여 방화벽 테이블을 저장합니다.

> [!IMPORTANT]
> 프라이빗 클라우드당 최대 2개의 방화벽 테이블을 만들 수 있습니다.

## <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙은 방화벽이 특정 유형의 트래픽을 처리하는 방법을 결정합니다. 선택한 방화벽 테이블의 **규칙** 탭에는 관련된 모든 규칙이 나열됩니다.

![방화벽 규칙 테이블](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>방화벽 규칙 만들기

1. 다음과 같은 방법으로 방화벽 규칙을 만들 수 있도록 설정을 표시합니다.
    * 방화벽 테이블을 만들 때 **규칙 추가를** 클릭합니다.
    * **네트워크 > 방화벽 테이블** 페이지에서 특정 방화벽 테이블을 선택하고 새 방화벽 만들기 규칙 **만들기를**클릭합니다.
2. 다음과 같이 규칙을 설정합니다.
    * **이름**. 규칙에 이름을 지정합니다.
    * **우선 순위**. 규칙에 우선 순위를 할당합니다. 숫자가 낮은 규칙이 먼저 실행됩니다.
    * **트래픽 유형**. 규칙이 프라이빗 클라우드, 인터넷 또는 VPN 트래픽(상태 비해제)에 대한 규칙인지 아니면 공용 IP 주소(상태 공개)인지 선택합니다.
    * **프로토콜**. 규칙이 적용되는 프로토콜(TCP, UDP 또는 모든 프로토콜)을 선택합니다.
    * **방향**. 규칙이 인바운드 트래픽또는 아웃바운드 트래픽인지 여부를 선택합니다. 인바운드 및 아웃바운드 트래픽에 대한 별도의 규칙을 정의해야 합니다.
    * **작업**. 규칙이 일치하는 경우(허용 또는 거부) 취할 작업을 선택합니다.
    * **소스**. 규칙이 적용되는 소스(CIDR 블록, 내부 또는 모든 소스)를 지정합니다.
    * **소스 포트 범위**. 규칙에 따라 포트 의 범위를 지정합니다.
    * **방향**. 인바운드 또는 아웃바운드를 선택합니다.
    * **대상**. 규칙이 적용되는 대상(CIDR 블록, 내부 또는 모든 소스)을 지정합니다.
    * **소스 포트 범위**. 규칙에 따라 포트 의 범위를 지정합니다.

    ![방화벽 테이블 추가 규칙](media/firewall-rule-create.png)

3. **완료를** 클릭하여 규칙을 저장하고 방화벽 테이블의 규칙 목록에 추가합니다.

> [!IMPORTANT]
> 각 방화벽 테이블에는 최대 10개의 인바운드 규칙과 20개의 아웃바운드 규칙이 있을 수 있습니다. [지원에 문의하여](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)이러한 제한을 늘릴 수 있습니다.

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>VLAN/서브넷 첨부

방화벽 테이블을 정의한 후 테이블의 규칙적용을 받는 서브넷을 지정할 수 있습니다.

1. **네트워크** > **방화벽 테이블** 페이지에서 방화벽 테이블을 선택합니다.
2. 연결된 **VLAN/서브넷** 탭을 엽니다.
3. **VLAN/서브넷에 연결 합니다.**
4. 프라이빗 클라우드 및 VLAN을 선택합니다. 연결된 서브넷 이름과 CIDR 블록이 표시됩니다.
5. **제출**을 클릭합니다.
