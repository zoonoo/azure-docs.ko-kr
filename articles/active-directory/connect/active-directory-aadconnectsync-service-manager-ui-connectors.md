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
ms.date: 02/27/2017
ms.author: billmath
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: b9a3b64d9de48f17a295ca7a9ea58cf26e8f83ed
ms.openlocfilehash: 55adbf800c6312371d937ed2da98a4a5ce704e51
ms.lasthandoff: 02/28/2017

---
# <a name="using-connectors-with-the-auzre-ad-connect-sync-service-manager"></a>Auzre AD Connect Sync Service Manager에서 커넥터 사용

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
이 표에서 개체를 선택하고, **속성**을 선택한 다음 원본 커넥터 공간의 메타버스, 대상 커넥터 공간으로 이어지는 [흐름을 따릅니다](#follow-an-object-and-its-data-through-the-system).

## <a name="follow-an-object-and-its-data-through-the-system"></a>시스템 전체에서 개체 및 해당 데이터의 흐름을 따르는
데이터 문제를 해결하는 경우 원본 커넥터 공간에서 메타버스, 대상 커넥터 공간으로 이어지는 개체의 흐름을 따르는 것이 데이터에 예상 값이 없는 이유를 이해하는 주요 절차입니다.

### <a name="connector-space-object-properties"></a>커넥터 공간 개체 속성
**가져오기**  
cs 개체를 열면 위쪽에 여러 개의 탭이 표시됩니다. **가져오기** 탭은 가져온 후에 준비된 데이터를 표시합니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/csimport.png)  
**이전 값**은 시스템에 현재 저장된 값을 표시하고 **새 값**은 원본 시스템에서 받았지만 아직 적용되지 않은 값을 표시합니다. 이 경우 동기화 오류가 있으므로 변경 내용을 적용할 수 없습니다.

**오류**  
오류 페이지는 개체에 문제가 있는 경우에만 표시됩니다. [동기화 오류 문제 해결](active-directory-aadconnectsync-service-manager-ui-operations.md#troubleshoot-errors-in-operations-tab)방법에 대한 자세한 내용은 작업 페이지의 세부 정보를 참조하세요.

**계보**  
계보 탭은 커넥터 공간 개체가 메타버스 개체와 관련된 방법을 보여 줍니다. 커넥터가 연결된 시스템에서 마지막으로 변경 내용을 가져온 시점 및 메타버스에 데이터를 채우는 데 적용된 규칙을 볼 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineage.png)  
**Action** 열에서 **Provision** 작업과 함께 하나의 **인바운드** 동기화 규칙이 있는 것을 볼 수 있습니다. 이는 이 커넥터 공간 개체가 있는 한 메타버스 개체가 그대로 유지됨을 나타냅니다. 대신 동기화 규칙 목록에 방향이 **아웃바운드** 및 **프로비전**인 동기화 규칙이 표시된 경우 이는 메타버스 개체가 삭제될 때 이 개체가 삭제됨을 나타냅니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/cslineageout.png)  
또한 **PasswordSync** 열에서 **True** 값이 있는 동기화 규칙으로 인바운드 커넥터 공간이 암호 변경에 영향을 줄 수 있다는 것을 볼 수 있습니다. 이 암호는 아웃바운드 규칙을 통해 Azure AD로 전송됩니다.

계보 탭에서 [메타버스 개체 속성](#metaverse-object-properties)을 클릭하여 메타버스를 가져올 수 있습니다.

모든 탭의 아래쪽에 두 개의 단추인 **미리 보기**와 **로그**가 있습니다.

**미리 보기**  
미리 보기 페이지는 단일 개체를 동기화하는 데 사용됩니다. 이는 일부 고객 동기화 규칙 문제를 해결하고 단일 개체에서 변경 내용의 영향을 보려는 경우에 유용합니다. **전체 동기화**와 **델타 동기화** 중에서 선택할 수 있습니다. 또한 변경 내용만을 메모리에 유지하는 **미리 보기 생성**과 모든 변경 내용을 대상 커넥터 공간에 준비하는 **커밋 미리 보기** 중에서 선택할 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview1.png)  
개체 및 특정 특성 흐름에 적용되는 규칙을 검사할 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/preview2.png)

**로그**  
로그 페이지를 사용하여 암호 동기화 상태와 기록을 볼 수 있습니다.

### <a name="metaverse-object-properties"></a>메타버스 개체 속성
**특성**  
특성 탭에서는 특성 값과 기여한 커넥터를 볼 수 있습니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvattributes.png)  
**커넥터**  
커넥터 탭은 개체의 표현이 있는 모든 커넥터 공간을 표시합니다.  
![Sync Service Manager](./media/active-directory-aadconnectsync-service-manager-ui/mvconnectors.png)  
이 탭을 사용하여 [커넥터 공간 개체](#connector-space-object-properties)로 이동할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

