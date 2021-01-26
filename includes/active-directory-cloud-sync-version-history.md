---
ms.openlocfilehash: 35cb7ba4523cfbc88daf958fd972b127519385ce
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792384"
---
이 문서에서는 릴리스된 Azure Active Directory Connect 프로 비전 에이전트의 버전 및 기능을 나열 합니다. Azure AD 팀은 새로운 기능과 기능으로 프로 비전 에이전트를 정기적으로 업데이트 합니다. 프로 비전 에이전트는 새 버전이 릴리스되면 자동으로 업데이트 됩니다. 

Microsoft는 최신 에이전트 버전 및 이전 버전을 직접 지원 합니다.

## <a name="113540"></a>1.1.354.0

2021 년 1 월 20 일: 다운로드를 위해 릴리스

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- 미리 작성 된 사용자 지정 GMSA 계정에 대 한 지원을 포함 하 여 GMSA 환경 개선
- GMSA 설치를 위한 [PowerShell cmdlet](../articles/active-directory/cloud-sync/how-to-gmsa-cmdlets.md) 지원
- 에이전트 설치에 대 한 [CLI 지원](../articles/active-directory/cloud-sync/how-to-install-pshell.md) (자동 설치)
- 에이전트 원본 격리 문제에 대 한 추가 진단
- Ou 범위 지정 필터의 메모리 사용량을 줄이고, 범위 내 사용자에 대해서만 PHS를 실행 하 고, OU 범위를 사용할 때 OU의 중첩 된 개체를 처리 하는 등의 버그 수정 


### <a name="fixed-issues"></a>해결된 문제
-    범위 지정 그룹이 범위를 벗어난 경우 격리 방지
-   범위 지정 필터가 구성 된 경우-PHS 작업은 이제 범위 내 사용자에 대해서만 작동 합니다.
-   업그레이드 하는 동안 에이전트가 잠시 중단 됨
-   OU 범위 지정을 사용 하는 경우 중첩 된 Ou의 개체에 대 한 초기 동기화
-   Repair-AADCloudSyncToolsAccount 더 강력 하 게 만들기
-   OU 범위 지정 필터의 메모리 사용량 감소
-   역할 구성원이 보안 그룹을 포함 하는 경우 관리자 역할 검사가 실패 함
-   에이전트 인증서 갱신을 방지 하는 GMSA folder 권한 문제 해결







## <a name="112810"></a>1.1.281.0

### <a name="release-status"></a>릴리스 상태

2020 년 11 월 23 일: 다운로드를 위해 릴리스

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* [GMSA](../articles/active-directory/cloud-sync/how-to-prerequisites.md#group-managed-service-accounts) 지원
* 증분 또는 델타 동기화 주기 동안 1500 개 미만의 구성원에 최대 크기의 그룹을 지원 합니다. 그룹 범위 지정 필터를 사용 하는 경우에 적용할 수 있습니다.
* 구성원 크기가 최대 15K 인 큰 그룹 지원
* 초기 동기화 기능 향상
* 고급 정보 로깅
* [AADCloudSyncTools PowerShell 모듈](../articles/active-directory/cloud-sync/reference-powershell.md) 소개
* 영어가 아닌 서버에 에이전트를 설치할 수 있도록 하는 제한 사항이 수정 되었습니다.
* 범위에 있는 개체에 대 한 PHS 필터링 지원 (원래 모든 개체에 대 한 암호 해시 동기화 중)
* 에이전트의 메모리 누수 문제를 수정 했습니다.
* 향상 된 프로 비전 로그
* [LDAP 연결 제한 시간](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-ldap-connection-timeout) 구성 지원 
* [조회 추적](../articles/active-directory/cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing) 구성 지원 


## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>릴리스 상태

2019 년 12 월 4 일: 다운로드를 위해 릴리스 됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 온-프레미스 Active Directory에서 Azure AD로 사용자, 연락처 및 그룹 데이터를 동기화 하는 [Azure AD Connect 클라우드 동기화](../articles/active-directory/cloud-sync/what-is-cloud-sync.md) 를 지원 합니다.


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>릴리스 상태

2019 년 9 월 9 일: 자동 업데이트를 위해 릴리스 됨

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 프로 비전 에이전트 문제를 디버깅 하기 위해 추가 추적 및 로깅을 구성 하는 기능
* 동기화 성능을 향상 시키기 위해 매핑에 구성 된 Azure AD 특성만 페치할 수 있습니다.

### <a name="fixed-issues"></a>해결된 문제

* Azure AD 연결 오류와 관련 된 문제가 있는 경우 에이전트가 응답 하지 않는 상태로 전환 되는 버그를 수정 했습니다.
* Azure Active Directory에서 이진 데이터를 읽을 때 문제를 일으킨 버그를 수정 했습니다.
* 에이전트가 클라우드 하이브리드 id 서비스와의 트러스트를 갱신 하지 못한 버그를 수정 했습니다.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>릴리스 상태

2019 년 1 월 23 일: 다운로드를 위해 릴리스

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능

* 더 나은 성능, 안정성 및 안정성을 위해 프로 비전 에이전트 및 커넥터 아키텍처를 개선 된. 
* UI 기반 설치 마법사를 사용 하 여 프로 비전 에이전트 구성 간소화 
* 자동 에이전트 업데이트에 대 한 지원 추가


