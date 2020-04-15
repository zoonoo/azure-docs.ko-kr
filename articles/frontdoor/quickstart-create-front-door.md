---
title: '빠른 시작: 애플리케이션의 고가용성을 위한 Front Door 프로필 만들기'
description: 이 빠른 시작 문서에는 고가용성, 고성능 글로벌 웹 애플리케이션을 위한 Front Door를 만드는 방법을 설명합니다.
services: front-door
documentationcenter: ''
author: sharad4u
editor: ''
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2018
ms.author: sharadag
ms.openlocfilehash: 2b44c0cdbe2d955efe20a5f9473a29bc9f500a07
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521461"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application"></a>빠른 시작: 고가용성 글로벌 웹 애플리케이션에 대한 Front Door 만들기

이 빠른 시작에서는 글로벌 웹 애플리케이션에 고가용성 및 고성능을 제공하는 Front Door 프로필을 만드는 방법을 설명합니다. 

이 빠른 시작에 설명된 시나리오에는 서로 다른 Azure 지역에서 실행되는 두 개의 웹 애플리케이션 인스턴스가 포함되어 있습니다. 동등한 [가중치 및 동일한 우선 순위 백 엔드](front-door-routing-methods.md)를 기반으로 애플리케이션을 실행하는 가장 가까운 사이트 백 엔드에 사용자 트래픽을 보내는 Front Door 구성이 만들어집니다. Front Door는 지속적으로 웹 애플리케이션을 모니터링하다가 가장 가까운 사이트를 사용할 수 없게 되면 그 다음 가용 백업 사이트로 자동 장애 조치(failover)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인 
[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="prerequisites"></a>사전 요구 사항
이 빠른 시작을 진행하려면 서로 다른 Azure 지역(*미국 동부* 및 *서유럽*)에서 실행되는 두 개의 웹 애플리케이션 인스턴스를 배포해야 합니다. 두 웹 애플리케이션 인스턴스 모두 활성/활성 모드에서 실행됩니다. 즉, 둘 중 하나가 장애 조치(failover) 역할을 하는 능동/대기 모드와는 달리 둘 중 하나가 언제든지 트래픽을 받을 수 있습니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** > **웹** > **웹앱**을 차례로 선택합니다.
2. **웹앱**에서, 다음 정보를 입력 또는 선택하고, 지정된 설정이 없으면 기본 설정을 입력합니다.

     | 설정         | 값     |
     | ---              | ---  |
     | Resource group          | **새로 만들기**를 선택한 다음, *myResourceGroupFD1*을 입력합니다. |
     | 속성           | 웹앱의 고유한 이름을 입력합니다.  |
     | 런타임 스택          | 앱의 런타임 스택 선택 |
     |      지역  |   미국 서부        |
     | App Service 계획/위치         | **새로 만들기**를 선택합니다.  App Service 계획에서 *myAppServicePlanEastUS*를 입력하고 **확인**을 선택합니다.| 
     |SKU 및 크기  | **크기 변경** 선택 **Standard S1 100 총 ACU, 1.75GB 메모리** 선택 |
     
3. **검토 + 만들기**를 선택합니다.
4. 웹앱에 대한 요약 정보를 검토합니다. **만들기**를 선택합니다.
5. 약 5분 후 웹앱이 성공적으로 배포되면 기본 웹 사이트가 생성됩니다.
6. 1-3 단계를 반복하여 다른 Azure 지역에 다음 설정으로 두 번째 웹 사이트를 만듭니다.

     | 설정         | 값     |
     | ---              | ---  |
     | Resource group          | **새로 만들기**를 선택한 다음, *myResourceGroupFD2*를 입력합니다. |
     | 속성           | 웹앱의 고유한 이름을 입력합니다.  |
     | 런타임 스택          | 앱의 런타임 스택 선택 |
     |      지역  |   서유럽      |
     | App Service 계획/위치         | **새로 만들기**를 선택합니다.  App Service 계획에서 *myAppServicePlanWestEurope*을 입력하고 **확인**을 선택합니다.|   
     |SKU 및 크기  | **크기 변경** 선택 **Standard S1 100 총 ACU, 1.75GB 메모리** 선택 |
    
## <a name="create-a-front-door-for-your-application"></a>애플리케이션에 대한 Front Door 만들기
### <a name="a-add-a-frontend-host-for-front-door"></a>A. Front Door에 대한 프런트 엔드 호스트 추가
두 개의 백 엔드 간의 가장 낮은 대기 시간에 따라 사용자 트래픽을 전달하는 Front Door 구성을 만듭니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Front Door**를 선택합니다.
2. **Front Door 만들기**에서 다음 정보를 입력하거나 선택하고, 지정된 설정이 없으면 기본 설정을 입력합니다.

     | 설정         | 값     |
     | ---              | ---  |
     |Subscription  | Front Door를 만들 구독을 선택합니다.|
     | Resource group          | **새로 만들기**를 선택한 다음, *myResourceGroupFD0*을 입력합니다. |
     | 리소스 그룹 위치  |   미국 중부        |
     
     > [!NOTE]
     > Front Door를 배포하기 위해 새 리소스 그룹을 만들 필요는 없습니다.  기존 리소스 그룹을 선택할 수도 있습니다.
     
3. **다음: 구성**을 선택합니다.
4. 프런트 엔드/도메인 카드에서 '+' 아이콘을 클릭합니다.  **호스트 이름**에 `<Your Initials>frontend`를 입력합니다. 이 호스트 이름은 전역적으로 고유해야 하면 Front Door가 유효성 검사를 처리합니다.
5. **추가**를 클릭합니다.

### <a name="b-add-application-backend-and-backend-pools"></a>B. 애플리케이션 백 엔드 및 백 엔드 풀 추가

다음으로, Front Door의 백 엔드 풀에서 프런트 엔드/도메인 및 애플리케이션 백 엔드를 구성하여 애플리케이션이 있는 위치를 알아야 합니다. 

1. 백 엔드 풀 카드에서 '+' 아이콘을 클릭하여 백 엔드 풀의 **이름**에 대한 백 엔드 풀을 추가하고 `myBackendPool`을 입력합니다.
2. 다음으로, **백 엔드 추가**를 클릭하여 이전에 만든 웹 사이트를 추가합니다.
3. **백 엔드 호스트 유형**을 'App Service'로 선택하고 웹 사이트를 만든 구독을 선택한 다음, **백 엔드 호스트 이름** 드롭다운에서 첫 번째 웹사이트를 선택합니다.
4. 나머지 필드는 그대로 두고 **추가'** 를 클릭합니다.
5. **백 엔드 호스트 유형**을 'App Service'로 선택하고 웹 사이트를 만든 구독을 선택한 다음, **백 엔드 호스트 이름** 드롭다운에서 **두 번째** 웹사이트를 선택합니다.
6. 나머지 필드는 그대로 두고 **추가'** 를 클릭합니다.
7. 필요에 따라 백 엔드 풀의 상태 프로브 및 부하 분산 설정을 업데이트할 수 있지만, 기본값을 사용해도 정상적으로 작동합니다. 두 경우 모두 **추가**를 클릭합니다.


### <a name="c-add-a-routing-rule"></a>C. 라우팅 규칙 추가
1. 마지막으로, 라우팅 규칙 카드에서 '+' 아이콘을 클릭하여 라우팅 규칙을 구성합니다. 이렇게 해야만 프런트 엔드 호스트를 백 엔드 풀에 매핑할 수 있으며, 엄밀하게 말해서 `myappfrontend.azurefd.net`로 요청이 들어오면 백 엔드 풀 `myBackendPool`로 전달하도록 구성하는 것입니다. 
2. **이름**에 'LocationRule'을 입력합니다.
3. **추가**를 클릭하여 Front Door에 대한 회람 규칙을 추가합니다. 
4. **검토 및 만들기**를 클릭합니다.
5. Front Door 생성 설정을 검토합니다. **만들기**

>[!WARNING]
> **반드시** Front Door의 각 프런트 엔드 호스트에 기본 경로('/\*')가 연결된 회람 규칙이 있어야 합니다. 즉, 모든 회람 규칙에서 기본 경로('/\*')에 정의된 각 프런트 엔드 호스트의 회람 규칙이 하나 이상 있어야 합니다. 그렇지 않으면 최종 사용자 트래픽이 올바르게 라우팅되지 않을 수 있습니다.

## <a name="view-front-door-in-action"></a>작동 중인 View Front
View Front를 만든 후 구성이 모든 곳에 글로벌하게 배포될 때까지 몇 분 정도 걸립니다. 배포가 완료되면 앞에서 만든 프런트 엔드 호스트에 액세스합니다. 다시 말해서, 웹 브라우저로 이동하여 `myappfrontend.azurefd.net` URL을 누릅니다. 백 엔드 풀에 지정된 백 엔드에서 가장 가까운 백 엔드로 요청이 자동 라우팅됩니다. 

### <a name="view-front-door-handle-application-failover"></a>Front Door 핸들 애플리케이션 장애 조치(failover) 보기
작동 중인 Front Door의 인스턴트 글로벌 장애 조치(failover)를 테스트하려면 앞에서 만든 웹 사이트 중 하나로 이동하여 웹 사이트를 중지합니다. 백 엔드 풀에 대해 정의된 상태 프로브 설정에 따라, 트래픽을 다른 웹 사이트 배포로 즉시 장애 조치(failover)할 것입니다. Front Door의 백 엔드 풀 구성에서 백 엔드를 사용하지 않도록 설정하여 동작을 테스트할 수도 있습니다. 

## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 **myResourceGroupFD1**, **myResourceGroupFD2** 및 **myResourceGroupFD0** 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 고가용성 및 고성능이 필요한 웹 애플리케이션에 대한 사용자 트래픽을 보낼 수 있도록 Front Door를 만들었습니다. 트래픽 라우팅에 대한 자세한 내용은 Front Door에서 사용하는 [라우팅 방법](front-door-routing-methods.md)을 참조하세요.
