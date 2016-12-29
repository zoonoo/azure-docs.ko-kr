---
title: "Traffic Manager 라우팅 메서드 구성 | Microsoft Docs"
description: "이 문서에서는 Traffic Manager에서 여러 가지 라우팅 방법을 구성하는 방법을 설명합니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: 6dca6de1-18f7-4962-bd98-6055771fab22
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: d4481115dbf8cc720019096969c55509f33d250c

---

# <a name="configure-traffic-manager-routing-methods"></a>Traffic Manager 라우팅 방법

Azure Traffic Manager는 트래픽이 사용 가능한 서비스 끝점으로 라우팅되는 세 가지 라우팅 방법을 제공합니다. 트래픽 라우팅 방법은 DNS 응답에 반환될 끝점을 결정하기 위해 수신된 각 DNS 쿼리에 적용됩니다.

트래픽 관리자에서 사용할 수 있는 세 가지 트래픽 라우팅 방법이 있습니다.

* **우선 순위:** 기본 서비스 끝점을 사용하고 기본 끝점을 사용할 수 없을 때 백업을 제공하려면 '우선 순위'를 선택합니다.
* **가중:** 여러 끝점에 균일하게 또는 정의한 가중치에 따라 트래픽을 분산하려면 '가중'을 선택합니다.
* **성능:** 끝점이 서로 다른 지역에 있고 최종 사용자가 가장 짧은 네트워크 대기 시간을 기준으로 "가장 가까운" 끝점을 사용하게 하려는 경우 성능을 선택합니다.

## <a name="configure-priority-routing-method"></a>우선 순위 라우팅 방법 구성

웹 사이트 모드에 관계없이 Azure Websites는 데이터 센터(지역이라고도 함) 내의 웹 사이트에 대해 이미 장애 조치(Failover) 기능을 제공합니다. Traffic Manager는 다른 데이터 센터의 웹 사이트에 대해 장애 조치(Failover)를 제공합니다.

일반적인 서비스 장애 조치(Failover) 패턴은 기본 서비스에 트래픽을 전송하고 장애 조치(Failover)에 대한 일련의 동일한 백업 서비스를 제공하는 것입니다. 다음 단계에서는 Azure Cloud Services 및 Websites를 사용하여 이 우선 순위가 지정된 장애 조치(Failover)를 구성하는 방법을 설명합니다.

1. Azure 클래식 포털의 왼쪽 창에서 **트래픽 관리자** 아이콘을 클릭하여 트래픽 관리자 창을 엽니다.
2. Azure 클래식 포털의 Traffic Manager 창에서 수정할 설정이 포함된 Traffic Manager 프로필을 찾습니다. 프로필 설정 페이지를 열려면 프로필 이름 오른쪽에 있는 화살표를 클릭합니다.
3. 프로필 페이지에서 페이지 맨 위의 **끝점**을 클릭합니다. 구성에 포함할 Cloud Services와 Websites가 둘 다 있는지 확인합니다.
4. 맨 위의 **구성** 을 클릭하여 구성 페이지를 엽니다.
5. **트래픽 라우팅 방법 설정**에서 트래픽 라우팅 방법이 **장애 조치(Failover)**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **장애 조치(Failover)** 를 클릭합니다.
6. **장애 조치(Failover) 우선 순위 목록**에서 끝점에 대한 장애 조치(Failover) 순서를 조정합니다. **장애 조치(Failover)** 트래픽 라우팅 방법을 선택하는 경우 선택한 끝점의 순서가 중요합니다. 기본 끝점이 맨 위에 있습니다. 위쪽 및 아래쪽 화살표를 사용하여 필요에 따라 순서를 변경합니다. Windows PowerShell을 사용하여 장애 조치(Failover) 우선 순위를 설정하는 방법에 대한 자세한 내용은 [Set-AzureTrafficManagerProfile](http://go.microsoft.com/fwlink/p/?LinkId=400880)을 참조하세요.
7. **모니터링 설정** 이 적절하게 구성되었는지 확인합니다. 모니터링은 오프라인 상태인 끝점으로 트래픽이 전송되지 않도록 합니다. 끝점을 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
8. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장** 을 클릭합니다.
9. 구성 변경 내용을 테스트합니다.
10. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

## <a name="configure-weighted-routing-method"></a>가중 라우팅 방법 구성

일반적인 트래픽 라우팅 방법 패턴은 클라우드 서비스와 웹 사이트를 포함하는 일련의 동일한 끝점을 제공하고 각 끝점에 라운드 로빈 방식으로 트래픽을 전송하는 것입니다. 다음 단계에서는 이 유형의 트래픽 라우팅 방법을 구성하는 방법을 간략하게 설명합니다.

> [!NOTE]
> Azure Websites는 데이터 센터(지역이라고도 함) 내의 웹 사이트에 대해 이미 라운드 로빈 부하 분산 기능을 제공합니다. 트래픽 관리자를 통해 다른 데이터 센터의 웹 사이트에 대해 라운드 로빈 트래픽 라우팅 방법을 지정할 수 있습니다.

1. Azure 클래식 포털의 왼쪽 창에서 **트래픽 관리자** 아이콘을 클릭하여 트래픽 관리자 창을 엽니다.
2. Traffic Manager 창에서 수정할 설정이 포함된 Traffic Manager 프로필을 찾습니다. 프로필 설정 페이지를 열려면 프로필 이름 오른쪽에 있는 화살표를 클릭합니다.
3. 프로필 페이지에서 페이지 맨 위의 **끝점** 을 클릭하고 구성에 포함할 서비스 끝점이 있는지 확인합니다.
4. 프로필 페이지에서 맨 위의 **구성** 을 클릭하여 구성 페이지를 엽니다.
5. **트래픽 라우팅 방법 설정**에서 트래픽 라우팅 방법이 **라운드 로빈**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **라운드 로빈** 을 클릭합니다.
6. **모니터링 설정** 이 적절하게 구성되었는지 확인합니다. 모니터링은 오프라인 상태인 끝점으로 트래픽이 전송되지 않도록 합니다. 끝점을 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
7. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장** 을 클릭합니다.
8. 구성 변경 내용을 테스트합니다.
9. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

## <a name="configure-performance-traffic-routing-method"></a>성능 트래픽 라우팅 방법 구성

성능 트래픽 라우팅 방법을 통해 클라이언트 네트워크에서 대기 시간이 가장 짧은 끝점으로 트래픽을 전송할 수 있습니다. 일반적으로 대기 시간이 가장 짧은 데이터 센터는 지리적 거리가 가장 가까운 데이터 센터입니다. 이 트래픽 라우팅 방법은 네트워크 구성이나 로드의 실시간 변경 내용을 고려할 수 없습니다.

1. Azure 클래식 포털의 왼쪽 창에서 **트래픽 관리자** 아이콘을 클릭하여 트래픽 관리자 창을 엽니다.
2. Traffic Manager 창에서 수정할 설정이 포함된 Traffic Manager 프로필을 찾습니다. 프로필 설정 페이지를 열려면 프로필 이름 오른쪽에 있는 화살표를 클릭합니다.
3. 프로필 페이지에서 페이지 맨 위의 **끝점** 을 클릭하고 구성에 포함할 서비스 끝점이 있는지 확인합니다.
4. 프로필 페이지에서 맨 위의 **구성** 을 클릭하여 구성 페이지를 엽니다.
5. **트래픽 라우팅 메서드 설정**에서 트래픽 라우팅 메서드가 **성능**인지 확인합니다. 아닌 경우 드롭다운 목록에서 **성능**을 클릭합니다.
6. **모니터링 설정** 이 적절하게 구성되었는지 확인합니다. 모니터링은 오프라인 상태인 끝점으로 트래픽이 전송되지 않도록 합니다. 끝점을 모니터링하려면 경로와 파일 이름을 지정해야 합니다. 슬래시 "/"는 상대 경로에 유효한 입력이며 파일이 루트 디렉터리(기본값)에 있음을 나타냅니다.
7. 구성 변경을 완료한 후 페이지 맨 아래에서 **저장** 을 클릭합니다.
8. 구성 변경 내용을 테스트합니다.
9. Traffic Manager 프로필이 작동하면 회사 도메인 이름이 Traffic Manager 도메인 이름을 가리키도록 권한 있는 DNS 서버의 DNS 레코드를 편집합니다.

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 프로필 관리](traffic-manager-manage-profiles.md)
* [트래픽 관리자 라우팅 방법](traffic-manager-routing-methods.md)
* [Traffic Manager 설정 테스트](traffic-manager-testing-settings.md)
* [회사 인터넷 도메인이 트래픽 관리자 도메인을 가리키도록 설정](traffic-manager-point-internet-domain.md)
* [Traffic Manager 끝점 관리](traffic-manager-manage-endpoints.md)
* [트래픽 관리자 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)




<!--HONumber=Nov16_HO5-->


