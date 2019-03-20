---
title: 포함 파일
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554750"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| Category | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>단일 사용자는 최대 20개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 온-프레미스 Active Directory와의 페더레이션을 위해 도메인을 설정한 경우 각 디렉터리에서 최대 450 개의 도메인 이름을 추가할 수 있습니다. |
| 개체 |<ul><li>Azure Active Directory 무료 버전의 사용자는 단일 디렉터리에 최대 50만 개의 개체를 만들 수 있습니다.</li><li>관리자가 아닌 사용자는 250개 이하의 개체를 만들 수 있습니다. 현재 개체와이 할당량에 대해 계산을 복원에 사용할 수 있는 삭제 된 개체입니다. 삭제 된 개체는 삭제만 30 일 미만 전 복원에 사용할 수 있습니다. 삭제 된 개체의 값이 할당량에 대해 계산을 복원 하려면 더 이상 사용할 수 있는 30 일간 1-4입니다. 아마도 [관리자 역할 할당](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) 관리자가 아닌 사용자에 게 반복적으로 일반 업무 과정에서이 할당량을 초과할 가능성이 있습니다.</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장에는 최대 256 자 있을 수 있습니다. </li><li>이진 형식 확장은 256 바이트로 제한 됩니다.</li><li>100 개의 확장 값에 걸쳐 *모든* 형식 및 *모든* 응용 프로그램에 단일 개체에 쓸 수 있습니다.</li><li>문자열 형식 또는 이진 형식의 단일 값 특성을 사용 하 여 사용자, 그룹, TenantDetail, 장치, 응용 프로그램 및 ServicePrincipal 엔터티만 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기 에서만 사용할 수 있습니다. 확장을 등록하려면 애플리케이션에 쓰기 권한이 있어야 합니다.</li></ul> |
| 애플리케이션 |최대 100명의 사용자가 단일 애플리케이션의 소유자가 될 수 있습니다. |
| 그룹 |<ul><li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>단일 그룹의 멤버가 될 수 있는 개체의 수는 제한이 없습니다.</li><li>사용자는 모든 그룹의 멤버가 될 수 있습니다.</li><li>동기화 할 수 있는 온-프레미스 Active Directory에서 Azure Active Directory에 Azure AD Connect를 사용 하 여 그룹의 멤버 수가 50,000 멤버로 제한 됩니다.</li></ul> |
| 액세스 패널 |<ul><li>사용자 당 액세스 패널에서 볼 수 있는 응용 프로그램의 수 제한은 없습니다. Azure AD Premium 또는 Enterprise Mobility Suite에 대 한 라이선스를 할당 하는 사용자에 게 적용 됩니다.</li><li>각 사용자에 대 한 액세스 패널에서 최대 10 개의 앱 타일을 볼 수 있습니다. 무료 라이선스를 할당 된 사용자 또는 Azure AD Basic 버전의 Azure Active Directory에이 제한이 적용 됩니다. 앱 타일의 예로 상자, Salesforce 또는 Dropbox를 들 수 있습니다. 이 한도 관리자 계정에 적용 되지 않습니다.</li></ul> |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 단위 | 개체는 30개 이하 관리 단위의 멤버일 수 있습니다. |
| 관리자 역할 및 권한 | <li>그룹을 소유자로 추가할 수 없습니다.<li>그룹을 역할에 할당할 수 없습니다.<li>기본 사용자 권한은 Azure AD의 사용자 설정이 테 넌 트 스위치를 제외 하 고 변경할 수 없습니다. |
