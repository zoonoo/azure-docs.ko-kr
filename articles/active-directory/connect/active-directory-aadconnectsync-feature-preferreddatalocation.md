---
title: "Azure AD Connect 동기화: Office 365에서 다중 지역 기능에 대한 기본 데이터 위치 구성 | Microsoft Docs"
description: "Azure AD Connect 동기화를 사용하여 Office 365 사용자 리소스를 사용자에게 가깝게 배치하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billmath
ms.openlocfilehash: 021f009e66e57665a2252646b210f0e6dc55d33c
ms.sourcegitcommit: e19742f674fcce0fd1b732e70679e444c7dfa729
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="azure-ad-connect-sync-configure-preferred-data-location-for-office-365-resources"></a>Azure AD Connect 동기화: Office 365 리소스에 대한 기본 데이터 위치 구성
이 항목의 목적은 Azure AD Connect 동기화에서 PreferredDataLocation을 구성하는 방법을 안내하는 것입니다. 고객이 Office 365에서 다중 지역 기능을 사용하는 경우 이 특성을 사용해서 사용자의 Office 365 데이터에 대한 지리적 위치를 지정합니다. 용어 **region(지역)**과 **Geo(지역)**는 서로 바꿔 사용할 수 있습니다.

> [!IMPORTANT]
> 다중 지역은 현재 미리 보기로 제공되고 있습니다. 미리 보기 프로그램에 가입하려는 경우 Microsoft 담당자에게 문의하세요.
>
>

## <a name="enable-synchronization-of-preferreddatalocation"></a>PreferredDataLocation 동기화 사용
기본적으로 사용자를 위한 Office 365 리소스는 Azure AD 테넌트와 같은 지역에 있습니다. 예를 들어 테넌트가 북아메리카에 있는 경우 사용자의 Exchange 사서함도 북아메리카에 있습니다. 다국적 조직의 경우 이 상황은 최적이 아닐 수 있습니다. 특성 preferredDataLocation을 설정하여 사용자의 지역을 정의할 수 있습니다.

이 특성을 설정하여 사서함 및 OneDrive와 같은 사용자의 Office 365 리소스를 사용자가 있는 동일한 지역에 두고, 전체 조직에 대해 하나의 테넌트만 유지할 수 있습니다.

> [!IMPORTANT]
> 다중 지역을 사용할 자격을 얻으려면 Office 365 구독에 5,000명 이상의 사용자 수가 포함되어야 합니다.
>
>

Office 365의 모든 지역 목록은 [데이터 위치](https://aka.ms/datamaps)에서 찾을 수 있습니다.

다중 지역 기능을 사용할 수 있는 Office 365의 지역은 다음과 같습니다.

| 지역 | preferredDataLocation value |
| --- | --- |
| 아시아 태평양 | APC |
| 오스트레일리아 | AUS |
| 캐나다 | CAN |
| 유럽 연합 | EUR |
| 인도 | IND |
| 일본 | JPN |
| 대한민국 | KOR |
| 영국 | GBR |
| 미국 | NAM |

* 지역이 이 표에 없는 경우(예: 남아메리카) 다중 지역에 사용할 수 없습니다.
* 인도 및 대한민국 지역은 청구 주소와 해당 지역에서 구입한 라이선스가 있는 고객만 사용할 수 있습니다.
* 일부 Office 365 워크로드는 사용자의 지역 설정 사용을 지원하지 않습니다.

Azure AD Connect는 1.1.524.0 이상 버전의 **User** 개체에 대한 **PreferredDataLocation** 특성의 동기화를 지원합니다. 구체적으로 다음과 같은 변경 내용이 도입되었습니다.

* Azure AD 커넥터의 **User** 개체 형식의 스키마가 단일 값 문자열 형식인 PreferredDataLocation 특성을 포함하도록 확장되었습니다.
* 메타버스의 **Person** 개체 형식의 스키마가 문자열 형식의 단일 값인 PreferredDataLocation 특성을 포함하도록 확장되었습니다.

기본적으로 PreferredDataLocation 특성은 동기화가 허용되지 않습니다. 이 기능은 대규모 조직을 위한 기능으로, 모든 사용자에게 유용하지 않을 수도 있습니다. 또한 온-프레미스 Active Directory에는 PreferredDataLocation 특성이 없으므로 사용자의 Office 365 지역을 포함할 수 있는 특성을 확인해야 합니다. 이러한 특성은 각 조직마다 다를 수 있습니다.

> [!IMPORTANT]
> 현재 Azure AD를 사용하면 Azure AD PowerShell을 사용하여 동기화된 User 개체와 클라우드 User 개체 모두의 PreferredDataLocation 특성을 직접 구성할 수 있습니다. PreferredDataLocation 특성의 동기화를 사용하도록 설정한 후에는 Azure AD Connect에서 온-프레미스 Active Directory의 원본 특성 값에 따라 **동기화된 User 개체**를 재정의하므로 Azure AD PowerShell을 사용하여 이러한 개체의 특성을 구성하는 것을 중지해야 합니다 .

> [!IMPORTANT]
> 2017년 9월 1일부터 Azure AD는 더 이상 Azure AD PowerShell을 사용하여 **동기화된 User 개체**의 PreferredDataLocation 특성을 직접 구성하도록 허용하지 않습니다. 동기화된 User 개체에 PreferredLocation 특성을 구성하려면 Azure AD Connect를 사용해야 합니다.

PreferredDataLocation 특성의 동기화를 사용하도록 설정하기 전에 다음을 수행해야 합니다.

* 먼저 온-프레미스 Active Directory 특성을 원본 특성으로 사용할지 결정합니다. 이 특성은 **단일 값 문자열** 형식이어야 합니다. 아래 단계에서 extensionAttributes 중 하나를 사용합니다.
* 이전에 Azure AD PowerShell을 사용하여 Azure AD의 동기화된 기존 User 개체에 PreferredDataLocation 특성을 구성한 경우, 특성 값을 온-프레미스 Active Directory의 해당 User 개체로 **백포팅**해야 합니다.

    > [!IMPORTANT]
    > 특성 값을 온-프레미스 Active Directory의 해당 User 개체로 백포팅하지 않으면, PreferredDataLocation 특성에 대한 동기화를 사용하도록 설정할 때 Azure AD Connect에서 Azure AD의 기존 특성 값을 제거합니다.

* 이제 두 개 이상의 온-프레미스 AD User 개체에 원본 특성을 구성하는 것이 좋습니다. 이러한 개체는 나중에 확인하는 데 사용할 수 있습니다.

PreferredDataLocation 특성의 동기화를 사용하도록 설정하는 단계는 다음과 같이 요약할 수 있습니다.

1. 동기화 스케줄러를 비활성화하고 진행 중인 동기화가 없는지 확인합니다.
2. 온-프레미스 ADDS 커넥터 스키마에 원본 특성 추가
3. Azure AD 커넥터 스키마에 PreferredDataLocation 추가
4. 온-프레미스 Active Directory에서 특성 값을 전달하는 인바운드 동기화 규칙 만들기
5. Azure AD로 특성 값을 전달하는 아웃바운드 동기화 규칙 만들기
6. 전체 동기화 주기 실행
7. 동기화 스케줄러를 사용하도록 설정
8. 결과 확인

> [!NOTE]
> 이 섹션의 나머지 부분에서는 이러한 단계에 대해 자세히 설명하며, 단일 포리스트 토폴로지를 사용하고 사용자 지정 동기화 규칙이 없는 Azure AD 배포와 관련됩니다. 다중 포리스트 토폴로지, 사용자 지정 동기화 규칙이 구성되어 있거나 스테이징 서버가 있으면 이에 따라 단계를 조정해야 합니다.

## <a name="step-1-disable-sync-scheduler-and-verify-there-is-no-synchronization-in-progress"></a>1단계: 동기화 스케줄러 비활성화 및 진행 중인 동기화가 없는지 확인
Azure AD로 의도하지 않은 변경 내용을 내보내지 않도록 동기화 규칙을 업데이트하는 중에 동기화가 수행되지 않도록 합니다. 기본 제공 동기화 스케줄러를 비활성화하려면

1. Azure AD Connect 서버에서 PowerShell 세션을 시작합니다.
2. cmdlet을 실행하여 예약된 동기화 비활성화: `Set-ADSyncScheduler -SyncCycleEnabled $false`.
3. **시작** > **동기화 서비스**로 이동하여 **Synchronization Service Manager**를 시작합니다.
4. **작업** 탭으로 이동하고 상태가 *진행 중*인 작업이 없는지 확인합니다.

![동기화 서비스 관리자 - 진행 중인 작업이 없는지 확인](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step1.png)

## <a name="step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema"></a>2단계: 온-프레미스 ADDS 커넥터 스키마에 원본 특성 추가
모든 AD 특성을 온-프레미스 AD 커넥터 공간으로 가져오지는 않습니다. 기본적으로 동기화되지 않은 특성을 사용하도록 선택한 경우 해당 특성을 가져와야 합니다. 가져온 특성 목록에 원본 특성을 추가하려면 다음을 수행합니다.

1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
2. **온-프레미스 AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
3. 팝업 대화 상자에서 **특성 선택** 탭으로 이동합니다.
4. 특성 목록에서 사용하도록 선택한 원본 특성이 선택되어 있는지 확인합니다. 특성이 표시되지 않으면 "모두 표시" 확인란을 클릭합니다.
5. **확인**을 클릭하여 저장합니다.

![온-프레미스 AD 커넥터 스키마에 원본 특성 추가](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step2.png)

## <a name="step-3-add-preferreddatalocation-to-the-azure-ad-connector-schema"></a>3단계: Azure AD 커넥터 스키마에 PreferredDataLocation 추가
기본적으로 PreferredDataLocation 특성은 Azure AD 커넥터 공간으로 가져오지 않습니다. 가져온 특성 목록에 PreferredDataLocation 특성을 추가하려면 다음을 수행합니다.

1. Synchronization Service Manager에 있는 **커넥터** 탭으로 이동합니다.
2. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택합니다.
3. 팝업 대화 상자에서 **특성 선택** 탭으로 이동합니다.
4. 특성 목록에서 PreferredDataLocation 특성을 선택합니다.
5. **확인**을 클릭하여 저장합니다.

![Azure AD 커넥터 스키마에 원본 특성 추가](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step3.png)

## <a name="step-4-create-an-inbound-synchronization-rule-to-flow-the-attribute-value-from-on-premises-active-directory"></a>4단계: 온-프레미스 Active Directory에서 특성 값을 전달하는 인바운드 동기화 규칙 만들기
인바운드 동기화 규칙은 온-프레미스 Active Directory의 원본 특성에서 메타버스로 특성 값을 전달하도록 허용합니다.

1. **시작** > **동기화 규칙 편집기**로 이동하여 **동기화 규칙 편집기**를 시작합니다.
2. **방향** 검색 필터를 **인바운드**로 설정합니다.
3. **새 규칙 추가** 단추를 클릭하여 새 인바운드 규칙을 만듭니다.
4. **설명 탭** 아래에서 다음 구성을 제공합니다.

    | 특성 | 값 | 세부 정보 |
    | --- | --- | --- |
    | Name | *이름 제공* | 예: *"AD에서 인바운드 - 사용자 PreferredDataLocation"* |
    | 설명 | *사용자 지정 설명 제공* |  |
    | 연결된 시스템 | *온-프레미스 AD 커넥터 선택* |  |
    | 연결된 시스템 개체 유형 | **User** |  |
    | 메타버스 개체 유형 | **Person** |  |
    | 링크 형식 | **Join** |  |
    | 우선 순위 | *1-99 사이의 숫자 선택* | 1-99는 사용자 지정 동기화 규칙을 위해 예약되어 있습니다. 다른 동기화 규칙에서 사용하는 값은 선택하지 않습니다. |

5. 모든 개체를 포함하려면 **범위 지정 필터**를 비워 둡니다. Azure AD Connect 배포에 따라 범위 지정 필터를 조정해야 할 수도 있습니다.
6. **변환 탭**으로 이동하여 다음 변환 규칙을 구현합니다.

    | 흐름 형식 | 대상 특성 | 원본 | 한 번 적용 | 병합 종류 |
    | --- | --- | --- | --- | --- |
    |직접 | PreferredDataLocation | 원본 특성 선택 | 선택 취소됨 | 주 지역에서 |

7. **추가**를 클릭하여 인바운드 규칙을 만듭니다.

![인바운드 동기화 규칙 만들기](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step4.png)

## <a name="step-5-create-an-outbound-synchronization-rule-to-flow-the-attribute-value-to-azure-ad"></a>5단계: Azure AD로 특성 값을 전달하는 아웃바운드 동기화 규칙 만들기
아웃바운드 동기화 규칙은 메타버스에서 Azure AD의 PreferredDataLocation 특성으로 특성 값을 전달하도록 허용합니다.

1. **동기화 규칙** 편집기로 이동합니다.
2. **방향** 검색 필터를 **아웃바운드**로 설정합니다.
3. **새 규칙 추가** 단추를 클릭합니다.
4. **설명 탭** 아래에서 다음 구성을 제공합니다.

    | 특성 | 값 | 세부 정보 |
    | ----- | ------ | --- |
    | Name | *이름 제공* | 예: "AAD로 아웃바운드 - 사용자 PreferredDataLocation" |
    | 설명 | *설명 제공* ||
    | 연결된 시스템 | *AAD 커넥터 선택* ||
    | 연결된 시스템 개체 유형 | 사용자 ||
    | 메타버스 개체 유형 | **Person** ||
    | 링크 형식 | **Join** ||
    | 우선 순위 | *1-99 사이의 숫자 선택* | 1-99는 사용자 지정 동기화 규칙을 위해 예약되어 있습니다. 다른 동기화 규칙에서 사용하는 값은 선택하지 않습니다. |

5. **범위 지정 필터** 탭으로 이동하여 **다음 두 절이 있는 단일 범위 지정 필터 그룹**을 추가합니다.

    | 특성 | 연산자 | 값 |
    | --- | --- | --- |
    | sourceObjectType | EQUAL | 사용자 |
    | cloudMastered | NOTEQUAL | True |

    범위 지정 필터는 이 아웃바운드 동기화 규칙이 적용되는 Azure AD 개체를 결정합니다. 이 예제에서는 "AD로 아웃바운드 - 사용자 ID" OOB 동기화 규칙과 동일한 범위 지정 필터를 사용합니다. 온-프레미스 Active Directory와 동기화되지 않은 User 개체에 동기화 규칙이 적용되지 않도록 합니다. Azure AD Connect 배포에 따라 범위 지정 필터를 조정해야 할 수도 있습니다.

6. **변환 탭**으로 이동하여 다음 변환 규칙을 구현합니다.

    | 흐름 형식 | 대상 특성 | 원본 | 한 번 적용 | 병합 종류 |
    | --- | --- | --- | --- | --- |
    | 직접 | PreferredDataLocation | PreferredDataLocation | 선택 취소됨 | 주 지역에서 |

7. **추가**를 클릭하여 아웃바운드 규칙을 만듭니다.

![아웃바운드 동기화 규칙 만들기](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-step5.png)

## <a name="step-6-run-full-synchronization-cycle"></a>6단계: 전체 동기화 주기 실행
일반적으로 AD 및 Azure AD 커넥터 스키마 모두에 새 특성을 추가하고 사용자 지정 동기화 규칙을 도입했으므로 전체 동기화 주기가 필요합니다. Azure AD로 내보내기 전에 변경 내용을 확인하는 것이 좋습니다. 다음 단계를 사용하여 전체 동기화 주기를 구성하는 단계를 수동으로 실행하는 동안 변경 내용을 확인할 수 있습니다.

1. **온-프레미스 AD 커넥터**에서 다음과 같이 **전체 가져오기** 단계를 실행합니다

   1. Synchronization Service Manager에 있는 **작업** 탭으로 이동합니다.
   2. **온-프레미스 AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.
   3. 팝업 대화 상자에서 **전체 가져오기**를 선택하고 **확인**을 클릭합니다.
   4. 작업이 완료될 때까지 기다립니다.

    > [!NOTE]
    > 원본 특성이 가져온 특성 목록에 이미 포함되어 있으면 온-프레미스 AD 커넥터에서 전체 가져오기를 건너뛸 수 있습니다. 즉 [2단계: 온-프레미스 AD 커넥터 스키마에 원본 특성 추가](#step-2-add-the-source-attribute-to-the-on-premises-adds-connector-schema)에서 변경하지 않아도 됩니다.

2. **Azure AD 커넥터**에서 다음과 같이 **전체 가져오기** 단계를 실행합니다

   1. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.
   2. 팝업 대화 상자에서 **전체 가져오기**를 선택하고 **확인**을 클릭합니다.
   3. 작업이 완료될 때까지 기다립니다.

3. 기존 User 개체에 대한 동기화 규칙 변경 내용을 확인합니다.

온-프레미스 Active Directory의 원본 특성과 Azure AD의 PreferredDataLocation을 각각의 커넥터 공간으로 가져왔습니다. 전체 동기화 단계를 진행하기 전에 온-프레미스 AD 커넥터 공간에 있는 기존 User 개체에 대해 **미리 보기**를 수행하는 것이 좋습니다. 선택한 개체에 원본 특성이 채워져야 합니다. PreferredDataLocation이 메타버스에 채워진 성공적인 **미리 보기**는 동기화 규칙을 올바르게 구성했음을 나타내는 좋은 지표입니다. **미리 보기**를 수행하는 방법에 대한 자세한 내용은 [변경 확인](active-directory-aadconnectsync-change-the-configuration.md#verify-the-change) 섹션을 참조하세요.

4. **온-프레미스 AD 커넥터**에서 다음과 같이 **전체 동기화** 단계를 실행합니다.

   1. **온-프레미스 AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.
   2. 팝업 대화 상자에서 **전체 동기화**를 선택하고 **확인**을 클릭합니다.
   3. 작업이 완료될 때까지 기다립니다.

5. Azure AD로의 **보류 중인 내보내기**를 확인합니다.

   1. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **커넥터 공간 검색**을 선택합니다.
   2. [커넥터 공간 검색] 팝업 대화 상자에서

      1. **범위**를 **보류 중인 내보내기**로 설정합니다.
      2. **추가, 수정 및 삭제**를 포함한 세 개의 확인란을 모두 선택합니다.
      3. **검색** 단추를 클릭하여 내보낼 변경 내용이 있는 개체의 목록을 가져옵니다. 지정된 개체에 대한 변경 내용을 검사하려면 해당 개체를 두 번 클릭합니다.
      4. 필요한 변경 내용인지 확인합니다.

6. **Azure AD 커넥터**에서 **내보내기** 단계를 실행합니다.

   1. **Azure AD 커넥터**를 마우스 오른쪽 단추로 클릭하고 **실행...**을 선택합니다.
   2. [커넥터 실행] 팝업 대화 상자에서 **내보내기**를 선택하고 **확인**을 클릭합니다.
   3. Azure AD로 내보내기가 완료될 때까지 기다립니다.

> [!NOTE]
> Azure AD 커넥터에 전체 동기화 단계 및 내보내기 단계가 포함되지 않은 단계가 나타날 수 있습니다. 특성 값이 온-프레미스 Active Directory에서 Azure AD로만 전달되기 때문에 이러한 단계가 필요하지 않습니다.

## <a name="step-7-re-enable-sync-scheduler"></a>7단계: 동기화 스케줄러를 다시 사용하도록 설정
기본 제공 동기화 스케줄러를 다시 사용하도록 설정하려면

1. PowerShell 세션을 시작합니다.
2. cmdlet을 실행하여 예약된 동기화 다시 활성화: `Set-ADSyncScheduler -SyncCycleEnabled $true`

## <a name="step-8-verify-the-result"></a>8단계: 결과 확인
구성을 확인하고 사용자를 위해 적용해야 합니다.

1. 사용자의 선택한 특성에 해당 지역을 추가합니다. 사용 가능한 지역 목록은 [이 표](#enable-synchronization-of-preferreddatalocation)에서 확인할 수 있습니다.  
![사용자에게 추가된 AD 특성](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-adattribute.png)
2. 특성을 Azure AD와 동기화될 때까지 기다립니다.
3. Exchange Online PowerShell을 사용하여 사서함 지역이 올바르게 설정되어 있는지 확인합니다.  
![Exchange Online에서 사용자에 대해 설정된 사서함 지역](./media/active-directory-aadconnectsync-feature-preferreddatalocation/preferreddatalocation-mailboxregion.png)  
테넌트가 이 기능을 사용할 수 있는 것으로 표시될 경우, 사서함은 올바른 지역으로 이동됩니다. 올바른 지역으로 이동되는지는 사서함이 있는 서버 이름을 확인하여 알 수 있습니다.
4. 이 설정이 많은 사서함에 적용되었는지 확인하려면 [Technet 갤러리](https://gallery.technet.microsoft.com/office/PowerShell-Script-to-a6bbfc2e)의 스크립트를 사용합니다. 또한 이 스크립트에는 모든 Office 365 데이터 센터 서버 접두사와 사서함이 있는 지역 목록도 포함됩니다. 이전 단계를 참조해서 사서함의 위치를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

**Office 365의 다중 지역에 대한 자세한 정보:**

* Ignite의 다중 지역 세션: https://aka.ms/MultiGeoIgnite
* OneDrive의 다중 지역: https://aka.ms/OneDriveMultiGeo
* SharePoint Online의 다중 지역: https://aka.ms/SharePointMultiGeo

**동기화 엔진의 구성 모델에 대한 자세한 정보:**

* [선언적 프로비전 이해](active-directory-aadconnectsync-understanding-declarative-provisioning.md)에서 구성 모델에 대해 자세히 알아봅니다.
* [선언적 프로비전 식 이해](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)에서 식 언어에 대해 자세히 알아봅니다.

**개요 항목**

* [Azure AD Connect 동기화: 동기화의 이해 및 사용자 지정](active-directory-aadconnectsync-whatis.md)
* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)
