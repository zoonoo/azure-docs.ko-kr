---
title: "커넥터 버전 릴리스 내역 | Microsoft Docs"
description: "이 항목에서는 FIM(Forefront Identity Manager) 및 MIM(Microsoft Identity Manager)에 대한 커넥터의 모든 버전을 보여 줍니다."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: 6a0c66ab-55df-4669-a0c7-1fe1a091a7f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 244ca634cfd47ee37e3845380ac05dc68d406621
ms.lasthandoff: 03/29/2017


---
# <a name="connector-version-release-history"></a>커넥터 버전 릴리스 내역
FIM(Forefront Identity Manager) 및 MIM(Microsoft Identity Manager)의 커넥터는 자주 업데이트됩니다.

> [!NOTE]
> 이 항목은 FIM 및 MIM에만 있습니다. 이러한 커넥터는 Azure AD Connect에서 지원되지 않습니다.

이 항목은 출시된 커넥터의 모든 버전을 나열합니다.

관련 링크:

* [최신 커넥터 다운로드](http://go.microsoft.com/fwlink/?LinkId=717495)
* [일반 LDAP 커넥터](active-directory-aadconnectsync-connector-genericldap.md) 참조 설명서
* [일반 SQL 커넥터](active-directory-aadconnectsync-connector-genericsql.md) 참조 설명서
* [웹 서비스 커넥터](http://go.microsoft.com/fwlink/?LinkID=226245) 참조 설명서
* [PowerShell 커넥터](active-directory-aadconnectsync-connector-powershell.md) 참조 설명서
* [Lotus Domino 커넥터](active-directory-aadconnectsync-connector-domino.md) 참조 설명서

## <a name="114430"></a>1.1.443.0

출시 날짜: 2017년 3월

### <a name="enhancements"></a>향상된 기능
* 일반 SQL:</br>
  **시나리오 증상:** 하나의 개체 형식에 대한 참조만 허용하고 멤버와 상호 참조하는 SQL 커넥터의 잘 알려진 제한입니다. </br>
  **솔루션 설명:** "*" 옵션을 선택한 참조를 위한 처리 단계에서 모든 개체 형식 조합은 동기화 엔진으로 다시 반환됩니다.

>[!Important]
- 그러면 자리 표시자가 많이 생성됩니다.
- 이름이 고유한 상호 개체 형식인지 확인해야 합니다.


* 일반 LDAP:</br>
 **시나리오:** 몇 가지 컨테이너만 특정 파티션에서 선택된 경우 검색은 여전히 전체 파티션에서 수행됩니다. 특정 파티션은 성능 저하로 이어질 수 있는 MA가 아닌 Synchronization Service에서 필터링됩니다. </br>

 **해결 방법 설명:** 모든 컨테이너에 걸쳐 수행하고 전체 파티션에서 검색하는 대신 각 파티션에서 개체를 검색하기 위해 변경된 GLDAP 커넥터의 코드입니다.


* Lotus Domino:

  **시나리오:** 내보내기를 수행하는 동안 사용자 제거를 위한 Domino 메일 삭제 지원입니다. </br>
  **해결 방법:** 내보내기를 수행하는 동안 사용자 제거를 위한 구성 가능한 메일 삭제 지원입니다.

### <a name="fixed-issues"></a>수정된 문제:
* 일반 웹 서비스:
 * WebService 구성 도구를 통해 기본 SAP wsconfig 프로젝트에서 서비스 URL을 변경한 후 다음과 같은 오류가 발생한 경우: 경로의 일부를 찾을 수 없습니다.

      ``'C:\Users\cstpopovaz\AppData\Local\Temp\2\e2c9d9b0-0d8a-4409-b059-dceeb900a2b3\b9bedcc0-88ac-454c-8c69-7d6ea1c41d17\cfg.config\cloneconfig.xml'. ``

* 일반 LDAP:
 * GLDAP 커넥터에서 AD LDS의 모든 특성을 확인할 수 없음
 * LDAP 디렉터리 스키마에서 UPN 특성이 감지되지 않는 경우 마법사 중단
 * "objectclass" 특성이 선택되지 않은 경우 전체 가져오기를 수행하는 동안 검색 오류가 발생하지 않으면 델타 가져오기 실패
 * A "Configure Partitions and Hierarchies” configuration page, doesn’t show any objects which type is equal to the partition for Novel servers in the Generic  
"파티션 및 계층 구조 구성" 구성 페이지에 일반 LDAP MA의 Novel 서버에 대한 파티션과 형식이 비슷한 개체가 표시되지 않습니다. RootDSE 파티션의 개체만 표시됩니다.


* 일반 SQL:
 * 일반 SQL 워터마크 델타 가져오기 다중값 특성이 버그를 가져오지 않는 문제 해결
 * 다중값 특성의 삭제/추가된 값을 내보낼 때 데이터 원본에서 삭제/추가되지 않음.  


* Lotus Notes:
 * 특성에 대한 값이 Null이거나 비어있다는 알림을 내보낼 때 특정 필드 "전체 이름"이 메타버스에 올바르게 표시됩니다.
 * 중복 인증자 오류 해결
 * 데이터가 없는 개체가 다른 개체를 통해 Lotus Domino 커넥터에서 선택된 경우 전체 가져오기를 수행하는 동안 검색 오류를 수신합니다.
 * 델타 가져오기가 Lotus Domino 커넥터에서 실행 중인 경우 실행 마지막에 Microsoft.IdentityManagement.MA.LotusDomino.Service.exe 서비스가 때때로 응용 프로그램 오류를 반환합니다.
 * 멤버 자격에서 사용자를 제거하려고 시도하기 위해 내보내기를 실행할 때 성공적으로 업데이트되었음이 표시되지만 실제로 사용자가 Lotus Notes의 멤버 자격에서 삭제되지는 않는 경우를 제외하면 전체 그룹 멤버는 정상적으로 작동하며 유지됩니다.
 * 다중값 특성을 내보내는 동안 새 항목을 아래에 추가할 수 있도록 "아래에 항목 추가"로 내보내기 모드를 선택할 수 있는 기능이 Lotus MA의 GUI 구성에 추가되었습니다.
 * 커넥터는 메일 폴더 및 ID 자격 증명 모음에서 파일을 삭제하는 데 필요한 논리를 추가합니다.
 * 멤버 자격 삭제는 NAB 멤버 간에 작동하지 않습니다.
 * 값은 다중값 특성에서 성공적으로 삭제되어야 합니다.

## <a name="111170"></a>1.1.117.0
출시 날짜: 2016년 3월

**일반 SQL 커넥터**  
의 [일반 SQL 커넥터](active-directory-aadconnectsync-connector-genericsql.md)초기 릴리스입니다.

**새로운 기능:**

* 일반 LDAP 커넥터:
  * Isode에서 델타 가져오기에 대한 지원을 추가했습니다.
* 웹 서비스 커넥터:
  * 개체 수준 오류가 동기화 엔진으로 다시 반환될 수 있도록 csEntryChangeResult 작업 및 setImportErrorCode 작업을 업데이트했습니다.
  * 새 개체 수준 오류 기능을 사용하도록 SAP6 및 SAP6User 템플릿을 업데이트했습니다.
* Lotus Domino 커넥터:
  * 내보내는 경우 주소록당 하나의 인증자가 있어야 합니다. 이제 관리를 쉽게 수행할 수 있도록 모든 인증자에 대해 동일한 암호를 사용할 수 있습니다.

**수정된 문제:**

* 일반 LDAP 커넥터:
  * IBM Tivoli DS의 경우 일부 참조 특성이 제대로 검색되지 않았습니다.
  * 델타 가져오기 동안 열린 LDAP의 경우 문자열의 시작과 끝에 있는 공백이 잘렸습니다.
  * Novell과 NetIQ의 경우 OU/컨테이너 사이의 개체를 이동하고 동시에 개체의 이름도 바꾼 내보내기가 실패했습니다.
* 웹 서비스 커넥터:
  * 웹 서비스에 동일한 바인딩에 대한 여러 개의 끝점이 있으면 커넥터에서 이러한 끝점을 올바르게 검색하지 않았습니다.
* Lotus Domino 커넥터:
  * fullName 특성을 메일 내 데이터베이스에 내보내지 못했습니다.
  * 그룹에서 멤버를 추가하고 제거한 내보내기에서 추가된 멤버만 내보냈습니다.
  * Notes Document가 잘못된 경우(isValid 특성이 false로 설정) 커넥터가 실패합니다.

## <a name="older-releases"></a>이전 릴리스
2016년 3월 이전에 커넥터가 지원 항목으로 출시되었습니다.

**일반 LDAP**

* [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, 2015년 9월
* [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, 2015년 3월
* [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, 2015년 1월
* [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, 2014년 9월
* [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, 2014년 3월

**WebServices**

* [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, 2014년 9월

**PowerShell**

* [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, 2014년 9월

**Lotus Domino**

* [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, 2015년 9월
* [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, 2015년 3월
* [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, 2014년 8월
* [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, 2014년 2월  
* [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, 2013년 10월
* [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, 2013년 8월

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

