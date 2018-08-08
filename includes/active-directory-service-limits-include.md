---
title: 포함 파일
description: 포함 파일
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 07/30/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: a2055c3f00306fbfdad3028a16bb49d919e1e251
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39361405"
---
다음은 Azure AD(Azure Active Directory) 서비스에 대한 사용 제약 조건 및 기타 서비스 제한입니다.

| Category | 제한 |
| --- | --- |
| 디렉터리 | 단일 사용자는 최대 500개의 Azure AD 디렉터리에 멤버 또는 게스트로 속할 수 있습니다.<br/>단일 사용자는 최대 20개의 디렉터리를 만들 수 있습니다. |
| 도메인 | 관리되는 도메인 이름은 900개까지 추가할 수 있습니다. 온-프레미스 Active Directory와의 페더레이션을 위해 모든 도메인을 설정하는 경우 각 디렉터리에서 도메인 이름을 450개까지 추가할 수 있습니다. |
| 개체 |<ul><li>Azure Active Directory 무료 버전의 사용자는 단일 디렉터리에 최대 50만 개의 개체를 만들 수 있습니다.</li><li>관리자가 아닌 사용자는 250개 이하의 개체를 만들 수 있습니다.</li></ul> |
| 스키마 확장 |<ul><li>문자열 형식 확장은 최대 256자까지 가능합니다. </li><li>이진 형식 확장은 256바이트로 제한됩니다.</li><li>단일 개체에 100개의 확장 값(모든 형식 및 모든 응용 프로그램)을 작성할 수 있습니다.</li><li>“User”, “Group”, “TenantDetail”, “Device”, “Application” 및 “ServicePrincipal” 엔터티만 “String” 형식 또는 “Binary” 형식의 단일 값 특성으로 확장할 수 있습니다.</li><li>스키마 확장은 Graph API 버전 1.21 미리 보기에서만 사용할 수 있습니다. 확장을 등록하려면 응용 프로그램에 쓰기 권한이 있어야 합니다.</li></ul> |
| 응용 프로그램 |최대 100명의 사용자가 단일 응용 프로그램의 소유자가 될 수 있습니다. |
| 그룹 |<ul><li>최대 100명의 사용자가 단일 그룹의 소유자가 될 수 있습니다.</li><li>Azure Active Directory에서 단일 그룹의 멤버가 될 수 있는 개체의 수는 제한이 없습니다.</li><li>Azure AD Connect를 사용하여 온-프레미스 Active Directory에서 Azure Active Directory로 동기화할 수 있는 그룹 내 멤버의 수는 50,000으로 제한됩니다.</li></ul> |
| 액세스 패널 |<ul><li>Azure AD Premium 또는 Enterprise Mobility Suite에 대한 라이선스가 할당된 사용자에 대해 각 최종 사용자의 액세스 패널에 표시될 수 있는 응용 프로그램의 수는 제한이 없습니다.</li><li>Azure Active Directory의 무료 버전 또는 Azure AD Basic 버전에 대한 라이선스가 할당된 사용자의 경우 각 최종 사용자의 액세스 패널에 표시될 수 있는 앱 타일(예: Box, Salesforce 또는 Dropbox) 수는 최대 10개입니다. 관리자 계정에는 이 제한이 적용되지 않습니다.</li></ul> |
| 보고서 | 최대 1,000행을 표시하거나 보고서에 다운로드할 수 있습니다. 그 외의 데이터는 잘립니다. |
| 관리 단위 | 개체는 30개 이하 관리 단위의 멤버일 수 있습니다. |
