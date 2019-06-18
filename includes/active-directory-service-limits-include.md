---
title: 포함 파일
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 067280cdad85f59106bce5ff214e2fa9eddf3b71
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133504"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| Category | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>단일 사용자는 최대 20개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 모든 도메인을 온-프레미스 Active Directory와의 페더레이션용으로 설정하는 경우 각 디렉터리에서 도메인 이름을 450개까지 추가할 수 있습니다. |
| 개체 |<ul><li>기본적으로 Azure Active Directory 무료 버전의 사용자는 단일 디렉터리에 최대 5만 개의 개체를 만들 수 있습니다. 확인된 도메인이 하나 이상인 경우 Azure AD의 기본 디렉터리 서비스 할당량은 30만 개 개체로 확장됩니다. </li><li>관리자가 아닌 사용자는 250개 이하의 개체를 만들 수 있습니다. 이러한 할당량에는 활성 개체와 복원 가능한 삭제된 개체가 포함됩니다. 삭제 후 30일이 넘지 않은 개체만 복원할 수 있습니다. 지난 30일 동안 삭제된 개체 중 더 이상 복원할 수 없는 개체는 1/4 값으로 이 할당량에 포함됩니다. 평상시 업무 수행 중에 이 할당량을 반복적으로 초과할 가능성이 높은 관리자가 아닌 사용자에게는 [관리자 역할을 할당](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md)할 수 있습니다.</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장은 최대 256자까지 가능합니다. </li><li>이진 형식 확장은 256바이트로 제한됩니다.</li><li>단일 개체에 100개의 확장 값(*모든* 형식 및 *모든* 애플리케이션)을 작성할 수 있습니다.</li><li>User, Group, TenantDetail, Device, Application 및 ServicePrincipal 엔터티만 String 형식 또는 Binary 형식의 단일 값 특성으로 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 애플리케이션에 쓰기 권한이 있어야 합니다.</li></ul> |
| 애플리케이션 |최대 100명의 사용자가 단일 애플리케이션의 소유자가 될 수 있습니다. |
| 그룹 |<ul><li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>단일 그룹의 멤버가 될 수 있는 개체의 수는 제한이 없습니다.</li><li>사용자는 모든 그룹의 멤버가 될 수 있습니다.</li><li>Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 그룹 내 멤버 수는 50,000으로 제한됩니다.</li></ul> |
| 애플리케이션 프록시 | <ul><li>앱 프록시 응용 프로그램 마다 초당 500 트랜잭션 최대</li><li>테 넌 트에 대 한 초당 750 트랜잭션 최대</li></ul><br/>트랜잭션이 단일 http 요청 및 고유 리소스에 대 한 응답으로 정의 됩니다. 제한 되는 경우 클라이언트 429 (너무 많은 요청) 응답을 받습니다. |
| 액세스 패널 |<ul><li>사용자당 액세스 패널에서 볼 수 있는 애플리케이션의 수는 제한이 없습니다. 이는 Azure AD Premium 또는 Enterprise Mobility Suite용 라이선스가 할당된 사용자에게 적용됩니다.</li><li>각 사용자는 액세스 패널에서 최대 10개의 앱 타일을 볼 수 있습니다. 이러한 제한은 Azure Active Directory의 무료 또는 Azure AD 기본 버전용 라이선스가 할당된 사용자에게 적용됩니다. 앱 타일의 예로는 Box, Salesforce 또는 Dropbox가 있습니다. 관리자 계정에는 이 제한이 적용되지 않습니다.</li></ul> |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 단위 | 개체는 30개 이하 관리 단위의 멤버일 수 있습니다. |
| 관리자 역할 및 권한 | <ul><li>그룹을 추가할 수 없습니다는 [소유자](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership)합니다.</li><li>그룹에 할당할 수 없습니다는 [역할](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles)입니다.</li><li>사용자가 다른 사용자의 디렉터리 정보를 읽을 수는 테 넌 트 수준 스위치 (권장 하지 않음) 모든 디렉터리 정보에 대 한 모든 관리자가 아닌 사용자의 액세스를 사용 하지 않도록 설정 하려면 외부에서 제한할 수 없습니다. 기본 권한에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users)합니다.</li><li>최대 15 분이 걸릴 수 있습니다 하거나 아웃/서명 관리 하기 전에 서명 역할 멤버 자격 추가 및 해지가 적용 합니다.</li></ul> |
