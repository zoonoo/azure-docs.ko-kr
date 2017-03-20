---
title: "Azure AD Synchronization Service Manager UI의 커넥터 | Microsoft Docs"
description: "Azure AD Connect의 Synchronization Service Manager에 있는 커넥터 탭을 이해합니다."
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 60f1d979-8e6d-4460-aaab-747fffedfc1e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/02/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: bd2240678fed44db748ae062bdf91e457159b4a2
ms.lasthandoff: 03/04/2017

---
# <a name="using-connectors-with-the-azure-ad-connect-sync-service-manager"></a>Auzre AD Connect Sync Service Manager에서 커넥터 사용

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectors.png)

커넥터 탭은 동기화 엔진이 연결된 모든 시스템을 관리하는 데 사용됩니다.

## <a name="connector-actions"></a>커넥터 작업
| 작업 | 주석 |
| --- | --- |
| 생성 |사용 안 함. 추가 AD 포리스트에 연결하려면 설치 마법사를 사용합니다. |
| 속성 |모든 도메인 및 OU 필터링에 사용 |
| [삭제](#delete) |커넥터 공간에서 데이터를 삭제하거나 포리스트에 대한 연결을 삭제하는 데 사용 |
| [실행 프로필 구성](#configure-run-profiles) |여기서는 도메인 필터링만 구성 이 작업을 사용하여 이미 구성된 실행 프로필을 볼 수 있습니다. |
| 실행 |프로필의 일회성 실행을 시작하는 데 사용 |
| 중지 |현재 프로필을 실행하는 커넥터 중지 |
| 커넥터 내보내기 |사용 안 함. |
| 커넥터 가져오기 |사용 안 함. |
| 커넥터 업데이트 |사용 안 함. |
| 스키마 새로 고침 |캐시된 스키마를 새로 고칩니다. 동기화 규칙도 업데이트되므로 대신 설치 마법사를 사용하는 것이 좋습니다. |
| [커넥터 공간 검색](#search-connector-space) |개체를 찾고 [시스템 전체에서 개체 및 해당 데이터의 흐름을 따르는](#follow-an-object-and-its-data-through-the-system)데 사용됩니다. |

### <a name="delete"></a>삭제
삭제 작업은 두 가지 작업에 사용됩니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/connectordelete.png)

**커넥터 공간만 삭제** 옵션은 모든 데이터를 제거하지만 구성은 그대로 유지합니다.

**커넥터 및 커넥터 공간 삭제** 옵션은 데이터와 구성을 제거합니다. 이 옵션은 포리스트에 더 이상 연결하지 않으려는 경우에 사용됩니다.

두 옵션 모두 모든 개체를 동기화하고 메타버스 개체를 업데이트합니다. 이 작업은 장기 실행 작업입니다.

### <a name="configure-run-profiles"></a>실행 프로필 구성
이 옵션을 사용하면 커넥터에 대해 구성된 실행 프로필을 볼 수 있습니다.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/configurerunprofiles.png)

### <a name="search-connector-space"></a>커넥터 공간 검색
커넥터 공간 검색 작업은 개체를 찾아 데이터 문제를 해결하는 데 유용합니다.

![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearch.png)

먼저 **범위**를 선택합니다. 데이터(RDN, DN 앵커, 하위 트리) 또는 개체의 상태(다른 모든 옵션)에 따라 검색할 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchscope.png)  
예를 들어 하위 트리 검색을 수행하는 경우 모든 개체를 하나의 OU로 가져옵니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cssearchsubtree.png)  
이 표에서 개체를 선택하고, **속성**을 선택한 다음 원본 커넥터 공간의 메타버스, 대상 커넥터 공간으로 이어지는 [흐름을 따릅니다](active-directory-aadconnectsync-troubleshoot-object-not-syncing.md).

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

