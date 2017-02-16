---
title: "Microsoft Azure 트래픽 관리자 프로필 | Microsoft Docs"
description: "이 문서는 Azure Traffic Manager 프로필의 기록을 만들고, 사용하거나 사용하지 않도록 설정하며, 삭제하고 표시하는 데 도움이 됩니다."
services: traffic-manager
documentationcenter: 
author: kumudd
manager: timlt
editor: 
ms.assetid: f06e0365-0a20-4d08-b7e1-e56025e64f66
ms.service: traffic-manager
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 7e7de7dc1eca6903403afef03fdd6afb98ff16c9

---

# <a name="manage-an-azure-traffic-manager-profile"></a>Azure 트래픽 관리자 프로필 관리

Traffic Manager 프로필에서는 트래픽 라우팅 방법을 사용하여 클라우드 서비스나 웹 사이트 끝점에 대한 트래픽의 배포를 제어합니다. 이 문서에서는 이러한 프로필을 만들고 관리하는 방법을 설명합니다.

## <a name="create-a-traffic-manager-profile-using-quick-create"></a>빠른 생성을 사용하여 트래픽 관리자 프로필 만들기

Azure 클래식 포털의 빠른 생성에서 트래픽 관리자 프로필을 신속하게 만들 수 있습니다. 빠른 생성을 사용하면 기본 구성 설정으로 프로필을 만들 수 있습니다. 그러나 빠른 생성은 끝점(클라우드 서비스 및 웹 사이트), 장애 조치 트래픽 라우팅 방법에 대한 장애 조치 순서 또는 모니터링 설정과 같은 설정에는 사용할 수 없습니다. 프로필을 만든 후 Azure 클래식 포털에서 이러한 설정을 구성할 수 있습니다. 트래픽 관리자는 프로필당 최대 200개의 끝점을 지원합니다. 하지만 대부분의 사용 시나리오에는 적은 수의 끝점만 필요합니다.

### <a name="to-create-a-traffic-manager-profile"></a>Traffic Manager 프로필을 만들려면

1. **프로덕션 환경으로 클라우드 서비스 및 웹 사이트를 배포합니다.** 클라우드 서비스에 대한 자세한 내용은 [클라우드 서비스](http://go.microsoft.com/fwlink/p/?LinkId=314074)를 참조하세요. 웹 사이트에 대한 자세한 내용은 [웹 사이트](http://go.microsoft.com/fwlink/p/?LinkId=393327)를 참조하세요.
2. **Azure 클래식 포털에 로그인합니다.** 포털의 왼쪽 아래에서 **새로 만들기**, **Network Services > Traffic Manager**를 차례로 클릭한 다음 **빨리 만들기**를 클릭하여 프로필 구성을 시작합니다.
3. **DNS 접두사를 구성합니다.**  트래픽 관리자 프로필에 고유한 DNS 접두사 이름을 제공합니다. 트래픽 관리자 도메인 이름에 대한 접두사만 지정할 수 있습니다.
4. **구독을 선택합니다.**  적절한 Azure 구독을 선택합니다. 각 프로필은 단일 구독과 연결됩니다. 하나의 구독만 있는 경우 이 옵션이 표시되지 않습니다.
5. **트래픽 라우팅 방법을 선택합니다.** **트래픽 라우팅 정책**에서 트래픽 라우팅 방법을 선택합니다. 트래픽 라우팅 방법에 대한 자세한 내용은 [트래픽 관리자 트래픽 라우팅 방법 정보](traffic-manager-routing-methods.md)를 참조하세요.
6. **“만들기”를 클릭하여 프로필을 만듭니다**. 프로필 구성이 완료되면 Azure 클래식 포털에 있는 트래픽 관리자 창에서 프로필을 찾을 수 있습니다.
7. **Azure 클래식 포털에서 끝점, 모니터링 및 추가 설정을 구성합니다.** 빨리 만들기를 사용하면 기본 설정만 구성합니다. 끝점 목록, 끝점 장애 조치 순서와 같은 설정을 추가로 구성할 필요가 있습니다.

## <a name="disable-enable-or-delete-a-profile"></a>프로필 사용 안 함, 사용 또는 삭제

기존 프로필을 사용하지 않도록 설정하면 Traffic Manager에서 구성된 끝점의 사용자 요청을 참조하지 않을 수 있습니다. Traffic Manager 프로필을 사용하지 않도록 설정하면 프로필 및 프로필에 포함된 정보는 그대로 유지되며 Traffic Manager 인터페이스에서 편집할 수 있습니다.  프로필을 다시 사용하도록 설정하는 경우 조회가 다시 시작됩니다. Azure 클래식 포털에서 Traffic Manager 프로필을 만들면 자동으로 사용하도록 설정됩니다. 프로필이 더 이상 필요하지 않으면 해당 프로필을 삭제할 수 있습니다.

### <a name="to-disable-a-profile"></a>프로필을 사용하지 않도록 설정하려면

1. 사용자 지정 도메인 이름을 사용하고 있는 경우 Traffic Manager 프로필을 더 이상 가리키지 않도록 인터넷 DNS 서버에서 CNAME 레코드를 변경합니다.
2. Traffic Manager 프로필 설정을 통해 끝점에 보내는 트래픽이 중지됩니다.
3. 사용하지 않도록 설정할 프로필을 선택합니다. Traffic Manager 페이지에서 프로필 이름 옆에 있는 열을 클릭하여 해당 프로필을 강조 표시합니다. 참고로 프로필 이름 또는 이름 옆의 화살표를 클릭하면 프로필 설정 페이지가 열립니다.
4. 프로필을 선택한 후 페이지의 맨 아래에서 **사용 안 함** 을 클릭합니다.

### <a name="to-enable-a-profile"></a>프로필을 사용하려면

1. 사용하지 않도록 설정할 프로필을 선택합니다. Traffic Manager 페이지에서 프로필 이름 옆에 있는 열을 클릭하여 해당 프로필을 강조 표시합니다. 참고로 프로필 이름 또는 이름 옆의 화살표를 클릭하면 프로필 설정 페이지가 열립니다.
2. 프로필을 선택한 후 페이지의 맨 아래에서 **사용** 을 클릭합니다.
3. 사용자 지정 도메인 이름을 사용하고 있는 경우 Traffic Manager 프로필의 도메인 이름을 가리키도록 인터넷 DNS 서버에 CNAME 리소스 레코드를 만듭니다.
4. 트래픽이 끝점에 다시 전달됩니다.

### <a name="to-delete-a-profile"></a>프로필을 삭제하려면

1. 인터넷 DNS 서버의 DNS 리소스 레코드에서 트래픽 관리자 프로필의 도메인 이름을 가리키는 CNAME 리소스 레코드를 더 이상 사용하지 않는지 확인합니다.
2. 사용하지 않도록 설정할 프로필을 선택합니다. Traffic Manager 페이지에서 프로필 이름 옆에 있는 열을 클릭하여 해당 프로필을 강조 표시합니다. 참고로 프로필 이름 또는 이름 옆의 화살표를 클릭하면 프로필 설정 페이지가 열립니다.
3. 프로필을 선택한 후 페이지의 맨 아래에서 **삭제** 를 클릭합니다.

## <a name="view-traffic-manager-profile-change-history"></a>트래픽 관리자 프로필 변경 기록 확인

Azure 클래식 포털의 관리 서비스에서 트래픽 관리자 프로필의 변경 기록을 확인할 수 있습니다.

### <a name="to-view-your-traffic-manager-change-history"></a>트래픽 관리자 변경 기록을 보려면

1. Azure 클래식 포털의 왼쪽 창에서 **관리 서비스**를 클릭합니다.
2. 관리 서비스 페이지에서 **작업 로그**를 클릭합니다.
3. 작업 로그 페이지에서 필터링을 통해 트래픽 관리자 프로필의 변경 기록을 확인할 수 있습니다. 필터링 옵션을 선택한 후 확인 표시를 클릭하여 결과를 확인합니다.

   * 모든 프로필의 프로필 변경 내용을 확인하려면 구독 및 시간 범위를 선택하고 **형식** 바로 가기 메뉴에서 **Traffic Manager**를 선택합니다.
   * 프로필 이름으로 필터링하려면 **서비스 이름** 필드에 프로필 이름을 입력하거나 바로 가기 메뉴에서 프로필을 선택합니다.
   * 각 개별 변경 내용을 자세히 보려면 변경된 행을 선택하고 페이지 맨 아래에서 **세부 정보** 를 클릭합니다. **작업 세부 정보** 창에서 작업의 일부로 작성되거나 업데이트된 API 개체의 XML 표현을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [끝점 추가](traffic-manager-endpoints.md)
* [장애 조치(Failover) 라우팅 방법 구성](traffic-manager-configure-failover-routing-method.md)
* [라운드 로빈 라우팅 방법 구성](traffic-manager-configure-round-robin-routing-method.md)
* [성능 라우팅 방법 구성](traffic-manager-configure-performance-routing-method.md)
* [회사 인터넷 도메인이 Traffic Manager 도메인 이름을 가리키도록 설정](traffic-manager-point-internet-domain.md)
* [트래픽 관리자 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)



<!--HONumber=Nov16_HO5-->


