---
title: Azure ExpressRoute CrossConnnections API 개발 및 통합
description: 이 문서에서는 expressRouteCrossConnections 리소스 종류와 관련한 ExpressRoute 파트너에 대한 자세한 개요를 제공합니다.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011985"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API 개발 및 통합

ExpressRoute Partner Resource Manager API를 사용하여 ExpressRoute 파트너는 고객 ExpressRoute 회로의 계층 2 및 계층 3 구성을 관리할 수 있습니다. ExpressRoute Partner Resource Manager API에는 새 리소스 종류인 **expressRouteCrossConnections** 가 도입되었습니다. 파트너는 이 리소스를 사용하여 고객 ExpressRoute 회로를 관리합니다.

## <a name="workflow"></a>워크플로

expressRouteCrossConnections 리소스는 ExpressRoute 회로에 대한 섀도 리소스입니다. Azure 고객이 ExpressRoute 회로를 만들고 특정 ExpressRoute 파트너를 선택하는 경우 Microsoft는 파트너의 Azure ExpressRoute 관리 구독에 expressRouteCrossConnections 리소스를 만듭니다. 이 과정에서 Microsoft에서는 expressRouteCrossConnections 리소스를 만들 리소스 그룹을 정의합니다. 리소스 그룹에 대한 명명 표준은 **CrossConnection-* PeeringLocation***입니다. 여기서 PeeringLocation = ExpressRoute 위치입니다. 예를 들어 고객이 덴버에서 ExpressRoute 회로를 만드는 경우 다음 리소스 그룹 **CrossConnnection-Denver** 에서 파트너의 Azure 구독에 CrossConnection이 생성됩니다.

ExpressRoute 파트너는 expressRouteCrossConnections 리소스에 대해 REST 작업을 발급하여 계층 2 및 계층 3 구성을 관리합니다.

## <a name="benefits"></a>이점

expressRouteCrossConnections 리소스로 이동할 경우의 이점:

* ExpressRoute 파트너에 대한 향후 개선 사항은 ExpressRouteCrossConnection 리소스에서 제공될 예정입니다.

* 파트너는 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 expressRouteCrossConnection 리소스에 적용할 수 있습니다. 이러한 컨트롤은 계정에서 expressRouteCrossConnection 리소스를 수정하고 피어링 구성을 추가/업데이트/삭제할 수 있는 사용자에 대한 권한을 정의할 수 있습니다.

* expressRouteCrossConnection 리소스는 ExpressRoute 연결 문제를 해결하는 데 도움이 될 수 있는 API를 노출합니다. 여기에는 ARP 테이블, BGP 경로 테이블 요약 및 BGP 경로 테이블 세부 정보가 포함됩니다. 이 기능은 클래식 배포 API에서 지원되지 않습니다.

* 파트너는 *RouteFilter* 리소스를 사용하여 Microsoft 피어링에 보급된 커뮤니티를 조회할 수도 있습니다.

## <a name="api-development-and-integration-steps"></a>API 개발 및 통합 단계

파트너 API에 대해 개발하기 위해 ExpressRoute 파트너는 테스트 고객 및 테스트 파트너 설정을 활용합니다. 테스트 고객 설정은 더미 디바이스 및 포트에 매핑되는 테스트 피어링 위치에서 ExpressRoute 회로를 만드는 데 사용됩니다. 테스트 파트너 설정은 테스트 피어링 위치에서 만든 ExpressRoute 회로를 관리하는 데 사용됩니다.

### <a name="1-enlist-subscriptions"></a>1. 구독 등록

테스트 파트너 및 테스트 고객 설정을 요청하려면 ExpressRoute 엔지니어링 담당자에게 두 개의 종량제 Azure 구독을 등록합니다.
* **ExpressRoute_API_Dev_Provider_Sub:** 이 구독은 더미 디바이스 및 포트의 테스트 피어링 위치에서 만든 ExpressRoute 회로를 관리하는 데 사용됩니다.

* **ExpressRoute_API_Dev_Customer_Sub:** 이 구독은 더미 디바이스 및 포트에 매핑되는 테스트 피어링 위치에서 ExpressRoute 회로를 만드는 데 사용됩니다.

테스트 피어링 위치: 더미 디바이스 및 포트는 기본적으로 프로덕션 고객에게 노출되지 않습니다. 테스트 설정에 매핑되는 ExpressRoute 회로를 만들려면 구독 기능 플래그를 사용하도록 설정해야 합니다.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. Dev_Provider 구독을 등록하여 expressRouteCrossConnections API에 액세스

expressRouteCrossConnections API에 액세스하려면 파트너 구독을 **Microsoft.Network 리소스 공급자** 에 등록해야 합니다. [Azure 리소스 공급자 및 종류](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) 문서의 단계에 따라 등록 프로세스를 완료합니다.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Azure Resource Manager REST API 호출에 대한 인증 설정

대부분의 Azure 서비스는 서비스 API를 호출하기 전에 유효한 자격 증명을 사용하여 Resource Manager에 인증하는 클라이언트 코드가 필요합니다. 인증은 Azure AD의 다양한 행위자 간에 조정되며 인증 증명으로 클라이언트에 액세스 토큰을 제공합니다.

인증 프로세스에는 두 가지 주요 단계가 포함됩니다.

1. [클라이언트를 등록](/rest/api/azure/#register-your-client-application-with-azure-ad)합니다.
2. [액세스 요청을 만듭니다](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. 클라이언트 애플리케이션에 대한 네트워크 기여자 권한 제공

인증이 구성되면 Dev_Provider_Sub에서 클라이언트 애플리케이션에 대한 네트워크 기여자 액세스 권한을 부여해야 합니다. 사용 권한을 부여하려면 [Azure Portal](https://ms.portal.azure.com/#home)에 로그인하고 다음 단계를 완료합니다.

1. 구독으로 이동하고 Dev_Provider_Sub을 선택합니다.
2. 엑세스 제어(IAM)로 이동합니다.
3. 역할 할당을 추가합니다.
4. 네트워크 기여자 역할을 선택합니다.
5. Azure AD 사용자, 그룹 또는 서비스 주체에 대한 액세스를 할당합니다.
6. 클라이언트 애플리케이션을 선택합니다.
7. 변경 내용 저장

### <a name="5-develop"></a>5. 개발

[expressRouteCrossConnections API](/rest/api/expressroute/expressroutecrossconnections)를 기반으로 하여 개발합니다.

## <a name="rest-api"></a>REST API

REST API 설명서의 [ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections)를 참고하세요.

## <a name="next-steps"></a>다음 단계

모든 ExpressRoute REST API에 대한 자세한 내용은 [ExpressRoute REST API](/rest/api/expressroute/)를 참고하세요.