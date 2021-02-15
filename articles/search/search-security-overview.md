---
title: 보안 개요
titleSuffix: Azure Cognitive Search
description: 끝점, 콘텐츠 및 작업을 보호 하기 위해 Azure Cognitive Search의 보안 기능에 대해 알아봅니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/04/2021
ms.custom: references_regions
ms.openlocfilehash: 46f2035e5f8409cd38faeb9c327b88b06fc7d7a0
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097639"
---
# <a name="security-overview-for-azure-cognitive-search"></a>Azure Cognitive Search에 대 한 보안 개요

이 문서에서는 콘텐츠와 작업을 보호 하는 Azure Cognitive Search의 보안 기능을 설명 합니다.

검색 서비스에 대 한 인바운드 요청의 경우 검색 서비스 끝점을 보호 하는 보안 조치의 진행은 요청에 대 한 API 키에서 방화벽의 인바운드 규칙까지 공용 인터넷에서 서비스를 완전히 보호 하는 개인 끝점입니다.

다른 서비스에 대 한 아웃 바운드 요청의 경우 내부 요청은 외부 소스에서 콘텐츠를 읽는 인덱서에 의해 수행 됩니다. 연결 문자열에 자격 증명을 제공할 수 있습니다. 또는 Azure Storage, Azure SQL, Cosmos DB 또는 기타 Azure 데이터 원본에서 데이터에 액세스할 때 신뢰할 수 있는 서비스를 검색 하도록 관리 되는 id를 설정할 수 있습니다. 관리 id는 연결에 대 한 자격 증명 또는 액세스 키를 대체 합니다. 이 기능에 대 한 자세한 내용은 [관리 되는 id를 사용 하 여 데이터 원본에 연결](search-howto-managed-identities-data-sources.md)을 참조 하세요.

외부 서비스에 대 한 쓰기 작업은 다음과 같습니다. 검색 서비스는 로그 파일에 기록 하 고, 기술 자료 저장소를 만들고 캐시 된 강화을 유지 하 고, 디버그 세션을 유지 하는 경우 Azure Storage에 기록 합니다. Cognitive Services와 같은 다른 서비스 간 호출은 내부 네트워크에서 수행 됩니다.

보안 아키텍처 및 각 기능 범주에 대 한 개요는이 빠른 학습 비디오를 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Shows/AI-Show/Azure-Cognitive-Search-Whats-new-in-security/player]

## <a name="network-security"></a>네트워크 보안

<a name="service-access-and-authentication"></a>

인바운드 보안 기능은 증가 하는 수준의 보안 및 복잡성을 통해 검색 서비스 끝점을 보호 합니다. 먼저 모든 요청에 인증 된 액세스를 위한 API 키가 필요 합니다. 둘째, 선택적으로 특정 IP 주소에 대 한 액세스를 제한 하는 방화벽 규칙을 설정할 수 있습니다. 고급 보호의 경우 세 번째 옵션은 Azure 개인 링크를 사용 하도록 설정 하 여 모든 인터넷 트래픽에 대 한 서비스 끝점을 보호 하는 것입니다.

### <a name="public-access-using-api-keys"></a>API 키를 사용 하 여 공용 액세스

기본적으로 검색 서비스는 관리에 대 한 키 기반 인증 또는 검색 서비스 끝점에 대 한 쿼리 액세스를 사용 하 여 공용 클라우드를 통해 액세스 됩니다. 유효한 키를 제출하면 요청이 신뢰할 수 있는 엔터티에서 시작되었다는 증명으로 간주됩니다. 키 기반 인증에 대해서는 다음 섹션에서 설명 합니다.

### <a name="configure-ip-firewalls"></a>IP 방화벽 구성

검색 서비스에 대 한 액세스를 세부적으로 제어 하기 위해 특정 IP 주소 또는 IP 주소 범위에 대 한 액세스를 허용 하는 인바운드 방화벽 규칙을 만들 수 있습니다. 모든 클라이언트 연결은 허용 된 IP 주소를 통해 수행 되어야 합니다. 그렇지 않으면 연결이 거부 됩니다.

:::image type="content" source="media/search-security-overview/inbound-firewall-ip-restrictions.png" alt-text="ip 제한 액세스에 대 한 샘플 아키텍처 다이어그램":::

포털을 사용 하 여 [인바운드 액세스를 구성할](service-configure-firewall.md)수 있습니다.

또는 관리 REST Api를 사용할 수 있습니다. [IpRule](/rest/api/searchmanagement/services/createorupdate#iprule) 매개 변수를 사용 하 여 API 버전 2020-03-13부터 검색 서비스에 대 한 액세스 권한을 부여 하려는 IP 주소를 개별적으로 식별 하 여 서비스에 대 한 액세스를 제한할 수 있습니다.

### <a name="network-isolation-through-a-private-endpoint-no-internet-traffic"></a>개인 끝점을 통한 네트워크 격리 (인터넷 트래픽 없음)

[가상 네트워크](../virtual-network/virtual-networks-overview.md) 의 클라이언트가 [개인 링크](../private-link/private-link-overview.md)를 통해 검색 인덱스의 데이터에 안전 하 게 액세스할 수 있도록 Azure Cognitive Search에 대 한 [개인 끝점](../private-link/private-endpoint-overview.md) 을 설정할 수 있습니다.

개인 끝점은 검색 서비스에 대 한 연결을 위해 가상 네트워크 주소 공간의 IP 주소를 사용 합니다. 클라이언트와 검색 서비스 간의 네트워크 트래픽은 가상 네트워크 및 Microsoft 백본 네트워크의 개인 링크를 순회 하 여 공용 인터넷에서 노출 되는 것을 제거 합니다. VNET은 온-프레미스 네트워크와 인터넷을 통해 리소스 간 보안 통신을 허용 합니다.

:::image type="content" source="media/search-security-overview/inbound-private-link-azure-cog-search.png" alt-text="개인 끝점 액세스를 위한 샘플 아키텍처 다이어그램":::

이 솔루션은 가장 안전 하지만, 추가 서비스를 사용 하는 것이 추가 비용 이므로,이에 대해서는 살펴보기 전에 혜택을 명확 하 게 이해 하 고 있어야 합니다. 비용에 대 한 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/private-link/)를 참조 하세요. 이러한 구성 요소가 함께 작동 하는 방법에 대 한 자세한 내용은이 문서의 맨 위에 있는 비디오를 시청 하세요. 개인 끝점 옵션은 5:48에서 시작 하 여 비디오에 적용 됩니다. 끝점을 설정 하는 방법에 대 한 지침은 [Azure Cognitive Search에 대 한 개인 끝점 만들기](service-create-private-endpoint.md)를 참조 하세요.

## <a name="authentication"></a>인증

검색 서비스에 대 한 인바운드 요청의 경우 인증은 신뢰할 수 있는 원본에서 요청을 증명 하는 [필수 API 키](search-security-api-keys.md) (임의로 생성 된 숫자 및 문자로 구성 된 문자열)를 통해 이루어집니다. Cognitive Search는 현재 인바운드 요청에 대 한 Azure Active Directory 인증을 지원 하지 않습니다.

인덱서에 의해 수행 된 아웃 바운드 요청은 외부 서비스의 인증을 받습니다. Cognitive Search의 인덱서 하위 서비스는 관리 되는 id를 사용 하 여 다른 서비스에 연결 하 여 Azure에서 신뢰할 수 있는 서비스를 만들 수 있습니다. 자세한 내용은 [관리 되는 id를 사용 하 여 데이터 원본에 대 한 인덱서 연결 설정](search-howto-managed-identities-data-sources.md)을 참조 하세요.

## <a name="authorization"></a>권한 부여

Cognitive Search는 콘텐츠 관리 및 서비스 관리를 위한 다양 한 권한 부여 모델을 제공 합니다. 

### <a name="authorization-for-content-management"></a>콘텐츠 관리에 대 한 권한 부여

콘텐츠에 대 한 권한 부여 및 콘텐츠와 관련 된 작업은 요청에 제공 된 [API 키](search-security-api-keys.md) 를 통해 부여 되 되는 쓰기 권한입니다. API 키는 인증 메커니즘 이지만 API 키의 형식에 따라 액세스 권한을 부여 하기도 합니다.

+ 서비스를 프로 비전 할 때 만든 관리 키 (검색 서비스에서 만들기-읽기-업데이트-삭제 작업에 대 한 읽기-쓰기 액세스 허용)

+ 필요에 따라 생성 되 고 쿼리를 실행 하는 클라이언트 응용 프로그램을 위해 설계 된 쿼리 키 (인덱스의 문서 컬렉션에 대 한 읽기 전용 액세스 허용)

응용 프로그램 코드에서는 끝점과 옵션에 대 한 액세스를 허용 하도록 끝점과 API 키를 지정 합니다. 끝점은 서비스 자체, 인덱스 컬렉션, 특정 인덱스, 문서 컬렉션 또는 특정 문서 일 수 있습니다. 끝점, 작업 (예: 만들기 또는 업데이트 요청) 및 권한 수준 (키에 따라 전체 또는 읽기 전용 권한)을 함께 연결 하는 경우 콘텐츠와 작업을 보호 하는 보안 수식을 구성 합니다.

### <a name="controlling-access-to-indexes"></a>인덱스에 대 한 액세스 제어

Azure Cognitive Search에서 개별 인덱스는 보안 개체가 아닙니다. 대신, 인덱스에 대 한 액세스는 작업의 컨텍스트와 함께 서비스 계층 (제공 하는 API 키에 따라 읽기 또는 쓰기 액세스)에서 결정 됩니다.

읽기 전용 액세스의 경우 쿼리 [키](search-security-rbac.md)를 사용 하 여 연결 하도록 쿼리 요청을 구조화 하 고 앱에서 사용 하는 특정 인덱스를 포함할 수 있습니다. 쿼리 요청에서 동시에 인덱스를 조인하거나 여러 인덱스에 액세스한다는 개념은 없으므로 모든 요청은 정의에 따라 단일 인덱스를 대상으로 지정합니다. 따라서 쿼리 요청 자체의 구조(키 및 단일 대상 인덱스)는 보안 경계를 정의합니다.

인덱스에 대한 관리자 및 개발자 액세스는 구분되지 않습니다. 둘 다 서비스에서 관리되는 개체를 만들고, 삭제하고, 업데이트하는 쓰기 액세스 권한이 필요합니다. 서비스에 대 한 [관리자 키](search-security-rbac.md) 가 있는 모든 사용자는 동일한 서비스에서 인덱스를 읽고 수정 하거나 삭제할 수 있습니다. 인덱스를 우발적이거나 악의적인 삭제로부터 보호하기 위해 코드 자산에 대한 내부 원본을 제어하여 원치 않는 삭제나 수정을 되돌리는 문제를 해결합니다. Azure Cognitive Search는 가용성을 보장 하기 위해 클러스터 내에서 장애 조치 (failover)를 수행 하지만 인덱스를 만들거나 로드 하는 데 사용 되는 소유 코드를 저장 하거나 실행 하지 않습니다.

인덱스 수준에서 보안 경계를 요구하는 다중 테넌트 솔루션의 경우 이러한 솔루션에는 일반적으로 중간 계층이 포함됩니다. 고객은 인덱스 격리를 처리하기 위해 이를 사용합니다. 다중 테 넌 트 사용 사례에 대 한 자세한 내용은 [다중 테 넌 트 SaaS 응용 프로그램 및 Azure Cognitive Search에 대 한 디자인 패턴](search-modeling-multitenant-saas-applications.md)을 참조 하세요.

### <a name="controlling-access-to-documents"></a>문서에 대 한 액세스 제어

검색 결과에 대 한 사용자 단위 제어를 세부적으로 요구 하는 경우 쿼리에 보안 필터를 빌드하여 지정 된 보안 id와 연결 된 문서를 반환할 수 있습니다. 

"행 수준 보안"과 개념적으로 동일 하며, 인덱스 내의 콘텐츠에 대 한 권한 부여는 Azure Active Directory의 엔터티에 매핑되는 미리 정의 된 역할 또는 역할 할당을 사용 하 여 기본적으로 지원 되지 않습니다. Cosmos DB 등의 외부 시스템 데이터에 대 한 모든 사용자 권한은 Cognitive Search에 의해 인덱싱되는 데이터와 함께 전송 되지 않습니다.

"행 수준 보안"이 필요한 솔루션에 대 한 해결 방법에는 보안 그룹 또는 사용자 id를 나타내는 데이터 원본에 필드를 만든 다음 Cognitive Search에서 필터를 사용 하 여 id를 기준으로 문서 및 콘텐츠의 검색 결과를 선택적으로 트리밍하는 것이 있습니다. 다음 표에서는 권한이 없는 콘텐츠의 검색 결과를 잘라내는 방법에 대한 두 가지 방법을 설명합니다.

| 접근 방식 | Description |
|----------|-------------|
|[ID 필터에 따라 보안 조정](search-security-trimming-for-azure-search.md)  | 사용자 ID 액세스 제어를 구현하기 위한 기본 워크플로를 문서화합니다. 인덱스에 보안 식별자를 추가하는 방법을 다루고 금지된 콘텐츠의 결과를 잘라내는 해당 필드에 대한 필터링을 설명합니다. |
|[Azure Active Directory ID에 따라 보안 조정](search-security-trimming-for-azure-search-with-aad.md)  | 이 문서는 Azure cloud platform의 [무료 서비스](https://azure.microsoft.com/free/) 중 하나인 Azure Active Directory (azure AD)에서 id를 검색 하는 단계를 제공 하는 이전 문서를 확장 합니다. |

### <a name="authorization-for-service-management"></a>서비스 관리에 대 한 권한 부여

서비스 관리 작업은 azure [RBAC (역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 통해 권한이 부여 됩니다. Azure RBAC는 Azure 리소스의 프로 비전을 위해 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반으로 구축 된 권한 부여 시스템입니다. 

Azure Cognitive Search에서 리소스 관리자를 사용 하 여 서비스를 만들거나 삭제 하 고, API 키를 관리 하 고, 서비스를 확장 합니다. 따라서 Azure 역할 할당은 [포털](search-manage.md), [POWERSHELL](search-manage-powershell.md)또는 [관리 REST api](/rest/api/searchmanagement/search-howto-management-rest-api)를 사용 하는지에 관계 없이 이러한 작업을 수행할 수 있는 사람을 결정 합니다.

검색 서비스 관리에 대해 [세 가지 기본 역할이](search-security-rbac.md#management-tasks-by-role) 정의 됩니다. 역할 할당은 지원 되는 방법 (포털, PowerShell 등)을 사용 하 여 만들 수 있으며 서비스 전체에 적용 됩니다. 소유자 및 참여자 역할은 다양 한 관리 기능을 수행할 수 있습니다. 필수 정보만 보는 사용자에 게 읽기 권한자 역할을 할당할 수 있습니다.

> [!Note]
> Azure 전체 메커니즘을 사용 하 여 구독 또는 리소스를 잠가 관리자 권한이 있는 사용자가 실수로 검색 서비스를 삭제 하지 못하도록 할 수 있습니다. 자세한 내용은 [예기치 않은 삭제를 방지 하기 위해 리소스 잠그기](../azure-resource-manager/management/lock-resources.md)를 참조 하세요.

<a name="encryption"></a>

## <a name="data-protection"></a>데이터 보호

저장소 계층에서 데이터 암호화는 인덱스, 동의어 맵, 인덱서, 데이터 원본 및 기술력과의 정의를 비롯 하 여 디스크에 저장 된 모든 서비스 관리 콘텐츠에 대해 기본적으로 제공 됩니다. 필요에 따라 인덱싱된 콘텐츠의 보충 암호화를 위해 CMK (고객 관리 키)를 추가할 수 있습니다. 8 월 1 2020 이후에 만들어진 서비스의 경우 CMK 암호화는 인덱싱된 콘텐츠의 전체 "이중 암호화"에 대해 임시 디스크의 데이터로 확장 됩니다.

### <a name="data-in-transit"></a>전송 중 데이터

Azure Cognitive Search에서 암호화는 연결과 전송으로 시작 되 고 디스크에 저장 된 콘텐츠로 확장 됩니다. 공용 인터넷에서 검색 서비스의 경우 Azure Cognitive Search는 HTTPS 포트 443에서 수신 대기 합니다. 모든 클라이언트-서비스 연결은 TLS 1.2 암호화를 사용 합니다. 이전 버전 (1.0 또는 1.1)은 지원 되지 않습니다.

### <a name="encrypted-data-at-rest"></a>미사용 암호화 된 데이터

검색 서비스에서 내부적으로 처리 되는 데이터의 경우 다음 표에서는 [데이터 암호화 모델](../security/fundamentals/encryption-models.md)에 대해 설명 합니다. 기술 자료 저장소, 증분 보강, 인덱서 기반 인덱싱 등의 일부 기능은 다른 Azure 서비스의 데이터 구조에서 읽거나 쓸 수 있습니다. 이러한 서비스에는 Azure Cognitive Search와 별도의 암호화 지원이 포함 됩니다.

| 모델 | 키&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 사항이&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 제한 | 적용 대상 |
|------------------|-------|-------------|--------------|------------|
| 서버 쪽 암호화 | Microsoft 관리형 키 | 없음 (기본 제공) | 없음. 모든 지역에서 24 2018 년 1 월 이후에 만들어진 콘텐츠의 경우 모든 계층에서 사용할 수 있습니다. | 콘텐츠 (인덱스 및 동의어 맵) 및 정의 (인덱서, 데이터 원본, 기술력과) |
| 서버 쪽 암호화 | 고객 관리 키 | Azure Key Vault | 청구 가능 계층의 모든 지역에서 2019 년 1 월 이후 생성 된 콘텐츠에 사용할 수 있습니다. | 데이터 디스크의 콘텐츠 (인덱스 및 동의어 맵) |
| 서버 쪽 이중 암호화 | 고객 관리 키 | Azure Key Vault | 8 월 1 2020 이후의 검색 서비스에서 선택한 지역의 청구 가능 계층에서 사용 가능 합니다. | 데이터 디스크 및 임시 디스크에 대 한 콘텐츠 (인덱스 및 동의어 맵) |

### <a name="service-managed-keys"></a>서비스 관리 키

서비스 관리 암호화는 256 비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용 하 여 [Azure Storage 서비스 암호화](../storage/common/storage-service-encryption.md)를 기반으로 하는 Microsoft 내부 작업입니다. 완전히 암호화 되지 않은 인덱스에 대 한 증분 업데이트 (1 월 2018 일 이전에 만들어짐)를 비롯 하 여 모든 인덱싱에서 자동으로 발생 합니다.

### <a name="customer-managed-keys-cmk"></a>CMK (고객이 관리 하는 키)

고객 관리 키에는 다른 지역에 있을 수 있지만 Azure Cognitive Search와 같은 구독에 있을 수 있는 추가 청구 가능 서비스 Azure Key Vault 필요 합니다. CMK 암호화를 사용 하면 인덱스 크기가 늘어나고 쿼리 성능이 저하 됩니다. 날짜에 대 한 관찰을 기준으로 쿼리 시간에 30%-60%가 증가 하는 것을 확인할 수 있습니다. 하지만 실제 성능은 인덱스 정의 및 쿼리 유형에 따라 달라 집니다. 이 성능에 영향을 주므로 정말 필요한 인덱스 에서만이 기능을 사용 하도록 설정 하는 것이 좋습니다. 자세한 내용은 [Azure Cognitive Search에서 고객이 관리 하는 암호화 키 구성](search-security-manage-encryption-keys.md)을 참조 하세요.

<a name="double-encryption"></a>

### <a name="double-encryption"></a>이중 암호화

Azure Cognitive Search에서 이중 암호화는 CMK의 확장입니다. 이는 2 배 암호화 (CMK에서 한 번, 서비스 관리 키에 의해 다시) 및 포괄적인 범위 (데이터 디스크에 기록 되는 장기 저장소 및 임시 디스크에 기록 되는 단기 저장소)에 대해 포괄적으로 이해 하 고 있습니다. 1 2020 년 8 월 이전 CMK와 Azure Cognitive Search에서 CMK가 이중 암호화 기능을 수행 하는 것은 임시 디스크에 있는 미사용 데이터의 추가 암호화입니다.

이중 암호화는 현재 8 월 1 일 이후에 이러한 지역에서 만든 새 서비스에서 사용할 수 있습니다.

+ 미국 서부 2
+ 미국 동부
+ 미국 중남부
+ US Gov 버지니아
+ US Gov 애리조나

## <a name="security-management"></a>보안 관리

### <a name="api-keys"></a>API 키

API 키 기반 인증을 사용 하는 경우 Azure 보안 모범 사례에 따라 정기적으로 관리 키를 다시 생성 하는 계획을 세워야 합니다. 검색 서비스 당 최대 2 개의 관리자 키가 있습니다. API 키를 보호 하 고 관리 하는 방법에 대 한 자세한 내용은 [api 키 만들기 및 관리](search-security-api-keys.md)를 참조 하세요.

#### <a name="activity-and-diagnostic-logs"></a>활동 및 진단 로그

Cognitive Search는 사용자 id를 기록 하지 않으므로 특정 사용자에 대 한 정보에 대 한 로그를 참조할 수 없습니다. 그러나이 서비스는 특정 작업의 에이전시를 이해 하기 위해 다른 로그와 상관 관계를 지정할 수 있는 만들기-읽기-업데이트-삭제 작업을 기록 합니다.

Azure에서 경고 및 로깅 인프라를 사용 하 여 쿼리 볼륨 급증 또는 예상 되는 워크 로드에서 벗어난 다른 작업을 선택할 수 있습니다. 로그를 설정 하는 방법에 대 한 자세한 내용은 [로그 데이터 수집 및 분석](search-monitor-logs.md) 및 [쿼리 요청 모니터링](search-monitor-queries.md)을 참조 하세요.

### <a name="certifications-and-compliance"></a>인증 및 규정 준수

Azure Cognitive Search는 정기적인 감사에 참여 하 고 공용 클라우드와 Azure Government에 대해 다양 한 전역, 지역 및 산업별 표준에 대해 인증 받았습니다. 전체 목록은 공식 감사 보고서 페이지에서 [ **Microsoft Azure 준수 제품** 백서](https://azure.microsoft.com/resources/microsoft-azure-compliance-offerings/) 를 다운로드 하세요.

규정 준수를 위해 [Azure Policy](../governance/policy/overview.md) 를 사용 하 여 [Azure 보안 벤치 마크](../security/benchmarks/introduction.md)에 대 한 높은 수준의 보안 모범 사례를 구현할 수 있습니다. Azure 보안 벤치 마크는 서비스 및 데이터에 대 한 위협을 완화 하기 위해 수행 해야 하는 주요 작업에 매핑되는 보안 변환 된에 대 한 보안 권장 사항의 모음입니다. 현재는 [네트워크 보안](../security/benchmarks/security-control-network-security.md), [로깅 및 모니터링](../security/benchmarks/security-control-logging-monitoring.md)을 비롯 하 여 몇 가지 보안 제어와 이름에 대 한 [데이터 보호 기능이](../security/benchmarks/security-control-data-protection.md) 있습니다.

Azure Policy는 azure에 기본 제공 되는 기능으로, Azure 보안 벤치 마크를 비롯 한 여러 표준에 대 한 규정 준수를 관리 하는 데 도움이 됩니다. 잘 알려진 벤치 마크의 경우 Azure Policy는 두 조건을 모두 제공 하는 기본 제공 정의 및 비준수를 해결 하는 조치 가능한 응답을 제공 합니다.

Azure Cognitive Search의 경우 현재 기본 제공 정의가 하나 있습니다. 진단 로깅에 대 한 것입니다. 이 기본 제공 기능을 사용 하면 진단 로깅이 누락 된 검색 서비스를 식별 하는 정책을 할당 한 후이를 켤 수 있습니다. 자세한 내용은 [Azure Cognitive Search에 대 한 규정 준수 제어 Azure Policy](security-controls-policy.md)를 참조 하세요.

## <a name="see-also"></a>참고 항목

+ [Azure 보안 기본 사항](../security/fundamentals/index.yml)
+ [Azure 보안](https://azure.microsoft.com/overview/security)
+ [Azure Security Center](../security-center/index.yml)