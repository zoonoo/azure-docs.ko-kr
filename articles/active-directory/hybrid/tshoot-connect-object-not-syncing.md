---
title: Azure Active Directory와 동기화되지 않는 개체 문제 해결 | Microsoft Docs
description: Azure Active Directory와 동기화되지 않는 개체 문제 해결
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 931865803328189d89c0fbae15caa801c3f7f7c6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60455141"
---
# <a name="troubleshoot-an-object-that-is-not-synchronizing-with-azure-active-directory"></a>Azure Active Directory와 동기화되지 않는 개체 문제 해결

개체가 예상대로 Microsoft Azure AD(Azure Active Directory)와 동기화되지 않는 경우 몇 가지 이유가 있을 수 있습니다. Azure AD에서 오류 전자 메일을 받았거나 Azure AD Connect Health에 오류 메시지가 표시될 경우 [동기화 중 오류 문제 해결](tshoot-connect-sync-errors.md)을 대신 읽어보세요. 하지만 개체가 Azure AD에 없는 문제가 발생하는 경우 이 문서를 참조하세요. 여기서는 온-프레미스 구성 요소 Azure AD Connect 동기화에서 오류를 찾는 방법을 설명합니다.

>[!IMPORTANT]
>버전 1.1.749.0 이상인 Azure AD Connect 배포의 경우 마법사에서 [문제 해결 작업](tshoot-connect-objectsync.md)을 사용하여 개체 동기화 문제를 해결합니다. 

## <a name="synchronization-process"></a>동기화 프로세스

특성 동기화 문제를 조사하기 전에, Azure AD Connect 동기화 프로세스부터 살펴보겠습니다.

  ![Azure AD Connect 동기화 프로세스 다이어그램](./media/tshoot-connect-object-not-syncing/syncingprocess.png)

### <a name="terminology"></a>**용어**

* **CS:** 커넥터 공간(데이터베이스의 테이블)
* **MV:** 메타버스(데이터베이스의 테이블)

### <a name="synchronization-steps"></a>**동기화 단계**
동기화 프로세스에서 다음 단계가 진행됩니다.

1. **AD에서 가져오기:** : Active Directory 개체를 Active Directory CS로 가져옵니다.

2. **Azure AD에서 가져오기:** Azure AD 개체를 Azure AD CS로 가져옵니다.

3. **동기화:** 인바운드 동기화 규칙 및 아웃바운드 동기화 규칙은 우선 순위 번호가 낮은 규칙부터 차례로 실행됩니다. 동기화 규칙을 보려면 데스크톱 애플리케이션에서 동기화 규칙 편집기로 이동합니다. 인바운드 동기화 규칙은 CS에서 MV로 데이터를 가져옵니다. 아웃바운드 동기화 규칙은 MV에서 CS로 데이터를 가져옵니다.

4. **AD로 내보내기:** 동기화 후 Active Directory CS에서 Active Directory로 개체를 내보냅니다.

5. **Azure AD로 내보내기:** 동기화 후 개체를 Azure AD CS에서 Azure AD로 내보냅니다.

## <a name="troubleshooting"></a>문제 해결

오류를 찾기 위해 다음 순서대로 몇 가지 위치를 확인해보겠습니다.

1. [작업 로그](#operations): 가져오기 및 동기화 동안 동기화 엔진에서 식별된 오류를 찾습니다.
2. [커넥터 공간](#connector-space-object-properties): 누락된 개체 및 동기화 오류를 찾습니다.
3. [메타버스](#metaverse-object-properties): 데이터 관련 문제를 찾습니다.

이러한 단계를 시작하기 전에 [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md)를 시작합니다.

## <a name="operations"></a>작업
Synchronization Service Manager의 **작업** 탭에서 문제 해결을 시작합니다. 이 탭에서는 최근 작업의 결과를 보여 줍니다. 

![작업 탭이 선택된 Synchronization Service Manager 스크린샷](./media/tshoot-connect-object-not-syncing/operations.png)  

**작업** 탭의 위쪽 절반에 모든 실행이 시간순으로 표시됩니다. 기본적으로 작업 로그는 지난 7일에 대한 정보를 유지하지만 이 설정은 [스케줄러](how-to-connect-sync-feature-scheduler.md)에 따라 변동될 수 있습니다. **성공** 상태를 표시하지 않는 실행을 찾아봅니다. 헤더를 클릭하여 정렬을 변경할 수 있습니다.

**상태** 열은 가장 중요한 정보를 포함하며 실행에 대해 가장 심각한 문제를 표시합니다. 다음은 조사 우선 순위에 따른 가장 일반적인 상태에 대한 간단한 요약입니다(여기서 *는 여러 가능한 오류 문자열을 나타냄).

| 상태 | 주석 |
| --- | --- |
| stopped- * |실행을 완료할 수 없습니다. 예를 들어 원격 시스템이 다운되어 연결할 수 없는 경우 이 상태가 나타날 수 있습니다. |
| stopped-error-limit |5000개보다 많은 오류가 있습니다. 많은 오류로 인해 실행이 자동으로 중지되었습니다. |
| completed-\*-errors |실행이 완료되었지만 조사해야 할 오류가 있습니다(5,000개 미만). |
| completed-\*-warnings |실행이 완료되었지만 일부 데이터가 예상된 상태가 아닙니다. 오류가 발생하는 경우 이 메시지는 일반적으로 증상일 뿐입니다. 오류를 해결할 때까지 경고를 조사하지 않습니다. |
| 성공 |문제가 없습니다. |

행을 선택하면 해당 실행의 세부 정보가 표시되도록 **작업** 탭 아래쪽이 업데이트됩니다. 이 영역의 왼쪽 끝에는 **#단계** 목록이 표시될 수 있습니다. 이 목록은 포리스트에 여러 도메인이 있고 각 도메인이 단계로 표시되는 경우에만 표시됩니다. 도메인 이름은 **파티션**머리글 아래에서 찾을 수 있습니다. **동기화 통계** 제목 아래에서 처리된 변경 내용의 수에 대한 자세한 정보를 찾을 수 있습니다. 링크를 선택하여 변경된 개체의 목록을 가져옵니다. 오류가 있는 개체가 있으면 해당 오류는 **동기화 오류** 제목 아래에 표시됩니다.

### <a name="errors-on-the-operations-tab"></a>작업 탭의 오류
오류가 발생하는 경우 Synchronization Service Manager는 오류 개체 및 오류 자체가 모두 자세한 정보를 제공하는 링크를 표시합니다.

![Synchronization Service Manager의 오류 스크린샷](./media/tshoot-connect-object-not-syncing/errorsync.png)  
먼저 오류 문자열을 선택합니다. (위의 그림에서 오류 문자열은 **sync-rule-error-function-triggered**입니다.) 먼저 개체의 개요가 나타납니다. 실제 오류를 보려면 **스택 추적**을 선택합니다. 이 추적은 오류에 대한 디버그 수준 정보를 제공합니다.

**호출 스택 정보** 상자를 마우스 오른쪽 단추로 클릭하고 **모두 선택**을 클릭한 후 **복사**를 선택합니다. 그런 다음 스택을 복사하고 메모장 등 즐겨 사용하는 편집기에서 오류를 확인합니다.

오류가 **SyncRulesEngine**에서 발생한 경우 호출 스택 정보가 개체에 대한 모든 특성의 목록을 먼저 제공합니다. **InnerException =>** 머리글이 표시될 때까지 아래로 스크롤합니다.  

  ![InnerException 제목 아래에 오류 정보를 표시하는 Synchronization Service Manager 스크린샷 =>](./media/tshoot-connect-object-not-syncing/errorinnerexception.png)
  
제목 다음 줄에 오류가 표시됩니다. 위 그림에서 오류는 Fabrikam이 만든 사용자 지정 동기화 규칙에서 발생한 것입니다.

오류가 충분한 정보를 제공하지 않는 경우 이제 데이터 자체에 대해 살펴봅니다. 개체 식별자가 있는 링크를 선택하고 [커넥터 공간 가져온 개체](#cs-import) 문제를 계속 해결합니다.

## <a name="connector-space-object-properties"></a>커넥터 공간 개체 속성
[**작업**](#operations) 탭에 오류가 표시되지 않으면 Active Directory에서 메타버스, Azure AD를 따라 이동하면서 커넥터 공간 개체를 확인합니다. 이 경로에서 문제가 발생한 위치를 찾아야 합니다.

### <a name="searching-for-an-object-in-the-cs"></a>CS에서 개체 검색

Synchronization Service Manager에서 **커넥터**를 선택하고 Active Directory Connector를 선택한 후 **커넥터 공간 검색**을 선택합니다.

**범위** 상자에서 **RDN**(CN 특성에 대해 검색하려는 경우) 또는 **DN 또는 앵커**(**distinguishedName** 특성에 대해 검색하려는 경우)를 선택합니다. 값을 입력하고 **검색**을 선택합니다. 
 
![커넥터 공간 검색 스크린샷](./media/tshoot-connect-object-not-syncing/cssearch.png)  

찾으려는 개체를 찾지 못할 경우 [도메인 기반 필터링](how-to-connect-sync-configure-filtering.md#domain-based-filtering) 또는 [OU 기반 필터링](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)으로 필터링되었을 수 있습니다. 필터링이 예상대로 구성되었는지 확인하려면 [Azure AD Connect 동기화: 필터링 구성](how-to-connect-sync-configure-filtering.md)을 읽어보세요.

Azure AD Connect를 선택하여 또 다른 유용한 검색을 수행할 수 있습니다. **범위** 상자에서 **보류 중인 가져오기**를 선택한 후 **추가** 확인란을 선택합니다. 이 검색은 온-프레미스 개체와 연결될 수 없는 Azure AD의 모든 동기화된 개체를 제공합니다.  

![커넥터 공간 검색의 고아 개체 스크린샷](./media/tshoot-connect-object-not-syncing/cssearchorphan.png) 
 
이러한 개체는 다른 동기화 엔진 또는 다른 필터링 구성을 갖는 동기화 엔진에서 만들어졌습니다. 이러한 고아 개체는 더 이상 관리되지 않습니다. 이 목록을 검토하고 [Azure AD PowerShell](https://aka.ms/aadposh) cmdlet을 사용하여 이러한 개체를 제거하는 것이 좋습니다.

### <a name="cs-import"></a>CS 가져오기
CS 개체를 열면 위쪽에 여러 개의 탭이 표시됩니다. **가져오기** 탭은 가져온 후에 준비된 데이터를 표시합니다.  

![가져오기 탭이 선택된 커넥터 공간 개체 속성 창 스크린샷](./media/tshoot-connect-object-not-syncing/csobject.png)    

**이전 값** 열은 커넥터에 현재 저장된 값을 표시하고 **새 값** 열은 원본 시스템에서 받았지만 아직 적용되지 않은 값을 표시합니다. 개체에 오류가 발생하는 경우 변경 내용이 처리되지 않습니다.

개체에 문제가 있을 때만 **동기화 오류** 탭이 **커넥터 공간 개체 속성** 창에 표시됩니다. 자세한 내용은 [**작업** 탭에서 동기화 오류를 해결하는](#errors-on-the-operations-tab) 방법을 검토하세요.

![커넥터 공간 개체 속성의 동기화 오류 탭 스크린샷](./media/tshoot-connect-object-not-syncing/cssyncerror.png)  

### <a name="cs-lineage"></a>CS 계보
**커넥터 공간 개체 속성** 창의 **계보** 탭은 커넥터 공간 개체가 메타버스 개체와 관련된 방식을 보여 줍니다. 커넥터가 연결된 시스템에서 마지막으로 변경 내용을 가져온 시점 및 메타버스에 데이터를 채우는 데 적용된 규칙을 볼 수 있습니다.  

![커넥터 공간 개체 속성 창의 계보 탭을 보여 주는 스크린샷](./media/tshoot-connect-object-not-syncing/cslineage.png)  

위의 그림에서 **작업** 열에는 **프로비전** 작업의 인바운드 동기화 규칙이 표시됩니다. 이는 이 커넥터 공간 개체가 있는 한 메타버스 개체가 그대로 유지됨을 나타냅니다. 대신, 동기화 규칙의 목록에 **프로비전** 작업의 아웃바운드 동기화 규칙이 표시되면 메타버스 개체가 삭제될 때 이 개체도 삭제됩니다.  

![커넥터 공간 개체 속성 창의 계보 탭, 계보 창 스크린샷](./media/tshoot-connect-object-not-syncing/cslineageout.png)  

이전 그림에서 **PasswordSync** 열에서 **True** 값이 있는 동기화 규칙으로 인바운드 커넥터 공간이 암호 변경에 영향을 줄 수 있다는 것을 볼 수 있습니다. 이 암호는 아웃바운드 규칙을 통해 Azure AD로 전송됩니다.

**계보** 탭에서 [**메타버스 개체 속성**](#mv-attributes)을 선택하여 메타버스로 이동할 수 있습니다.

### <a name="preview"></a>미리 보기
**커넥터 공간 개체 속성** 창의 왼쪽 아래 모서리에는 **미리 보기** 단추가 있습니다. 이 단추를 선택하여 단일 개체를 동기화할 수 있는 **미리 보기** 페이지를 엽니다. 이 페이지는 일부 사용자 동기화 규칙 문제를 해결하고 단일 개체에서 변경 내용의 영향을 보려는 경우에 유용합니다. **전체 동기화** 또는 **델타 동기화** 중에서 선택할 수 있습니다. 메모리에만 변경 내용을 유지하는 **미리 보기 생성**을 선택할 수도 있습니다. 또는 메타버스를 업데이트하고 대상 커넥터 공간에 모든 변경 내용을 단계적으로 적용하는 **커밋 미리 보기**를 선택합니다.  

![미리 보기 시작이 선택된 미리 보기 페이지 스크린샷](./media/tshoot-connect-object-not-syncing/preview.png)  

미리 보기에서 개체 및 특정 특성 흐름에 적용되는 규칙을 검사할 수 있습니다.  

![특성 흐름 가져오기를 보여 주는 미리 보기 페이지의 스크린샷](./media/tshoot-connect-object-not-syncing/previewresult.png)

### <a name="log"></a>로그
**미리 보기** 단추 옆에 있는 **로그** 단추를 선택하여 **로그** 페이지를 엽니다. 여기에서 암호 동기화 상태와 기록을 볼 수 있습니다. 자세한 내용은 [Azure AD Connect 동기화를 사용하여 암호 해시 동기화 문제 해결](tshoot-connect-password-hash-synchronization.md)을 참조하세요.

## <a name="metaverse-object-properties"></a>메타버스 개체 속성
일반적으로는 원본 Active Directory 커넥터 공간에서 검색을 시작하는 것이 좋습니다. 하지만 메타버스에서 검색을 시작할 수도 있습니다.

### <a name="searching-for-an-object-in-the-mv"></a>MV에서 개체 검색
다음 그림과 같이 Synchronization Service Manager에서 **메타버스 검색**을 선택합니다. 사용자를 찾는 쿼리를 만듭니다. **accountName**(**sAMAccountName**) 및 **userPrincipalName** 등의 일반적인 특성을 검색합니다. 자세한 내용은 [Sync Service Manager 메타버스 검색](how-to-connect-sync-service-manager-ui-mvsearch.md)을 참조하세요.

![메타버스 검색 탭이 선택된 Synchronization Service Manager 스크린샷](./media/tshoot-connect-object-not-syncing/mvsearch.png)  

**검색 결과** 창에서 개체를 클릭합니다.

개체를 찾지 못한 경우 메타버스에 아직 도달하지 않은 것입니다. Active Directory [커넥터 공간](#connector-space-object-properties)에서 개체를 계속 검색합니다. Active Directory 커넥터 공간에 개체가 있는 경우 동기화 오류가 발생하여 개체를 메타버스로 가져올 수 없는 상태이거나, 동기화 규칙 범위 지정 필터가 적용된 것일 수 있습니다.

### <a name="object-not-found-in-the-mv"></a>MV에 개체가 없음
개체가 Active Directory CS에는 있는데 MV에는 없으면 범위 지정 필터가 적용된 것입니다. 범위 지정 필터를 확인하려면 데스크톱 애플리케이션 메뉴로 이동하여 **동기화 규칙 편집기**를 선택합니다. 아래 필터를 조정하여 개체에 해당하는 규칙을 필터링합니다.

  ![인바운드 동기화 규칙 검색을 표시하는 동기화 규칙 편집기 스크린샷](./media/tshoot-connect-object-not-syncing/syncrulessearch.png)

위의 목록에서 각 규칙을 확인하고 **범위 지정 필터**를 선택합니다. 다음 범위 지정 필터에서 **isCriticalSystemObject** 값이 null 또는 FALSE이거나 비어 있으면 해당 값은 범위 내에 포함되는 것입니다.

  ![인바운드 동기화 규칙 검색의 범위 지정 필터 스크린샷](./media/tshoot-connect-object-not-syncing/scopingfilter.png)

[CS 가져오기](#cs-import) 특성 목록으로 이동하여 MV로 개체를 이동할 수 없도록 차단하는 필터를 선택합니다. **커넥터 공간** 특성 목록에 null이 아니며 비어 있지 않은 특성만 표시됩니다. 예를 들어 **isCriticalSystemObject**가 목록에 나타나지 않으면 이 특성의 값이 null이거나 비어 있는 것입니다.

### <a name="object-not-found-in-the-azure-ad-cs"></a>Azure AD CS에 개체가 없음
개체가 Azure AD의 커넥터 공간에는 없고 MV에는 있는 경우 해당 커넥터 공간의 아웃바운드 규칙 범위 지정 필터를 확인하여 [MV 특성](#mv-attributes)이 조건을 충족하지 않아 개체가 필터링되었는지 여부를 확인합니다.

아웃바운드 범위 지정 필터를 확인하려면 아래의 필터를 조정하여 개체에 해당하는 규칙을 선택합니다. 각 규칙과 해당 [MV 특성](#mv-attributes) 값을 확인합니다.

  ![동기화 규칙 편집기의 아웃바운드 동기화 규칙 검색 스크린샷](./media/tshoot-connect-object-not-syncing/outboundfilter.png)


### <a name="mv-attributes"></a>MV 특성
**특성** 탭에서는 특성 값과 기여한 커넥터를 볼 수 있습니다.  

![특성 탭이 선택된 메타버스 개체 속성 창 스크린샷](./media/tshoot-connect-object-not-syncing/mvobject.png)  

개체가 동기화되지 않는 경우 메타버스의 특성 상태에 대해 다음과 같은 질문을 합니다.
- 특성 **cloudFiltered**가 있고 **True**로 설정되어 있나요? 그렇다면 [특성 기반 필터링](how-to-connect-sync-configure-filtering.md#attribute-based-filtering)의 단계에 따라 필터링된 것입니다.
- 특성 **sourceAnchor**가 있나요? 없으면 계정-리소스 포리스트 토폴로지가 있나요? 개체가 연결된 사서함으로 식별되는 경우(특성 **msExchRecipientTypeDetails**의 값이 **2**임) **sourceAnchor**는 Active Directory 계정이 사용하도록 설정된 포리스트에서 제공한 것입니다. 마스터 계정을 제대로 가져와 동기화했는지 확인합니다. 마스터 계정은 개체에 대한 [커넥터](#mv-connectors)에 나열되어야 합니다.

### <a name="mv-connectors"></a>MV 커넥터
**커넥터** 탭은 개체의 표현이 있는 모든 커넥터 공간을 표시합니다. 
 
![커넥터 탭이 선택된 메타버스 개체 속성 창 스크린샷](./media/tshoot-connect-object-not-syncing/mvconnectors.png)  

다음에 대한 커넥터가 있어야 합니다.

- 사용자가 표시되는 각 Active Directory 포리스트. 이 표시에는 **foreignSecurityPrincipals** 및 **Contact** 개체가 포함될 수 있습니다.
- Azure AD의 커넥터.

Azure AD에 대한 커넥터가 없으면 [MV 특성](#mv-attributes)에 대한 섹션을 읽어 Azure AD로 프로비전하기 위한 기준을 확인합니다.

**커넥터** 탭에서 [커넥터 공간 개체](#connector-space-object-properties)로 이동할 수도 있습니다. 행을 선택하고 **속성**을 클릭합니다.

## <a name="next-steps"></a>다음 단계
- [Azure AD Connect 동기화](how-to-connect-sync-whatis.md)에 대해 자세히 알아봅니다.
- [하이브리드 ID](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
