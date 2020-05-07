---
title: Azure AD 갤러리 앱에 대 한 사용자 프로 비전에 몇 시간이 걸릴 수 있습니다.
description: 애플리케이션에 대한 프로비저닝이 예상보다 오래 걸리는 이유를 확인하는 방법
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.openlocfilehash: d663962dab058a108faa7d5310a3f2892c345a75
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593899"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Azure AD 갤러리 애플리케이션에 대한 사용자 프로비저닝이 오래 걸림

응용 프로그램에 대해 처음으로 프로 비전을 사용 하도록 설정 하는 경우 Azure AD 디렉터리의 크기와 프로 비전 범위에 있는 사용자 수에 따라 초기 주기가 20 분에서 몇 시간까지 걸릴 수 있습니다. 

프로 비전 서비스는 초기 주기 후 두 시스템의 상태를 나타내는 워터 마크를 저장 하므로 초기 주기가 끝난 후에도 이후 동기화는 이후 동기화의 성능이 향상 됩니다.

## <a name="how-to-improve-provisioning-performance"></a>프로비저닝 성능을 향상시키는 방법

초기 주기가 몇 시간 이상 소요 되는 경우 성능을 향상 시키기 위해 수행할 수 있는 한 가지 작업이 있습니다.

-   **사용자 범위 지정 필터** 범위 지정 필터를 사용하면 특정 특성 값에 따라 사용자를 필터링하여 프로비저닝 서비스가 Azure AD에서 추출하는 데이터를 미세 조정할 수 있습니다. 범위 지정 필터에 대한 자세한 내용은 [범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비전](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory를 사용 하 여 SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전 해제 자동화](user-provisioning.md)

