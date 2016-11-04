---
title: Azure 포털을 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치 만들기 시작 | Microsoft Docs
description: Azure 포털을 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''
tags: azure-service-management

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/17/2016
ms.author: sewhee

---
# Azure 포털에서 인터넷 연결 부하 분산 장치(클래식) 만들기 시작
[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Azure 포털을 사용하여 부하 분산 장치 끝점 만들기 시작
Azure 포털에서 인터넷 연결 부하 분산 장치(클래식) 배포 모델을 만들려면 다음 단계를 수행합니다.

1. 브라우저에서 http://portal.azure.com으로 이동하고 필요한 경우 Azure 계정으로 로그인합니다.
2. 가상 컴퓨터(클래식) 블레이드로 이동하여 가상 컴퓨터를 선택합니다.
3. 가상 컴퓨터 "essentials" 블레이드에서 "모든 설정"을 선택합니다.
4. "부하 분산된 집합"을 클릭합니다.
5. 새 부하 분산 장치를 만들려면 부하 분산 집합 블레이드의 상단에 있는 "join" 아이콘을 클릭합니다.
6. 인터넷 연결 부하 분산 장치를 위해 공개된 "부하 분산된 집합 유형"을 선택합니다.
7. "필요한 설정 구성"을 클릭하여 "부하 분산 집합 선택"을 열고 "부하 분산 집합 만들기"를 클릭합니다.
8. "부하 분산 집합 만들기" 블레이드에서 부하 분산 장치 집합의 이름을 만듭니다. 이름, 공용 포트, 프로브 프로토콜, 프로브 포트를 작성합니다.
9. 필요한 경우 프로브 간격 및 재시도 횟수를 변경합니다.
10. (선택 사항) 필요한 경우, 부하 분산 장치 집합 생성 블레이드에서 액세스 제어 규칙을 구성할 수 있습니다.
11. 확인을 클릭하여 "부하 분산된 집합에 조인" 블레이드로 돌아갑니다.
12. 확인을 클릭하여 "부하 분산 장치 집합" 블레이드에 표시할 새 부하 분산 장치 리소스를 기다립니다.

## 다음 단계
[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_0824_2016-->