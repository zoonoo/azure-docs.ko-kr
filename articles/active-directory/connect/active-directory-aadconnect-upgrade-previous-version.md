---
title: "Azure AD Connect: 이전 버전에서 업그레이드 | Microsoft Docs"
description: "전체 업그레이드와 스윙 마이그레이션을 포함하여 최신 릴리스의 Azure Active Directory Connect로 업그레이드하는 여러 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 31f084d8-2b89-478c-9079-76cf92e6618f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b6ec60f9e15e459f70127448eb7d9c03e4b118e8
ms.openlocfilehash: 3bd1ca8e0bb9f17b76dda68a6cb2f9f64a5d05dd
ms.lasthandoff: 02/23/2017


---
# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Azure AD Connect: 이전 버전에서 최신 버전으로 업그레이드
이 항목에서는 Azure Active Directory(Azure AD) Connect 설치를 최신 릴리스로 업그레이드하는 데 사용할 수 있는 여러 가지 방법을 설명합니다. Azure AD Connect 릴리스를 최신 상태로 유지하는 것이 좋습니다. [스윙 마이그레이션](#swing-migration) 섹션에 설명된 단계는 상당한 구성 변경을 수행하는 경우에도 사용할 수 있습니다.

DirSync에서 업그레이드하려는 경우 대신 [Azure AD 동기화 도구(DirSync)에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md) 를 참조하세요.

Azure AD Connect를 업그레이드하는 데 사용할 수 있는 몇 가지 전략이 있습니다.

| 메서드 | 설명 |
| --- | --- |
| [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md) |빠른 설치를 사용하는 고객에게 가장 쉬운 방법입니다. |
| [전체 업그레이드](#in-place-upgrade) |단일 서버가 있는 경우 동일한 서버에 설치된 항목에 대해 전체 업그레이드를 수행할 수 있습니다. |
| [스윙 마이그레이션](#swing-migration) |두 개의 서버가 있는 경우 서버 중 하나를 새 릴리스 또는 구성으로 준비하고, 준비가 완료되면 활성 서버를 변경할 수 있습니다. |

사용 권한 정보는 [업그레이드에 필요한 사용 권한](active-directory-aadconnect-accounts-permissions.md#upgrade)을 참조하세요.

> [!NOTE]
> 새 Azure AD Connect 서버를 사용하여 변경 내용을 Azure AD에 동기화하기 시작하면 DirSync 또는 Azure AD Sync를 사용하도록 롤백하지 않아야 합니다. Azure AD Connect에서 DirSync 및 Azure AD Sync를 포함하여 레거시 클라이언트로 다운그레이드하는 기능은 지원되지 않으며 Azure AD에서 데이터 손실 등의 문제가 발생할 수 있습니다.

## <a name="in-place-upgrade"></a>전체 업그레이드
전체 업그레이드는 Azure AD Sync 또는 Azure AD Connect에서 이동하는 경우에 작동합니다. Forefront Identity Manager(FIM) + Azure AD 커넥터를 사용하는 솔루션이거나 DirSync에서 전환하는 경우에는 작동하지 않습니다.

이 방법은 단일 서버가 있고 개체 수가 100,000개 미만인 경우에 사용하는 것이 좋습니다. 기본 동기화 규칙이 변경된 경우, 업그레이드 후에 전체 가져오기 및 전체 동기화가 발생합니다. 이를 통해 시스템의 모든 기존 개체에 새 구성이 적용됩니다. 이 작업은 동기화 엔진 범위에 속하는 개체 수에 따라 몇 시간이 걸릴 수 있습니다. 기본적으로 30분마다 동기화되는 일반 델타 동기화 스케줄러는 일시 중단되지만 암호 동기화는 계속됩니다. 따라서 주말 동안 전체 업그레이드를 수행하는 것이 좋습니다. 새 Azure AD Connect 릴리스를 포함한 기본 구성이 변경되지 않은 경우 대신 일반 델타 가져오기/동기화가 시작됩니다.  
![전체 업그레이드](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

기본 동기화 규칙을 변경한 경우 업그레이드 시 기본 구성으로 다시 설정됩니다. 업그레이드 간에 구성을 유지하려면 [기본 구성 변경에 대한 모범 사례](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)에 설명된 대로 변경해야 합니다.

## <a name="swing-migration"></a>스윙 마이그레이션
배포가 복잡하거나 개체가 많은 경우에는 라이브 시스템에서 전체 업그레이드를 수행하는 것이 적절하지 않을 수 있습니다. 일부 고객의 경우 며칠이 걸리고 이 시간 동안 델타 변경이 처리되지 않을 수 있습니다. 이 방법은 구성을 크게 변경하고 클라우드로 푸시하기 전에 시도하려는 경우에도 사용할 수 있습니다.

이 시나리오에서 권장되는 방법은 스윙 마이그레이션을 사용하는 것입니다. 활성 서버 하나와 스테이징 서버 하나, (적어도) 두 개의 서버가 필요합니다. 활성 서버(아래 그림에 파란색 실선으로 표시됨)는 활성 프로덕션 부하를 담당합니다. 스테이징 서버(자주색 파선으로 표시됨)는 새 릴리스 또는 구성으로 준비됩니다. 완벽하게 준비되면 이 서버가 활성 상태가 됩니다. 이제 이전 버전 또는 구성이 설치된 이전 활성 서버가 스테이징 서버가 되며 업그레이드됩니다.

두 서버는 서로 다른 버전을 사용할 수 있습니다. 예를 들어, 서비스를 해제할 활성 서버는 Azure AD Sync를 사용할 수 있고 새 준비 서버는 Azure AD Connect를 사용할 수 있습니다. 스윙 마이그레이션을 사용하여 새 구성을 개발하는 경우 두 서버에서 동일한 버전을 사용하는 것이 좋습니다.  
![스테이징 서버](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

> [!NOTE]
> 일부 고객은 이 시나리오를 위해 기본적으로 서너 대의 서버를 유지합니다. 스테이징 서버가 업그레이드되면, [재해 복구](active-directory-aadconnectsync-operations.md#disaster-recovery)를 위한 백업 서버가 없습니다. 서너 대의 서버가 있으면 새 버전의 기본/대기 서버 집합을 준비할 수 있으므로 스테이징 서버가 항상 준비된 상태로 유지됩니다.

또한 이러한 단계는 Azure AD Sync에서 전환하거나 FIM과 Azure AD 커넥터를 사용하는 솔루션일 경우에도 작동합니다. 이러한 단계는 DirSync에서 작동하지 않지만 DirSync 단계를 사용하는 동일한 스윙 마이그레이션 방법(병렬 배포라고도 함)은 [Azure Active Directory 동기화(DirSync) 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md)에서 확인할 수 있습니다.

### <a name="use-a-swing-migration-to-upgrade"></a>스윙 마이그레이션을 사용하여 업그레이드
1. 두 서버 모두에서 Azure AD Connect를 사용하고 구성 변경만 수행하려는 경우 활성 서버와 스테이징 서버에서 동일한 버전을 사용하고 있는지 확인합니다. 그러면 나중에 쉽게 차이를 비교할 수 있습니다. Azure AD Sync에서 업그레이드하는 경우 두 서버는 서로 다른 버전을 사용하게 됩니다. Azure AD Connect 이전 버전에서 업그레이드 하는 경우, 동일한 버전을 사용하는 두 서버로 시작하는 것이 좋지만 필수는 아닙니다.
2. 일부 사용자 지정 구성을 적용했는데 스테이징 서버에 해당 구성이 없는 경우 [활성 서버에서 스테이징 서버로 사용자 지정 구성 이동](#move-custom-configuration-from-active-to-staging-server)에 설명된 단계를 따릅니다.
3. Azure AD Connect의 이전 릴리스에서 업그레이드하는 경우 스테이징 서버를 최신 버전으로 업그레이드합니다. Azure AD Sync에서 전환하는 경우 스테이징 서버에 Azure AD Connect를 설치합니다.
4. 동기화 엔진에서 전체 가져오기를 실행하고 준비 서버에서 전체 동기화를 실행합니다.
5. [서버의 구성 확인](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)에서 "확인" 아래의 단계를 사용하여 새 구성으로 인해 예기치 않은 변경이 발생하지 않았는지 확인합니다. 올바르지 않은 항목이 있을 경우 단계를 따라 수정하고, 가져오기 및 동기화를 실행하고, 데이터가 올바르게 될 때까지 확인합니다.
6. 스테이징 서버를 활성 서버로 전환합니다. 이는 [서버의 구성 확인](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server)에서 마지막 단계인 "활성 서버 전환" 단계입니다.
7. Azure AD Connect를 업그레이드하는 경우 이제 준비 모드의 서버를 최신 릴리스로 업그레이드합니다. 이전과 동일한 단계에 따라 데이터 및 구성을 업그레이드합니다. Azure AD Sync에서 업그레이드 한 경우, 이제 이전 서버를 끄고 서비스 해제를 할 수 있습니다.

### <a name="move-a-custom-configuration-from-the-active-server-to-the-staging-server"></a>활성 서버에서 스테이징 서버로 사용자 지정 구성 이동
활성 서버에 대한 구성을 변경한 경우 해당 변경 내용을 스테이징 서버에 적용해야 합니다.

PowerShell을 사용하여 만든 사용자 지정 동기화 규칙을 이동할 수 있습니다. 두 시스템 모두에서 동일한 방식으로 다른 변경 내용을 적용해야 하고, 변경 내용은 마이그레이션할 수 없습니다.

두 서버에서 다음과 같은 항목을 동일한 방식으로 구성해야 합니다.

* 동일한 포리스트에 대한 연결
* 모든 도메인 및 OU 필터링
* 동일한 선택적 기능(예: 암호 동기화 및 비밀번호 쓰기 저장)

**사용자 지정 동기화 규칙 이동**  
사용자 지정 동기화 규칙을 이동하려면 다음을 수행합니다.

1. 활성 서버에서 **동기화 규칙 편집기** 를 엽니다.
2. 사용자 지정 규칙을 선택합니다. **내보내기**를 클릭합니다. 그러면 메모장 창이 열립니다. PS1 확장명으로 임시 파일을 저장합니다. 이렇게하면 PowerShell 스크립트가 됩니다. PS1 파일을 스테이징 서버에 복사합니다.  
   ![동기화 규칙 내보내기](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. 스테이징 서버에서는 커넥터 GUID가 다르므로 변경해야 합니다. GUID를 가져오려면 **동기화 규칙 편집기**를 시작하여 동일한 연결된 시스템을 나타내는 기본 규칙 중 하나를 선택하고 **내보내기**를 클릭합니다. PS1 파일의 GUID를 스테이징 서버의 GUID로 바꿉니다.
4. PowerShell 프롬프트에서 PS1 파일을 실행합니다. 스테이징 서버에 사용자 지정 동기화 규칙이 만들어집니다.
5. 모든 사용자 지정 규칙에 대해 이 단계를 반복합니다.

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

