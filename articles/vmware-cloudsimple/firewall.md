---
title: CloudSimple의 Azure VMware 솔루션-방화벽 테이블 및 규칙 설정
description: 서브넷 및 Vlan에서 트래픽을 제한 하도록 사설 클라우드 방화벽 테이블 및 규칙을 설정 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 986f4b0da7254ebac3725a704f32af785c72fbcc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79244668"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>사설 클라우드에 대 한 방화벽 테이블 및 규칙 설정

방화벽 테이블 및 관련 규칙을 사용 하 여 특정 서브넷 및 Vlan에 적용할 트래픽에 대 한 제한을 지정할 수 있습니다.

* 서브넷은 하나의 방화벽 테이블에 연결할 수 있습니다.
* 방화벽 테이블은 여러 서브넷에 연결할 수 있습니다.

## <a name="add-a-new-firewall-table"></a>새 방화벽 테이블 추가

1. [CloudSimple 포털에 액세스](access-cloudsimple-portal.md) 하 고 측면 메뉴에서 **네트워크** 를 선택 합니다.
2. **방화벽 테이블**을 선택 합니다.
3. **방화벽 테이블 만들기**를 선택 합니다.

    ![VLAN/서브넷 페이지](media/firewall-tables-page.png)

4. 테이블의 이름을 입력 합니다.
5. 테이블에 대 한 기본 규칙이 나열 됩니다. **새 규칙 만들기** 를 클릭 하 여 추가 규칙을 만듭니다. 자세한 내용은 다음 절차를 참조 하세요.
6. **완료** 를 클릭 하 여 방화벽 테이블을 저장 합니다.

> [!IMPORTANT]
> 사설 클라우드 당 최대 2 개의 방화벽 테이블을 만들 수 있습니다.

## <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙은 방화벽이 특정 유형의 트래픽을 처리 하는 방법을 결정 합니다. 선택한 방화벽 테이블의 **규칙** 탭에는 연결 된 모든 규칙이 나열 됩니다.

![방화벽 규칙 표](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>방화벽 규칙 만들기

1. 다음 방법 중 하나로 방화벽 규칙을 만드는 설정을 표시 합니다.
    * 방화벽 테이블을 만들 때 **규칙 추가** 를 클릭 합니다.
    * **네트워크 > 방화벽 테이블** 페이지에서 특정 방화벽 테이블을 선택 하 고 **새 방화벽 규칙 만들기**를 클릭 합니다.
2. 규칙을 다음과 같이 설정 합니다.
    * **이름**. 규칙에 이름을 지정 합니다.
    * **우선 순위**. 규칙에 우선 순위를 할당 합니다. 숫자가 낮은 규칙이 먼저 실행 됩니다.
    * **트래픽 유형**입니다. 규칙이 사설 클라우드, 인터넷 또는 VPN 트래픽 (상태 비저장) 인지 또는 공용 IP 주소 (상태 저장)에 대 한 것인지 선택 합니다.
    * **프로토콜**. 규칙이 적용 되는 프로토콜 (TCP, UDP 또는 any protocol)을 선택 합니다.
    * **방향**입니다. 인바운드 또는 아웃 바운드 트래픽에 대 한 규칙 인지 여부를 선택 합니다. 인바운드 및 아웃 바운드 트래픽에 대 한 별도의 규칙을 정의 해야 합니다.
    * **작업**. 규칙이 일치 하는 경우 수행할 작업을 선택 합니다 (허용 또는 거부).
    * **원본** - 규칙이 적용 되는 원본 (CIDR 블록, 내부 또는 모든 원본)을 지정 합니다.
    * **원본 포트 범위**입니다. 규칙에 적용 되는 포트 범위를 지정 합니다.
    * **방향**입니다. 인바운드 또는 아웃 바운드를 선택 합니다.
    * **대상**. 규칙이 적용 되는 대상 (CIDR 블록, 내부 또는 모든 원본)을 지정 합니다.
    * **원본 포트 범위**입니다. 규칙에 적용 되는 포트 범위를 지정 합니다.

    ![방화벽 테이블 규칙 추가](media/firewall-rule-create.png)

3. **완료** 를 클릭 하 여 규칙을 저장 하 고 방화벽 테이블의 규칙 목록에 추가 합니다.

> [!IMPORTANT]
> 각 방화벽 테이블에는 최대 10 개의 인바운드 규칙과 20 아웃 바운드 규칙이 있을 수 있습니다. [지원에 문의](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)하 여 이러한 제한을 늘릴 수 있습니다.

## <a name="attach-vlans-subnet"></a>Vlan/서브넷 연결

방화벽 테이블을 정의한 후에는 테이블의 규칙에 적용 되는 서브넷을 지정할 수 있습니다.

1. **네트워크** > **방화벽 테이블** 페이지에서 방화벽 테이블을 선택 합니다.
2. **연결 된 vlan/서브넷** 탭을 엽니다.
3. **VLAN/서브넷에 연결을**클릭 합니다.
4. 사설 클라우드 및 VLAN을 선택 합니다. 연결 된 서브넷 이름 및 CIDR 블록이 표시 됩니다.
5. **제출**을 클릭합니다.
