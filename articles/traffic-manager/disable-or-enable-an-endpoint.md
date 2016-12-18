---
title: "Traffic Manager 끝점 사용 안 함 또는 사용 | Microsoft 문서"
description: "이 문서는 트래픽 관리자 프로필 끝점을 사용하거나 사용하지 않도록 설정하는 데 도움이 됩니다."
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 9b2264ce-be06-43b2-a00b-5c724e5d71fd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a3c8d9f5995ac51b128e46691962b5f6a220ab7d


---
<!-- repub for nofollow -->

# <a name="disable-or-enable-a-traffic-manager-endpoint"></a>트래픽 관리자 끝점 사용 안 함 또는 사용
트래픽 관리자 프로필의 일부인 개별 끝점을 사용하지 않도록 설정할 수도 있습니다. 끝점에는 클라우드 서비스와 웹 사이트가 둘 다 포함됩니다. 끝점을 사용하지 않도록 설정하는 경우 프로필의 일부로 유지되지만 끝점이 없는 것처럼 프로필이 작동합니다. 이 작업은 유지 관리 모드이거나 다시 배포할 예정인 끝점을 일시적으로 제거하는 데 매우 유용합니다. 끝점이 다시 작동하여 실행되면 사용하도록 설정할 수 있습니다.

> [!NOTE]
> **끝점을 해제해도 Azure의 배포 상태에는 영향을 주지 않습니다. 정상 끝점은 실행 상태로 유지되며 트래픽 관리자에서 사용하지 않도록 설정된 경우에도 트래픽을 수신할 수 있습니다. 또한 한 프로필에서 끝점을 해제 해도 다른 프로필의 끝점 상태는 변경 되지 않습니다.**.
> 
> 

## <a name="to-disable-an-endpoint"></a>끝점을 사용하지 않도록 설정하려면
1. Azure 클래식 포털의 트래픽 관리자 창에서 수정할 끝점 설정이 포함된 트래픽 관리자 프로필을 찾은 다음 프로필 이름 옆에 있는 화살표를 클릭합니다. 프로필에 대한 설정 페이지가 열립니다.
2. 페이지 맨 위에서 **끝점** 을 클릭하여 구성에 포함된 끝점을 확인합니다.
3. 사용하지 않도록 설정할 끝점을 클릭한 다음 페이지 맨 아래에서 **사용 안 함** 을 클릭합니다.
4. 트래픽 관리자 도메인 이름에 대해 구성된 DNS TTL(Time-To-Live)에 따라 트래픽이 더 이상 끝점으로 흐르지 않습니다. 트래픽 관리자 프로필의 구성 페이지에서 TTL을 변경할 수 있습니다.

## <a name="to-enable-an-endpoint"></a>끝점을 사용하도록 설정하려면
1. Azure 클래식 포털의 트래픽 관리자 창에서 수정할 끝점 설정이 포함된 트래픽 관리자 프로필을 찾은 다음 프로필 이름 옆에 있는 화살표를 클릭합니다. 프로필에 대한 설정 페이지가 열립니다.
2. 페이지 맨 위에서 **끝점** 을 클릭하여 구성에 포함된 끝점을 확인합니다.
3. 사용하도록 설정할 끝점을 클릭한 다음 페이지 맨 아래에서 **사용** 을 클릭합니다.
4. 프로필에 지정된 대로 트래픽이 다시 서비스로 흐르기 시작합니다.

## <a name="next-steps"></a>다음 단계
[트래픽 관리자 - 프로필 사용 안 함, 사용 또는 삭제](disable-enable-or-delete-a-profile.md)

[트래픽 관리자 성능 저하 상태 문제 해결](traffic-manager-troubleshooting-degraded.md)

[트래픽 관리자 성능 고려 사항](traffic-manager-performance-considerations.md)




<!--HONumber=Nov16_HO3-->


