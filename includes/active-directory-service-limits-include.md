---
title: 포함 파일
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/28/2020
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: d906a3dd072770a05b818fd3ca8de359b8427728
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986803"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| Category | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>한 명의 사용자가 최대 200개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 도메인을 온-프레미스 Active Directory와의 페더레이션용으로 설정하는 경우 각 디렉터리에서 도메인 이름을 450개까지 추가할 수 있습니다. |
|리소스 |<ul><li>기본적으로 Azure Active Directory의 무료 버전 사용자가 단일 디렉터리에서 최대 50,000개의 Azure AD 리소스를 만들 수 있습니다. 확인된 도메인이 하나 이상 있는 경우 조직의 기본 Azure AD 서비스 할당량이 300,000개의 Azure AD 리소스로 확장됩니다. 이 서비스 제한은 Azure AD 가격 책정 페이지에서 500,000개의 리소스의 가격 책정 계층 제한과 관련이 없습니다. 기본 할당량을 초과하려면 Microsoft 지원에 문의해야 합니다.</li><li>관리자가 아닌 사용자는 250개 이하의 Azure AD 리소스를 만들 수 있습니다. 이 할당량에 대한 수를 복원하는 데 사용할 수 있는 활성 리소스와 삭제된 리소스 모두 30일 전에 삭제된 삭제된 Azure AD 리소스만 복원할 수 있습니다. 삭제된 Azure AD 리소스는 30일 동안 1/4의 값으로 이 할당량에 대한 수를 복원할 수 없습니다. 일반 업무 과정에서 이 할당량을 반복적으로 초과할 가능성이 있는 개발자가 있는 경우 무제한의 앱 등록을 만들 수 있는 권한을 가진 [사용자 지정 역할을 만들고 할당할](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) 수 있습니다.</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장은 최대 256자까지 가능합니다. </li><li>이진 형식 확장은 256바이트로 제한됩니다.</li><li>*모든* 형식 및 *모든* 응용 프로그램에서 100개의 확장 값만 단일 Azure AD 리소스에 쓸 수 있습니다.</li><li>User, Group, TenantDetail, Device, Application 및 ServicePrincipal 엔터티만 String 형식 또는 Binary 형식의 단일 값 특성으로 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 애플리케이션에 쓰기 권한이 있어야 합니다.</li></ul> |
| 애플리케이션 |최대 100명의 사용자가 단일 애플리케이션의 소유자가 될 수 있습니다. |
|애플리케이션 매니페스트 |응용 프로그램 매니페스트에 최대 1200개의 항목을 추가할 수 있습니다. |
| 그룹 |<ul><li>사용자는 Azure AD 조직에서 최대 250개의 그룹을 만들 수 있습니다.</li><li>Azure AD 조직에는 최대 5000개의 동적 그룹이 있을 수 있습니다.<li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>Azure AD 리소스의 수는 단일 그룹의 구성원일 수 있습니다.</li><li>사용자는 모든 그룹의 멤버가 될 수 있습니다.</li><li>Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 그룹 내 멤버 수는 50,000으로 제한됩니다.</li><li>Azure AD의 중첩 그룹은 모든 시나리오에서 지원되지 않습니다.</li></ul><br/> 현재 중첩된 그룹에서 지원되는 시나리오는 다음과 같습니다.<ul><li> 한 그룹을 다른 그룹의 구성원으로 추가할 수 있으며 그룹 중첩을 수행할 수 있습니다.</li><li> 그룹 구성원 클레임(앱이 토큰에서 그룹 구성원 클레임을 받도록 구성된 경우 로그인한 사용자가 속한 중첩 된 그룹이 포함됨)</li><li>조건부 액세스(그룹에 대한 조건부 액세스 정책 범위 지정 시)</li><li>셀프 서비스 암호 재설정에 대한 액세스 제한</li><li>Azure AD 조인 및 장치 등록을 수행할 수 있는 사용자 제한</li></ul><br/>다음 시나리오는 중첩 된 그룹을 지원하지 않습니다.<ul><li> 앱 역할 할당(앱에 그룹을 할당하는 것은 지원되지만 직접 할당된 그룹 내에 중첩된 그룹은 액세스 및 프로비저닝을 위한 액세스 권한이 없습니다).</li><li>그룹 기반 라이선스(그룹의 모든 구성원에게 자동으로 라이선스 할당)</li><li>Office 365 그룹입니다.</li></ul> |
| 애플리케이션 프록시 | <ul><li>앱 프록시 응용 프로그램당 초당 최대 500개의 트랜잭션</li><li>Azure AD 조직의 초당 최대 트랜잭션 750개</li></ul><br/>트랜잭션은 고유 리소스에 대한 단일 http 요청 및 응답으로 정의됩니다. 제한되면 클라이언트는 429 응답(요청이 너무 많음)을 받게 됩니다. |
| 액세스 패널 |<ul><li>사용자당 액세스 패널에서 볼 수 있는 애플리케이션의 수는 제한이 없습니다. 이는 Azure AD Premium 또는 Enterprise Mobility Suite용 라이선스가 할당된 사용자에게 적용됩니다.</li><li>각 사용자는 액세스 패널에서 최대 10개의 앱 타일을 볼 수 있습니다. 이 제한은 Azure AD 무료 라이선스 플랜에 대한 라이선스가 할당된 사용자에게 적용됩니다. 앱 타일의 예로는 Box, Salesforce 또는 Dropbox가 있습니다. 관리자 계정에는 이 제한이 적용되지 않습니다.</li></ul> |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 단위 | Azure AD 리소스는 30개 이하의 관리 단위의 구성원일 수 있습니다. |
| 관리자 역할 및 권한 | <ul><li>그룹을 [소유자로](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)추가할 수 없습니다.</li><li>그룹에 할당할 수 [없습니다.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)</li><li>다른 사용자의 디렉터리 정보를 읽는 사용자의 기능은 Azure AD 조직 전체 스위치 외부에서 제한되어 모든 비관리자 사용자의 모든 디렉터리 정보에 대한 액세스를 비활성화할 수 없습니다(권장되지 않음). 기본 권한에 대한 자세한 내용은 [여기를 참조하십시오.](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)</li><li>관리자 역할 멤버 자격 추가 및 해지가 적용되기까지 최대 15분 또는 로그아웃/로그인이 필요할 수 있습니다.</li></ul> |
