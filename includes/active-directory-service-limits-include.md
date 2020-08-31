---
title: 파일 포함
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/28/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: ba0791108878dada5088428ce40d1af672d1892a
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88011057"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| 범주 | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>단일 사용자는 최대 200개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 도메인을 온-프레미스 Active Directory와의 페더레이션용으로 설정하는 경우 각 디렉터리에서 도메인 이름을 450개까지 추가할 수 있습니다. |
|리소스 |<ul><li>기본적으로 Azure Active Directory 무료 버전의 사용자는 단일 디렉터리에 최대 50,000개의 Azure AD 리소스를 만들 수 있습니다. 확인된 도메인이 하나 이상 있는 경우 조직의 기본 Azure AD 서비스 할당량은 Azure AD 리소스 300,000개로 확장됩니다. 이 서비스 제한은 Azure AD 가격 책정 페이지의 리소스 500,000개 가격 책정 계층 제한과는 관련이 없습니다. 기본 할당량을 초과하여 사용하려면 Microsoft 지원에 문의해야 합니다.</li><li>관리자가 아닌 사용자는 Azure AD 리소스를 250개까지 만들 수 있습니다. 이러한 할당량에는 활성 리소스와 삭제되었지만 복원 가능한 리소스가 포함됩니다. 삭제 후 30일이 넘지 않은 Azure AD 리소스만 복원할 수 있습니다. 지난 30일 동안 삭제된 Azure AD 리소스 중 더 이상 복원할 수 없는 개체는 1/4 값으로 이 할당량에 포함됩니다. 정규 업무를 수행하는 동안 이 할당량을 반복적으로 초과할 가능성이 높은 개발자가 있는 경우 앱 등록을 무제한으로 만들 수 있는 권한이 있는 [사용자 지정 역할을 만들고 할당](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md)할 수 있습니다.</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장은 최대 256자까지 가능합니다. </li><li>이진 형식 확장은 256바이트로 제한됩니다.</li><li>*모든* 형식 및 *모든* 애플리케이션에서 단일 Azure AD 리소스에는 확장 값을 100개만 작성할 수 있습니다.</li><li>User, Group, TenantDetail, Device, Application 및 ServicePrincipal 엔터티만 String 형식 또는 Binary 형식의 단일 값 특성으로 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 애플리케이션에 쓰기 권한이 있어야 합니다.</li></ul> |
| 애플리케이션 |최대 100명의 사용자가 단일 애플리케이션의 소유자가 될 수 있습니다. |
|애플리케이션 매니페스트 |애플리케이션 매니페스트에서 최대 1200개 항목을 추가할 수 있습니다. |
| 그룹 |<ul><li>사용자는 Azure AD 조직 하나에 최대 250개의 그룹을 만들 수 있습니다.</li><li>Azure AD 조직 하나에 최대 5000개의 동적 그룹이 포함될 수 있습니다.<li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>단일 그룹의 멤버가 될 수 있는 Azure AD 리소스 수에는 제한이 없습니다.</li><li>사용자는 모든 그룹의 멤버가 될 수 있습니다.</li><li>기본적으로 Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 그룹 내 멤버 수는 50,000개로 제한됩니다. 이 한도를 초과하는 그룹 멤버 자격을 동기화해야 하는 경우 [Azure AD Connect Sync V2 엔드포인트 API](../articles/active-directory/hybrid/how-to-connect-sync-endpoint-api-v2.md)를 온보딩해야 합니다.</li><li>Azure AD의 중첩 그룹은 모든 시나리오에서 지원되지 않습니다.</li></ul><br/> 현재 중첩 그룹을 지원하는 시나리오는 다음과 같습니다.<ul><li> 한 그룹을 다른 그룹의 멤버로 추가하고 그룹을 중첩할 수 있습니다.</li><li> 그룹 멤버 자격 클레임(토큰에서 그룹 멤버 자격 클레임을 수신하도록 앱이 구성된 경우 로그인한 사용자가 멤버인 중첩 그룹 포함)</li><li>조건부 액세스(그룹에 대한 조건부 액세스 정책의 범위를 지정하는 경우)</li><li>셀프 서비스 암호 재설정에 대한 액세스 제한</li><li>Azure AD 조인 및 디바이스 등록을 수행할 수 있는 사용자 제한</li></ul><br/>다음 시나리오는 중첩 그룹을 지원하지 않습니다.<ul><li> 액세스 및 프로비저닝을 위한 앱 역할 할당(앱에 그룹을 할당하는 것은 지원되지만, 직접 할당된 그룹 내부의 중첩 그룹은 액세스 권한이 없음)</li><li>그룹 기반 라이선스(그룹의 모든 멤버에게 자동으로 라이선스 할당)</li><li>Office 365 그룹</li></ul> |
| 애플리케이션 프록시 | <ul><li>앱 프록시 애플리케이션마다 초당 최대 500개의 트랜잭션</li><li>Azure AD 조직에 대해 초당 최대 750개의 트랜잭션</li></ul><br/>트랜잭션은 고유 리소스에 대한 단일 http 요청 및 응답으로 정의됩니다. 트랜잭션이 제한되면 클라이언트는 429개의 응답을 수신합니다(너무 많은 요청). |
| 액세스 패널 |할당된 라이선스에 관계없이 사용자별 액세스 패널에 표시할 수 있는 애플리케이션의 수는 제한이 없습니다.  |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 단위 | Azure AD 리소스가 멤버로 포함될 수 있는 관리 단위의 수는 30개 이하입니다. |
| 관리자 역할 및 권한 | <ul><li>그룹은 [소유자](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)로 추가할 수 없습니다.</li><li>관리자가 아닌 사용자는 어떤 디렉터리 정보에도 액세스할 수 없도록 차단하는 Azure AD 조직 전체 스위치 외에는 다른 사용자의 디렉터리 정보를 읽는 기능을 제한할 수 없습니다(권장하지 않음). 기본 권한에 대한 자세한 내용은 [여기](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)를 참조하세요.</li><li>관리자 역할 멤버 자격을 추가하고 해지가 발효될 때까지 최대 15분이 소요되거나 로그아웃/로그인이 필요할 수 있습니다.</li></ul> |
