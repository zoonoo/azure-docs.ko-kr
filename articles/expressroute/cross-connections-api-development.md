---
title: Azure 익스프레스루트 크로스콘네션스 API 개발 및 통합
description: 이 문서에서는 expressRouteCrossConnections 리소스 유형에 대한 ExpressRoute 파트너에 대한 자세한 개요를 제공합니다.
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: mialdrid
ms.openlocfilehash: b4a83013d2cbeb2871a3963ec0c95144c02f4d66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77187022"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>익스프레스루트 크로스컨네션 API 개발 및 통합

ExpressRoute 파트너 리소스 관리자 API를 사용하면 ExpressRoute 파트너가 고객 ExpressRoute 회로의 계층 2 및 계층 3 구성을 관리할 수 있습니다. ExpressRoute 파트너 리소스 관리자 API는 새 리소스 유형인 **expressRouteCrossConnections**를 소개합니다. 파트너는 이 리소스를 사용하여 고객 ExpressRoute 회로를 관리합니다.

## <a name="workflow"></a>워크플로

expressRouteCrossConnections 리소스는 익스프레스루트 회로의 그림자 리소스입니다. Azure 고객이 ExpressRoute 회로를 만들고 특정 ExpressRoute 파트너를 선택하면 Microsoft는 파트너의 Azure ExpressRoute 관리 구독에서 expressRouteCrossConnections 리소스를 만듭니다. 이 과정에서 Microsoft는 expressRouteCrossConnections 리소스를 만드는 리소스 그룹을 정의합니다. 리소스 그룹의 명명 표준은 **교차 연결-* 피어링위치***입니다. 피어링위치 = 익스프레스루트 위치입니다. 예를 들어 고객이 덴버에서 ExpressRoute 회로를 만드는 경우 CrossConnection는 다음 리소스 그룹인 **CrossConnnection-Denver에서**파트너의 Azure 구독에 생성됩니다.

ExpressRoute 파트너는 expressRouteCrossConnections 리소스에 대해 REST 작업을 실행하여 계층 2 및 계층 3 구성을 관리합니다.

## <a name="benefits"></a>이점

익스프레스루트크로스연결 리소스로 이동하면 다음과 같은 이점:

* 익스프레스루트 파트너에 대한 향후 개선 사항은 익스프레스루트크로스연결 리소스에서 확인할 수 있습니다.

* 파트너는 expressRouteCrossConnection 리소스에 [역할 기반 액세스 제어를](https://docs.microsoft.com/azure/role-based-access-control/overview) 적용할 수 있습니다. 이러한 컨트롤은 사용자 계정이 expressRouteCrossConnection 리소스를 수정하고 피어링 구성을 추가/업데이트/삭제할 수 있는 권한을 정의할 수 있습니다.

* expressRouteCrossConnection 리소스는 ExpressRoute 연결 문제를 해결하는 데 도움이 될 수 있는 API를 노출합니다. 여기에는 ARP 테이블, BGP 경로 테이블 요약 및 BGP 경로 테이블 세부 정보가 포함됩니다. 이 기능은 클래식 배포 API에서 지원되지 않습니다.

* 파트너는 *RouteFilter* 리소스를 사용하여 Microsoft 피어링에서 보급된 커뮤니티를 조회할 수도 있습니다.

## <a name="api-development-and-integration-steps"></a>API 개발 및 통합 단계

파트너 API에 대해 개발하기 위해 ExpressRoute 파트너는 테스트 고객 및 테스트 파트너 설정을 활용합니다. 테스트 고객 설정은 더미 장치 및 포트에 매핑되는 테스트 피어링 위치에서 ExpressRoute 회로를 만드는 데 사용됩니다. 테스트 파트너 설정은 테스트 피어링 위치에서 생성된 ExpressRoute 회로를 관리하는 데 사용됩니다.

### <a name="1-enlist-subscriptions"></a>1. 가입 신청

테스트 파트너를 요청하고 고객 설정을 테스트하려면 ExpressRoute 엔지니어링 연락처에 두 개의 종량제 Azure 구독을 등록합니다.
* **ExpressRoute_API_Dev_Provider_Sub:** 이 구독은 더미 장치 및 포트의 테스트 피어링 위치에서 생성된 ExpressRoute 회로를 관리하는 데 사용됩니다.

* **ExpressRoute_API_Dev_Customer_Sub:** 이 구독은 더미 장치 및 포트에 매핑되는 테스트 피어링 위치에서 ExpressRoute 회로를 만드는 데 사용됩니다.

테스트 피어링 위치: 더미 장치 및 포트는 기본적으로 프로덕션 고객에게 노출되지 않습니다. 테스트 설정에 매핑되는 ExpressRoute 회로를 만들려면 구독 피처 플래그를 사용하도록 설정해야 합니다.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. 익스프레스루트크로스연결 API에 액세스하려면 Dev_Provider 구독을 등록합니다.

익스프레스RouteCrossConnections API에 액세스하려면 파트너 구독을 **Microsoft.Network 리소스 공급자**에 등록해야 합니다. [Azure 리소스 공급자 및 형식](/azure/azure-resource-manager/management/resource-providers-and-types#azure-portal) 문서의 단계를 수행하여 등록 프로세스를 완료합니다.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Azure 리소스 관리자 REST API 호출에 대 한 인증 설정

대부분의 Azure 서비스는 서비스 API를 호출하기 전에 유효한 자격 증명을 사용하여 리소스 관리자로 인증하는 클라이언트 코드가 필요합니다. 인증은 Azure AD에 의해 다양한 행위자 간에 조정되며 클라이언트에 인증 증명으로 액세스 토큰을 제공합니다.

인증 프로세스에는 다음 두 가지 주요 단계가 포함됩니다.

1. [클라이언트를 등록합니다.](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)
2. [액세스 요청을 만듭니다.](https://docs.microsoft.com/rest/api/azure/#create-the-request)

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 클라이언트 애플리케이션에 네트워크 기여자 권한 제공

인증이 성공적으로 구성되면 Dev_Provider_Sub 따라 클라이언트 응용 프로그램에 대한 네트워크 기여자 액세스 권한을 부여해야 합니다. 권한을 부여하려면 [Azure 포털에](https://ms.portal.azure.com/#home) 로그인하고 다음 단계를 완료합니다.

1. 구독으로 이동하여 Dev_Provider_Sub 선택합니다.
2. 액세스 제어(IAM)로 이동
3. 역할 할당 추가
4. 네트워크 기여자 역할 선택
5. Azure AD 사용자, 그룹 또는 서비스 주체에 대한 액세스 할당
6. 클라이언트 응용 프로그램 선택
7. 변경 내용 저장

### <a name="5-develop"></a>5. 개발

[익스프레스루트크로스연결 API에](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections)대해 개발한다.

## <a name="rest-api"></a>REST API

REST API 설명서에 대한 [ExpressRoute 교차 연결 REST API를](https://docs.microsoft.com/rest/api/expressroute/expressroutecrossconnections) 참조하십시오.

## <a name="next-steps"></a>다음 단계

모든 익스프레스루트 REST API에 대한 자세한 내용은 [익스프레스루트 REST API를](https://docs.microsoft.com/rest/api/expressroute/)참조하십시오.