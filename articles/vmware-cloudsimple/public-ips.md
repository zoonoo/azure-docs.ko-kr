---
title: Azure VMware 솔루션 (AVS)-공용 IP 주소 할당
description: AVS 사설 클라우드 환경에서 가상 머신에 대 한 공용 IP 주소를 할당 하는 방법을 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 87133f5efb9f096d3fdb0956aab1caac58b4bd94
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024299"
---
# <a name="allocate-public-ip-addresses-for-avs-private-cloud-environment"></a>AVS 사설 클라우드 환경에 대 한 공용 IP 주소 할당

네트워크 페이지에서 공용 IP 탭을 열어 AVS 사설 클라우드 환경의 가상 컴퓨터에 대 한 공용 IP 주소를 할당 합니다.

1. [AVS 포털에 액세스](access-cloudsimple-portal.md) 하 고 측면 메뉴에서 **네트워크** 를 선택 합니다.
2. **공용 ip**를 선택 합니다.
3. **새 공용 IP**를 클릭 합니다.

    ![공용 Ip 페이지](media/public-ips-page.png)

4. IP 주소 항목을 식별 하는 이름을 입력 합니다.
5. 기본 위치를 유지 합니다.
6. 필요한 경우 슬라이더를 사용 하 여 유휴 시간 제한을 변경 합니다.
7. 공용 IP 주소를 할당 하려는 로컬 IP 주소를 입력 합니다.
8. 연결 된 DNS 이름을 입력 합니다.
9. **제출**을 클릭합니다.

![공용 Ip 할당](media/network-public-ip-allocate.png)

공용 IP 주소를 할당 하는 작업이 시작 됩니다. 작업 **> 작업** 페이지에서 작업의 상태를 확인할 수 있습니다. 할당이 완료 되 면 공용 Ip 페이지에 새 항목이 표시 됩니다.
