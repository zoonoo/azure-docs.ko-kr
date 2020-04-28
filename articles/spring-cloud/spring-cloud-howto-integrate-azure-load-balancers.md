---
title: 자습서-azure 부하 분산 솔루션과 Azure 스프링 클라우드 통합
description: Azure 부하 분산 솔루션과 Azure 스프링 클라우드를 통합 하는 방법
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.openlocfilehash: 7022c4587b425168fc5bd2182ed65c281633aabf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/27/2020
ms.locfileid: "82177082"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Azure 부하 분산 솔루션과 Azure 스프링 클라우드 통합

Azure 스프링 클라우드는 Azure에서 마이크로 서비스를 지원 합니다.  비즈니스를 높이면 Azure 스프링 클라우드의 여러 인스턴스를 관리 하는 여러 데이터 센터가 필요할 수 있습니다.

Azure는 이미 다른 부하 분산 솔루션을 제공 합니다. Azure 스프링 클라우드를 Azure 부하 분산 솔루션과 통합 하는 세 가지 옵션이 있습니다.

1.  Azure Traffic Manager와 Azure 스프링 클라우드 통합
2.  Azure 앱 Gateway와 Azure 스프링 클라우드 통합
3.  Azure 프런트 도어를 사용 하 여 Azure 스프링 클라우드 통합

## <a name="prerequisites"></a>사전 요구 사항

* Azure 스프링 클라우드: [azure 스프링 클라우드 서비스를 만드는 방법](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-launch-app-portal)
* Azure Traffic Manager: [Traffic Manager를 만드는 방법](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* Azure 앱 Gateway: [응용 프로그램 게이트웨이를 만드는 방법](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* Azure Front 도어: [Front 도어를 만드는 방법](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Azure Traffic Manager와 Azure 스프링 클라우드 통합

Azure 스프링 클라우드를 Traffic Manager와 통합 하려면 해당 공용 끝점을 traffic manager의 끝점으로 추가한 다음 traffic manager와 Azure 스프링 클라우드 모두에 대해 사용자 지정 도메인을 구성 합니다.

### <a name="add-endpoint-in-traffic-manager"></a>Traffic Manager에 끝점 추가
Traffic manager에서 끝점 추가:
1.  *외부 끝점이*될 **형식을** 지정 합니다.
1.  각 Azure 스프링 클라우드 공용 끝점의 FQDN (정규화 된 도메인 이름)을 입력 합니다.
1. **확인**을 클릭합니다.

    ![Traffic Manager 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>사용자 지정 도메인 구성
구성을 완료 하려면:
1.  도메인 공급자의 웹 사이트에 로그인 하 고 사용자 지정 도메인에서 traffic manager의 Azure 기본 도메인 이름으로 CNAME 레코드 매핑을 만듭니다.
1.  [Azure 스프링 클라우드에 사용자 지정 도메인을 추가 하는 방법에 대 한 지침을](spring-cloud-tutorial-custom-domain.md)따르세요.
1. 위의 사용자 지정 도메인 바인딩을 traffic manager에 추가 하 여 Azure 스프링 클라우드 해당 app service에 추가 하 고 여기에 SSL 인증서를 업로드 합니다.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Azure 앱 Gateway와 Azure 스프링 클라우드 통합

Azure 스프링 클라우드 서비스와 통합 하려면 다음 구성을 완료 합니다.

### <a name="configure-backend-pool"></a>백 엔드 풀 구성
1. **대상 유형을** *IP 주소* 또는 *FQDN*으로 지정 합니다.
1. Azure 스프링 클라우드 공용 끝점을 입력 합니다.

    ![앱 게이트웨이 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>사용자 지정 프로브 추가
1. **상태 프로브** 를 선택 하 고 **추가** 를 선택 하 여 사용자 지정 **프로브** 대화 상자를 엽니다. 
1. 핵심 요소는 **백 엔드 HTTP 설정에서 호스트 이름 선택** 옵션에 대해 *예* 를 선택 하는 것입니다.

    ![앱 게이트웨이 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Http 설정 구성
1.  Http **설정** 을 선택 하 고 **추가** 를 선택 하 여 http 설정을 추가 합니다.
1.  **새 호스트 이름으로 재정의:** *예*를 선택 합니다.
1.  **호스트 이름 재정의**: **백 엔드 대상에서 호스트 이름 선택을**선택 합니다.
1.  **사용자 지정 프로브 사용**: *예* 를 선택 하 고 위에서 만든 사용자 지정 프로브를 선택 합니다.

    ![앱 게이트웨이 3](media/spring-cloud-load-balancers/app-gateway-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Azure 프런트 도어를 사용 하 여 Azure 스프링 클라우드 통합

Azure 스프링 클라우드 서비스와 통합 하 고 백 엔드 풀을 구성 하려면 
1. **백 엔드 풀을 추가**합니다.
1. 호스트를 추가 하 여 백 엔드 끝점을 지정 합니다.

    ![전면 도어 1](media/spring-cloud-load-balancers/front-door-1.png)

1.  **백 엔드 호스트 유형을** *사용자 지정 호스트로*지정 합니다.
1.  **백 엔드 호스트 이름**에 Azure 스프링 클라우드 공용 끝점의 FQDN을 입력 합니다.
1.  백 엔드 호스트 **이름과**같은 **백 엔드 호스트 헤더** 기본값을 적용 합니다.

    ![전면 도어 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>다음 단계
* [Traffic manager를 만드는 방법](https://docs.microsoft.com/azure/traffic-manager/quickstart-create-traffic-manager-profile/)
* [응용 프로그램 게이트웨이를 만드는 방법](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)
* [프런트 도어를 만드는 방법](https://docs.microsoft.com/azure/frontdoor/quickstart-create-front-door)
