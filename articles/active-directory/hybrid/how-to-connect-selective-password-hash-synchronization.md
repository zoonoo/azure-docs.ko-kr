---
title: Azure AD Connect에 대 한 선택적 암호 해시 동기화
description: 이 문서에서는 Azure AD Connect와 함께 사용할 선택적 암호 해시 동기화를 설정 하 고 구성 하는 방법을 설명 합니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2021
ms.subservice: hybrid
ms.author: billmath
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 774c78cbb09d2e5e60dfc0cafc0082b25e9b1b45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103603104"
---
# <a name="selective-password-hash-synchronization-configuration-for-azure-ad-connect"></a>Azure AD Connect에 대 한 선택적 암호 해시 동기화 구성

[암호 해시 동기화](whatis-phs.md) 는 하이브리드 id를 수행 하는 데 사용 되는 로그인 방법 중 하나입니다. Azure AD Connect는 사용자 암호 해시의 해시를 온-프레미스 Active Directory 인스턴스에서 클라우드 기반 Azure AD 인스턴스로 동기화합니다.  기본적으로 설정 된 후에는 동기화 하는 모든 사용자에 대해 암호 해시 동기화가 수행 됩니다.

Azure AD에 대 한 암호 해시 동기화에서 제외 된 사용자의 하위 집합을 사용 하려는 경우이 문서에 제공 된 단계별 단계를 사용 하 여 선택적 암호 해시 동기화를 구성할 수 있습니다.

>[!Important]
> Microsoft는 공식적으로 문서화된 구성 또는 작업 외의 Azure AD Connect 동기화에 대한 수정 또는 작업을 지원하지 않습니다. 이러한 구성 또는 작업으로 인해 Azure AD Connect 동기화 상태가 일치 하지 않거나 지원 되지 않을 수 있습니다. 따라서 Microsoft는 이러한 배포에 대 한 효율적인 기술 지원을 제공할 수 있도록 보장 하지 않습니다. 


## <a name="consider-your-implementation"></a>구현 고려  
구성 관리 작업을 줄이려면 먼저 암호 해시 동기화에서 제외 하려는 사용자 개체의 수를 고려해 야 합니다. 함께 사용할 수 없는 아래 시나리오 중에서 적절 한 구성 옵션을 선택 하기 위한 요구 사항에 부합 하는 시나리오를 확인 합니다.
- **제외할** 사용자 수가 **포함할** 사용자 수보다 **작은** 경우에는이 [섹션](#excluded-users-is-smaller-than-included-users)의 단계를 수행 합니다.
- **제외할** 사용자 수가 **포함할** **사용자 수보다 많은** 경우이 [섹션](#excluded-users-is-larger-than-included-users)의 단계를 수행 합니다.

> [!Important]
> 선택한 구성 옵션을 사용 하 여 변경 내용을 적용 하는 데 필요한 초기 동기화 (전체 동기화)가 다음 동기화 주기 동안 자동으로 수행 됩니다.

### <a name="the-admindescription-attribute"></a>AdminDescription 특성
두 시나리오 모두 사용자의 adminDescription 특성을 특정 값으로 설정 하는 데 사용 됩니다.  이렇게 하면 규칙을 적용할 수 있으며,이를 통해 선택적 PHS가 작동 합니다.

|시나리오|adminDescription 값|
|-----|-----|
|제외 된 사용자가 포함 된 사용자 보다 작음|PHSFiltered 됨|
|제외 된 사용자가 포함 된 사용자 보다 큼|PHSIncluded|

이 특성은 다음 중 하나로 설정할 수 있습니다.

- Active Directory 사용자 및 컴퓨터 UI 사용
- `Set-ADUser`PowerShell cmdlet을 사용 합니다.  자세한 내용은 [Set-ADUser](https://docs.microsoft.com/powershell/module/addsadministration/set-aduser)를 참조 하십시오.

 


### <a name="disable-the-synchronization-scheduler"></a>동기화 스케줄러를 사용 하지 않도록 설정 합니다.
두 시나리오를 시작 하기 전에 동기화 규칙을 변경 하는 동안 동기화 스케줄러를 사용 하지 않도록 설정 해야 합니다.
 1. Windows PowerShell을 시작 합니다.

     ```Set-ADSyncScheduler -SyncCycleEnabled $false``` 
 
2.  다음 cmdlet을 실행 하 여 스케줄러를 사용 하지 않도록 설정 했는지 확인 합니다.
     
    ```Get-ADSyncScheduler```

스케줄러에 대 한 자세한 내용은 [Azure AD Connect sync scheduler](how-to-connect-sync-feature-scheduler.md)를 참조 하세요.




## <a name="excluded-users-is-smaller-than-included-users"></a>제외 된 사용자가 포함 된 사용자 보다 작음
다음 섹션에서는 **제외할** 사용자 수가 **포함할** 사용자 수보다 **작은** 경우 선택적 암호 해시 동기화를 사용 하도록 설정 하는 방법을 설명 합니다.

>[!Important]
> 계속 하기 전에 위에 설명 된 대로 동기화 스케줄러를 사용 하지 않도록 설정 해야 합니다.

- **암호 해시 동기화를 선택 취소** 하 고 범위 지정 필터를 정의 하는 옵션을 사용 하 여 **In From AD – User accountenabled** 의 편집 가능한 복사본을 만듭니다. 
- **선택 된 암호 해시 동기화를 사용 하도록 설정** 하 고 범위 지정 필터를 정의 하는 옵션을 사용 하 여 **AD에서 기본 설정** 의 다른 편집 가능한 복사본을 만듭니다. 
- 동기화 스케줄러를 다시 사용 하도록 설정 
- Active directory에서 암호 해시 동기화에 허용 하려는 사용자의 범위 특성으로 정의 된 특성 값을 설정 합니다. 

>[!Important]
>선택적 암호 해시 동기화를 구성 하기 위해 제공 되는 단계는 Active Directory 값이 **Phsfiltered** 설정 된 **admindescription** 특성이 있는 사용자 개체에만 적용 됩니다.
이 특성이 채워지지 않거나 값이 **Phsfiltered** 아닌 다른 경우 이러한 규칙은 사용자 개체에 적용 되지 않습니다.


### <a name="configure-the-necessary-synchronization-rules"></a>필요한 동기화 규칙을 구성 합니다.

 1. 동기화 규칙 편집기를 시작 하 고 **암호 동기화** 필터를 **켜기** 로 설정 하 고 **규칙 유형** 을 **표준** 으로 설정 합니다.
     ![동기화 규칙 편집기 시작](media/how-to-connect-selective-password-hash-synchronization/exclude-1.png)
 2. **In FROM AD – User AccountEnabled** 에서 규칙을 선택 하 고 선택적 암호를 구성 하려는 Active Directory 포리스트 커넥터에 대해 해시 동기화가 설정 되어 있는 경우 **편집** 을 클릭 합니다. 다음 대화 상자에서 **예** 를 선택 하 여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![규칙 선택](media/how-to-connect-selective-password-hash-synchronization/exclude-2.png)
 3. 첫 번째 규칙은 암호 해시 동기화를 사용 하지 않도록 설정 합니다. 새 사용자 지정 규칙에 다음 이름을 제공 합니다. **In FROM AD-User AccountEnabled-PHS에서 사용자를 필터링** 합니다.
 우선 순위 값을 100 보다 작은 숫자로 변경 합니다 ( **90** 예: 사용자 환경에서 사용할 수 있는 가장 낮은 값).
 **암호 동기화** 및 **사용 안 함** 확인란을 선택 하지 않은 상태에서 c로 설정 합니다.
 **다음** 을 클릭합니다.
  ![인바운드 편집](media/how-to-connect-selective-password-hash-synchronization/exclude-3.png)
 4. **범위 지정 필터** 에서 **절 추가** 를 클릭 합니다.
 특성 열에서 **Admindescription** 을 선택 하 고 연산자 열에서 **같음** 을 선택 하 고 값으로 **phsfiltered** 를 입력 합니다.
     ![범위 지정 필터](media/how-to-connect-selective-password-hash-synchronization/exclude-4.png)
 5. 추가로 변경할 필요가 없습니다. **조인 규칙** 및 **변환은** 복사 된 기본 설정으로 남겨 두어야 하므로 지금 **저장** 을 클릭할 수 있습니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행 되는 것을 알리는 경고 대화 상자에서 **확인을** 클릭 합니다.
     ![규칙 저장](media/how-to-connect-selective-password-hash-synchronization/exclude-5.png)
 6. 다음으로 암호 해시 동기화가 사용 하도록 설정 된 다른 사용자 지정 규칙을 만듭니다. 선택적 암호를 동기화 할 수 있도록 구성할 Active Directory 포리스트에 대해 **AD에서** 기본 규칙을 다시 선택 하 고 **편집** 을 클릭 합니다. 다음 대화 상자에서 **예** 를 선택 하 여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![사용자 지정 규칙](media/how-to-connect-selective-password-hash-synchronization/exclude-6.png)
 7. 새 사용자 지정 규칙에 **대해 In FROM AD-User AccountEnabled-PHS에 포함 된 사용자** 에 게 다음 이름을 제공 합니다.
 우선 순위 값을 이전에 만든 규칙 보다 낮은 수로 변경 합니다 (이 예제에서는 **89** 이 됨).
 **암호 동기화 사용** 확인란을 선택 하 고 **사용 안 함** 확인란을 선택 하지 않았는지 확인 합니다.
 **다음** 을 클릭합니다.  
     ![새 규칙 편집](media/how-to-connect-selective-password-hash-synchronization/exclude-7.png)
 8. **범위 지정 필터** 에서 **절 추가** 를 클릭 합니다.
 특성 열에서 **Admindescription** 을 선택 하 고 연산자 열에서을 **참고** 합니다. 값으로 **phsfiltered** 를 입력 합니다.
     ![범위 규칙](media/how-to-connect-selective-password-hash-synchronization/exclude-8.png)
 9. 추가로 변경할 필요가 없습니다. **조인 규칙** 및 **변환은** 복사 된 기본 설정으로 남겨 두어야 하므로 지금 **저장** 을 클릭할 수 있습니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행 되는 것을 알리는 경고 대화 상자에서 **확인을** 클릭 합니다.
     ![조인 규칙](media/how-to-connect-selective-password-hash-synchronization/exclude-9.png)
 10. 규칙 만들기를 확인 합니다. **암호 동기화** 및 **규칙 유형** **표준** **의** 필터를 제거 합니다. 방금 만든 새 규칙이 모두 표시 되어야 합니다.
     ![규칙 확인](media/how-to-connect-selective-password-hash-synchronization/exclude-10.png) 


### <a name="re-enable-synchronization-scheduler"></a>동기화 스케줄러를 다시 사용 하도록 설정 합니다.  
필요한 동기화 규칙을 구성 하는 단계를 완료 한 후에는 다음 단계에 따라 동기화 스케줄러를 다시 사용 하도록 설정 합니다.
 1. Windows PowerShell에서 다음을 실행 합니다.

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. 다음을 실행 하 여 성공적으로 설정 되었는지 확인 합니다.

     ```Get-ADSyncScheduler```

스케줄러에 대 한 자세한 내용은 [Azure AD Connect sync scheduler](how-to-connect-sync-feature-scheduler.md)를 참조 하세요.

### <a name="edit-users-admindescription-attribute"></a>사용자 **Admindescription** 특성 편집:
모든 구성이 완료 되 면 Active Directory의 암호 해시 동기화에서 **제외** 하려는 모든 사용자에 대해 **admindescription** 특성을 편집 하 고, 범위 지정 필터에 사용 되는 문자열을 추가 해야 합니다. **phsfiltered** 합니다.
   
  ![특성 편집](media/how-to-connect-selective-password-hash-synchronization/exclude-11.png)


## <a name="excluded-users-is-larger-than-included-users"></a>제외 된 사용자가 포함 된 사용자 보다 큼
다음 섹션에서는 **제외할** 사용자 수가 **포함할** 사용자 수보다 많은 경우 선택적 암호 해시 **동기화를 사용** 하도록 설정 하는 방법을 설명 합니다.

>[!Important]
> 계속 하기 전에 위에 설명 된 대로 동기화 스케줄러를 사용 하지 않도록 설정 해야 합니다.

다음은 아래 단계에서 수행 되는 작업에 대 한 요약입니다.

- **암호 해시 동기화를 선택 취소** 하 고 범위 지정 필터를 정의 하는 옵션을 사용 하 여 **In From AD – User accountenabled** 의 편집 가능한 복사본을 만듭니다. 
- **선택 된 암호 해시 동기화를 사용 하도록 설정** 하 고 범위 지정 필터를 정의 하는 옵션을 사용 하 여 **AD에서 기본 설정** 의 다른 편집 가능한 복사본을 만듭니다. 
- 동기화 스케줄러를 다시 사용 하도록 설정 
- Active directory에서 암호 해시 동기화에 허용 하려는 사용자의 범위 특성으로 정의 된 특성 값을 설정 합니다. 

>[!Important]
>선택적 암호 해시 동기화를 구성 하기 위해 제공 되는 단계는 Active Directory 값이 **Phsincluded** 값과 함께 지정 된 **admindescription** 특성이 있는 사용자 개체에만 적용 됩니다.
이 특성이 채워지지 않거나 값이 **Phsincluded** 이외의 값 이면 이러한 규칙은 사용자 개체에 적용 되지 않습니다.


### <a name="configure-the-necessary-synchronization-rules"></a>필요한 동기화 규칙을 구성 합니다.

 1. 동기화 규칙 편집기를 시작 하 고 **암호 동기화** **필터 및** **규칙 유형** **표준** 을 설정 합니다.
     ![규칙 유형](media/how-to-connect-selective-password-hash-synchronization/include-1.png)
 2. 선택적 암호를 동기화 할 수 있도록 구성할 Active Directory 포리스트에 대해 **AD에서** 규칙을 선택 하 고 **편집** 을 클릭 합니다. 다음 대화 상자에서 **예** 를 선택 하 여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![In from AD](media/how-to-connect-selective-password-hash-synchronization/include-2.png)
 3. 첫 번째 규칙은 암호 해시 동기화를 사용 하지 않도록 설정 합니다. 새 사용자 지정 규칙에 다음 이름을 제공 합니다. **In FROM AD-User AccountEnabled-PHS에서 사용자를 필터링** 합니다.
 우선 순위 값을 100 보다 작은 숫자로 변경 합니다 ( **90** 예: 사용자 환경에서 사용할 수 있는 가장 낮은 값).
 **암호 동기화 사용** 및 **사용 안 함** 확인란이 선택 취소 되어 있는지 확인 합니다.
 **다음** 을 클릭합니다.
  ![우선 순위 설정](media/how-to-connect-selective-password-hash-synchronization/include-3.png)
 4. **범위 지정 필터** 에서 **절 추가** 를 클릭 합니다.
특성 열에서 **Admindescription** 을 선택 하 고 연산자 열에서을 **참고** 합니다. 값으로 **phsincluded** 을 입력 합니다.
     ![절 추가](media/how-to-connect-selective-password-hash-synchronization/include-4.png)
 5. 추가로 변경할 필요가 없습니다. **조인 규칙** 및 **변환은** 복사 된 기본 설정으로 남겨 두어야 하므로 지금 **저장** 을 클릭할 수 있습니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행 되는 것을 알리는 경고 대화 상자에서 **확인을** 클릭 합니다.
     ![변환](media/how-to-connect-selective-password-hash-synchronization/include-5.png)
 6. 다음으로 암호 해시 동기화가 사용 하도록 설정 된 다른 사용자 지정 규칙을 만듭니다. 선택적 암호를 동기화 할 수 있도록 구성할 Active Directory 포리스트에 대해 **AD에서** 기본 규칙을 다시 선택 하 고 **편집** 을 클릭 합니다. 다음 대화 상자에서 **예** 를 선택 하 여 원래 규칙의 편집 가능한 복사본을 만듭니다.
     ![사용자 AccountEnabled](media/how-to-connect-selective-password-hash-synchronization/include-6.png)
 7. 새 사용자 지정 규칙에 **대해 In FROM AD-User AccountEnabled-PHS에 포함 된 사용자** 에 게 다음 이름을 제공 합니다.
 우선 순위 값을 이전에 만든 규칙 보다 낮은 수로 변경 합니다 (이 예제에서는 **89** 이 됨).
 **암호 동기화 사용** 확인란을 선택 하 고 **사용 안 함** 확인란을 선택 하지 않았는지 확인 합니다.
 **다음** 을 클릭합니다.
     ![암호 동기화 사용](media/how-to-connect-selective-password-hash-synchronization/include-7.png)
 8. **범위 지정 필터** 에서 **절 추가** 를 클릭 합니다.
 특성 열에서 **Admindescription** 을 선택 하 고 연산자 열에서 **같음** 을 선택 하 고 값으로 **phsincluded** 을 입력 합니다.
     ![PHSIncluded](media/how-to-connect-selective-password-hash-synchronization/include-8.png)
 9. 추가로 변경할 필요가 없습니다. **조인 규칙** 및 **변환은** 복사 된 기본 설정으로 남겨 두어야 하므로 지금 **저장** 을 클릭할 수 있습니다.
 커넥터의 다음 동기화 주기에서 전체 동기화가 실행 되는 것을 알리는 경고 대화 상자에서 **확인을** 클릭 합니다.
     ![지금 저장](media/how-to-connect-selective-password-hash-synchronization/include-9.png)
 10.    규칙 만들기를 확인 합니다. **암호 동기화** 및 **규칙 유형** **표준** **의** 필터를 제거 합니다. 방금 만든 새 규칙이 모두 표시 되어야 합니다.
     ![동기화 설정](media/how-to-connect-selective-password-hash-synchronization/include-10.png)

### <a name="re-enable-synchronization-scheduler"></a>동기화 스케줄러를 다시 사용 하도록 설정 합니다.  
필요한 동기화 규칙을 구성 하는 단계를 완료 한 후에는 다음 단계에 따라 동기화 스케줄러를 다시 사용 하도록 설정 합니다.
 1. Windows PowerShell에서 다음을 실행 합니다.

     ```Set-ADSyncScheduler -SyncCycleEnabled $true```
 2. 다음을 실행 하 여 성공적으로 설정 되었는지 확인 합니다.

     ```Get-ADSyncScheduler```

스케줄러에 대 한 자세한 내용은 [Azure AD Connect sync scheduler](how-to-connect-sync-feature-scheduler.md)를 참조 하세요.

### <a name="edit-users-admindescription-attribute"></a>사용자 **Admindescription** 특성 편집:
모든 구성이 완료 되 면 Active Directory에서 암호 해시 동기화에 **포함** 하려는 모든 사용자에 대해 **admindescription** 특성을 편집 하 고, 범위 지정 필터에 사용 되는 문자열을 추가 해야 합니다. **phsincluded** 합니다.

  ![특성 편집](media/how-to-connect-selective-password-hash-synchronization/include-11.png)
 
 

## <a name="next-steps"></a>다음 단계
- [암호 해시 동기화란?](whatis-phs.md)
- [암호 해시 동기화 작동 방식](how-to-connect-password-hash-synchronization.md)
