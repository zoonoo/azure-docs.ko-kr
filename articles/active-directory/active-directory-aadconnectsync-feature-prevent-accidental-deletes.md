<properties
   pageTitle="Azure AD Connect 동기화: 실수로 인한 삭제 방지 | Microsoft Azure"
   description="이 항목에서는 Azure AD Connect의 실수로 인한 삭제 방지 기능을 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/20/2015"
   ms.author="andkjell"/>

# 실수로 인한 삭제 방지
이 항목에서는 Azure AD Connect의 실수로 인한 삭제 방지 기능을 설명합니다.

Azure AD Connect를 설치하면 실수로 인한 삭제 방지가 기본적으로 사용되며 삭제 수가 500개를 초과하는 내보내기를 허용하지 않도록 구성됩니다. 이 기능은 다수의 사용자에게 영향을 주는 실수에 의한 구성 변경 및 온-프레미스 디렉터리 변경을 방지하기 위한 것입니다.

기본값인 500개 개체는 PowerShell에서 `Enable-ADSyncExportDeletionThreshold`를 사용하여 변경할 수 있습니다. 조직의 규모에 맞게 이 값을 구성해야 합니다. 동기화 스케줄러가 3시간마다 실행되므로 이 값은 3시간 내에 표시되는 삭제 수입니다.

이 기능을 사용하면 Azure AD로 내보내도록 스테이징된 삭제 수가 너무 많을 경우 내보내기가 계속되지 않으며 다음과 같은 메일을 받게 됩니다.

![실수로 인한 삭제 방지 메일](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

예상된 경우가 아니라면 조사하여 수정 작업을 수행합니다. 삭제되는 개체를 확인하려면 다음을 수행합니다.

1. 시작 메뉴에서 **동기화 서비스**를 시작합니다.
2. **커넥터**로 이동합니다.
3. **Azure Active Directory** 유형의 커넥터를 선택합니다.
4. 오른쪽에 있는 **작업**에서 **커넥터 공간 검색**을 선택합니다.
5. **범위** 아래의 팝업에서 **다음 이후 연결이 끊어짐**을 선택하고 과거 시간을 선택합니다. **검색**을 클릭합니다. 삭제되는 모든 개체가 표시됩니다. 각 항목을 클릭하면 개체에 대한 추가 정보를 얻을 수 있습니다. **열 설정**을 클릭하여 그리드에 표시되는 특성을 더 추가할 수도 있습니다.

![커넥터 공간 검색](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

모든 삭제를 진행하려면 다음을 수행합니다.

1. 일시적으로 이 보호를 해제하고 삭제를 진행할 수 있도록 하려면 PowerShell cmdlet `Disable-ADSyncExportDeletionThreshold`를 실행합니다. 자격 증명을 물으면 Azure AD 전역 관리자 계정 및 암호를 제공합니다.![자격 증명](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Azure Active Directory 커넥터를 선택한 상태로 **실행** 작업, **내보내기**를 차례로 선택합니다.
3. 보호를 다시 사용하도록 설정하려면 PowerShell cmdlet `Enable-ADSyncExportDeletionThreshold`를 실행합니다.

## 다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=Oct15_HO4-->