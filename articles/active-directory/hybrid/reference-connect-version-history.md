---
title: 'Azure AD Connect: 버전 릴리스 내역 | Microsoft Docs'
description: 이 문서에서는 Azure AD Connect 및 Azure AD Sync의 모든 릴리스를 나열 합니다.
services: active-directory
author: billmath
manager: daveba
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.topic: reference
ms.workload: identity
ms.date: 08/07/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73318d1ee14894f5d22f7c4d2e61418e3b1038c1
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94636880"
---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: 버전 릴리스 내역
Azure AD(Azure Active Directory) 팀은 새로운 기능과 성능으로 Azure AD Connect를 정기적으로 업데이트합니다. 모든 추가 내용이 모든 대상에 적용되는 것은 아닙니다.

이 문서는 릴리스된 버전을 추적하고 최신 버전에 변경 내용을 이해할 수 있도록 도와줍니다.



이 테이블은 관련 항목 목록입니다.

항목 |  세부 정보
--------- | --------- |
Azure AD Connect에서 업그레이드하는 단계 | Azure AD Connect 릴리스를 [이전 버전에서 최신 버전으로 업그레이드](how-to-upgrade-previous-version.md) 하는 다른 방법입니다.
필요한 사용 권한 | 업데이트를 적용하는 데 필요한 사용 권한은 [계정 및 사용 권한](reference-connect-accounts-permissions.md#upgrade)을 참조하세요.
다운로드| [Azure AD Connect 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771).

>[!NOTE]
>새로운 버전의 Azure AD Connect를 릴리스하는 과정은 서비스 작업 기능을 보장하기 위해 몇 가지 품질 관리 단계가 필요한 프로세스이며, 이 프로세스를 거치는 동안 새 릴리스의 버전 번호와 릴리스 상태가 최신 상태를 반영하도록 업데이트됩니다.
이 프로세스를 진행하는 동안 릴리스 버전 번호는 "1.3.X.0"과 같이 부 릴리스 번호 위치에 "X"로 표시되며, 이 문서의 릴리스 정보는 "1.3"으로 시작하는 모든 버전에 유효하다는 의미입니다. 릴리스 프로세스가 완료되는 즉시 릴리스 버전 번호는 가장 최근에 릴리스된 버전으로 업데이트되고 릴리스 상태는 "자동 업그레이드 및 다운로드용으로 릴리스"로 업데이트됩니다.
일부 버전의 Azure AD Connect는 자동 업그레이드에 사용할 수 있습니다. 릴리스 상태는 릴리스가 자동 업그레이드 또는 다운로드에만 사용할 수 있는지 여부를 나타냅니다. 자동 업그레이드가 Azure AD Connect 서버에서 활성화된 경우 해당 서버는 자동으로 자동 업그레이드용으로 릴리스되는 최신 버전의 Azure AD Connect로 업그레이드됩니다. 일부 Azure AD Connect 구성을 자동 업그레이드에 사용할 수 있습니다. 

자동 업그레이드를 명확 하 게 하기 위해 중요 한 업데이트 및 중요 한 픽스를 모두 푸시하는 것이 좋습니다. 모든 버전에서 중요 한 보안 문제에 대 한 픽스를 요구 하거나 포함 하지 않기 때문에 최신 버전이 아닐 수도 있습니다 (예: 다 수의 한 예만). 자동 업그레이드를 통해 제공 되는 새 버전을 사용 하 여 문제를 해결할 수 있습니다. 이러한 문제가 없으면 자동 업그레이드를 사용 하 여 푸시 된 업데이트가 없으며, 일반적으로 최신 자동 업그레이드 버전을 사용 하는 경우에는 정상입니다.
그러나 모든 최신 기능 및 업데이트를 원하는 경우이 페이지를 확인 하 고 적절 하 게 설치 하는 것이 가장 좋은 방법입니다. 

[자동 업그레이드](how-to-connect-install-automatic-upgrade.md)에 대한 자세한 내용은 이 링크 참조

>[!IMPORTANT]
> 2020년 11월 1일부터 사용 중단 프로세스를 구현하기 시작하여, 릴리스된지 18개월이 넘은 Azure AD Connect 버전이 더 이상 사용되지 않게 됩니다. 이번에는 버전 1.3.20.0(2019년 4월 24일에 릴리스됨) 및 이전 버전의 모든 Azure AD Connect 릴리스를 사용 중단하는 것으로 시작하여 새 버전이 출시될 때마다 이전 Azure AD Connect 버전의 사용 중단을 평가할 예정입니다.
>
> 최적의 지원 환경을 받으려면 최신 버전의 Azure AD Connect를 실행하고 있는지 확인해야 합니다. 
>
>사용되지 않는 Azure AD Connect 버전을 실행하면 최신 보안 수정, 성능 향상, 문제 해결 및 진단 도구와 서비스 개선 사항이 포함되지 않을 수 있으며, 지원이 필요한 경우 귀사에 필요한 수준의 서비스를 제공하지 못할 수 있습니다.
>
>Azure AD Connect에서 동기화를 사용하도록 설정하면 이전 버전을 실행하는 경우 예정된 사용 중단에 대해 경고하는 상태 알림을 자동으로 받게 됩니다.
>
>Azure AD Connect를 최신 버전으로 업그레이드하는 방법에 대한 자세한 내용은 [이 문서](./how-to-upgrade-previous-version.md)를 참조하세요.
>
>사용 되지 않는 버전에 대 한 버전 기록 정보는 [Azure AD Connect 버전 릴리스 기록 보관](reference-connect-version-history-archive.md) 을 참조 하세요.

## <a name="15450"></a>1.5.45.0

### <a name="release-status"></a>릴리스 상태
07/29/2020: 다운로드를 위해 릴리스 됨

### <a name="functional-changes"></a>기능 변경 내용
버그 수정 릴리스입니다. 이 릴리스에는 기능이 변경 되지 않았습니다.

### <a name="fixed-issues"></a>해결된 문제

- AZUREADSSOACC 컴퓨터 계정이 이미 "Active Directory"에 있는 경우 관리자가 "원활한 Single Sign-on"을 사용 하도록 설정할 수 없는 문제를 해결 했습니다.
- 상태 포털을 통해 복구 된 충돌 하는 개체에 대해 V2 API 델타 가져오기 작업을 수행 하는 동안 준비 오류를 발생 시킨 문제를 해결 했습니다.
- 사용 하지 않도록 설정 된 사용자 지정 규칙을 사용 하도록 설정한 경우 가져오기/내보내기 구성에서 문제가 해결 되었습니다.

## <a name="15420"></a>1.5.42.0

### <a name="release-status"></a>릴리스 상태
07/10/2020: 다운로드를 위해 릴리스 됨

### <a name="functional-changes"></a>기능 변경 내용
이 릴리스에는 기존 Azure AD Connect 서버의 구성을로 내보내는 기능의 공개 미리 보기가 포함 되어 있습니다. 새 Azure AD Connect 서버를 설치 하 여 원본 서버의 복사본을 만들 때 사용할 수 있는 JSON 파일입니다.

이 새로운 기능에 대 한 자세한 설명은 [이 문서](./how-to-connect-import-export-config.md) 에서 찾을 수 있습니다.

### <a name="fixed-issues"></a>해결된 문제
- 업그레이드 하는 동안 지역화 된 빌드의 로컬 DB 크기에 대 한 거짓 경고가 발생 하는 버그를 수정 했습니다.
- 계정 이름/도메인 이름 바꾸기에 대 한 앱 이벤트에 잘못 된 오류가 발생 하는 버그를 수정 했습니다.
- DC에 Azure AD Connect 설치에 실패 하 고 "멤버를 찾을 수 없음" 오류가 발생 하는 오류를 수정 했습니다.


## <a name="15300"></a>1.5.30.0

### <a name="release-status"></a>릴리스 상태
2020년 5월 7일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드는 손자 컨테이너만 선택한 경우 마법사 UI에서 선택하지 않은 도메인이 잘못 선택되는 문제를 해결합니다.


>[!NOTE]
>이 버전에는 새 Azure AD Connect 동기화 V2 엔드포인트 API가 포함 됩니다.  새로운 V2 엔드포인트는 현재 공개 미리 보기 상태입니다.  새로운 V2 엔드포인트 API를 사용하려면 이 버전 이상이 필요합니다.  하지만 이 버전을 설치하는 것만으로는 V2 엔드포인트를 사용할 수 없습니다. V2 엔드포인트를 사용하도록 설정하지 않으면 V1 엔드포인트가 계속 사용됩니다.  사용하도록 설정하고 공개 미리 보기를 옵트인하려면 [Azure AD Connect 동기화 V2 엔드포인트 API(공개 미리 보기)](how-to-connect-sync-endpoint-api-v2.md)의 단계를 수행해야 합니다.  

## <a name="15290"></a>1.5.29.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 23일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드는 빌드 1.5.20.0에서 발생한 MFA를 사용하는 테넌트 관리자가 DSSO를 사용하도록 설정할 수 없는 문제를 해결합니다.

## <a name="15220"></a>1.5.22.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 20일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
이 핫픽스 빌드는 1.5.20.0 빌드에서 **AD에서 들어오기 – 그룹 조인** 규칙을 복제하고 **AD에서 들어오기 – 그룹 공통** 규칙을 복제하지 않는 경우 발생하는 문제를 해결합니다.

## <a name="15200"></a>1.5.20.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 9일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제
- 이 핫픽스 빌드는 1.5.18.0 빌드에서 그룹 필터링 기능을 사용하도록 설정하고 mS-DS-ConsistencyGuid를 소스 앵커로 사용하는 경우 발생하는 문제를 해결합니다.
- 모든 Set-ADSync* Permissions cmdlet에 사용된 DSACLS 명령을 호출하면 다음 오류 중 하나가 발생하는 ADSyncConfig PowerShell 모듈의 문제가 해결되었습니다.
     - `GrantAclsNoInheritance : The parameter is incorrect.   The command failed to complete successfully.`
     - `GrantAcls : No GUID Found for computer …`

> [!IMPORTANT]
> **AD에서 들어오기 – 그룹 조인** 동기화 규칙을 복제하고 **AD에서 들어오기 – 그룹 공통** 동기화 규칙을 복제하지 않는 상태에서 업그레이드하려는 경우에는 업그레이드의 일환으로 다음 단계를 완료하십시오.
> 1. 업그레이드 중에 **구성이 완료되면 동기화 프로세스를 시작합니다.** 옵션을 선택 취소합니다.
> 2. 복제된 조인 동기화 규칙을 편집하고 다음 두 가지 변환을 추가합니다.
>     - 직접 흐름 `objectGUID`를 `sourceAnchorBinary`로 설정합니다.
>     - 식 흐름 `ConvertToBase64([objectGUID])`를 `sourceAnchor`로 설정합니다.     
> 3. `Set-ADSyncScheduler -SyncCycleEnabled $true`를 사용하여 스케줄러를 사용하도록 설정합니다.



## <a name="15180"></a>1.5.18.0

### <a name="release-status"></a>릴리스 상태
2020년 4월 2일: 다운로드용으로 릴리스됨

### <a name="functional-changes-adsyncautoupgrade"></a>기능 변경 ADSyncAutoUpgrade 

- 그룹 개체의 mS-DS-ConsistencyGuid 기능에 대한 지원이 추가되었습니다. 이를 통해 포리스트 간에 그룹을 이동하거나 AD의 그룹을 AD 그룹 objectID가 변경된(예: 재난 후에 AD 서버를 재 구축한 경우) Azure AD에 다시 연결할 수 있습니다. 자세한 내용은 [포리스트 간 그룹 이동](how-to-connect-migrate-groups.md)을 참조하세요.
- mS-DS-ConsistencyGuid 특성이 동기화된 모든 그룹에 자동으로 설정되어, 이 기능을 사용하도록 설정하기 위해 아무것도 수행할 필요가 없습니다. 
- Get-ADSyncRunProfile이 더 이상 사용되지 않기 때문에 제거되었습니다. 
- AD DS 커넥터 계정에 대해 엔터프라이즈 관리자 또는 도메인 관리자 계정을 사용하여 더 많은 컨텍스트를 제공하려고 할 때 보이는 경고가 변경되었습니다. 
- 기존 CSDelete.exe 도구가 제거된 커넥터 공간에서 개체를 제거하는 새 cmdlet이 추가되고 새 Remove-ADSyncCSObject cmdlet으로 대체되었습니다. Remove-ADSyncCSObject cmdlet은 CsObject를 입력으로 사용합니다. 이 개체는 Get-ADSyncCSObject cmdlet을 사용하여 검색할 수 있습니다.

>[!NOTE]
>이전 CSDelete.exe 도구가 제거되고 새로운 Remove-ADSyncCSObject cmdlet으로 대체되었습니다. 

### <a name="fixed-issues"></a>해결된 문제

- 기능을 사용하지 않도록 설정한 후 Azure AD Connect 마법사를 다시 실행하는 경우 그룹 쓰기 저장(writeback) 포리스트/OU 선택기에 있는 버그가 수정되었습니다. 
- 필요한 DCOM 레지스트리 값에 새 도움말 링크가 누락된 경우 표시되는 새 오류 페이지가 도입되었습니다. 정보는 로그 파일에도 기록됩니다. 
- 계정을 사용하려고 하기 전에 계정이 모든 서비스 복제본에 전파되지 않았기 때문에 디렉터리 확장 또는 PHS를 사용하도록 설정하면 실패할 수 있는, Azure Active Directory 동기화 계정 생성과 관련된 문제가 해결되었습니다. 
- 서로게이트 문자를 올바르게 처리하지 못하는 동기화 오류 압축 유틸리티의 버그가 수정되었습니다. 
- 서버를 스케줄러 일시 중단 상태로 두는 자동 업그레이드의 버그가 수정되었습니다. 

## <a name="14380"></a>1.4.38.0
### <a name="release-status"></a>릴리스 상태
2019년 12월 9일: 다운로드용 릴리스입니다. 자동 업그레이드를 통해 사용할 수 없습니다.
### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- Kerberos 해시의 패딩을 올바르게 처리하도록 Azure AD Domain Services에 대한 암호 해시 동기화가 업데이트되었습니다.  이렇게 하면 Azure AD에서 Azure AD Domain Services로 암호를 동기화 하는 동안 성능을 향상 시킬 수 있습니다.
- 인증 에이전트와 서비스 버스 간의 신뢰할 수 있는 세션을 위한 지원이 추가되었습니다.
- 이 릴리스는 인증 에이전트와 클라우드 서비스 간의 통신에 TLS 1.2를 적용합니다.
- 인증 에이전트와 클라우드 서비스 간의 WebSocket 연결을 위한 DNS 캐시가 추가되었습니다.
- 에이전트 연결을 테스트하기 위해 클라우드에서 특정 에이전트를 대상으로 하는 기능이 추가되었습니다.

### <a name="fixed-issues"></a>해결된 문제
- 릴리스 1.4.18.0에는 DSSO용 PowerShell cmdlet이 ps를 실행하는 동안 제공된 관리자 자격 증명 대신 로그인 창 자격 증명을 사용하는 버그가 있었습니다. 따라서 Azure AD Connect 사용자 인터페이스를 통해 여러 포리스트에서 DSSO 사용 하도록 설정할 수 없습니다. 
- Azure AD Connect 사용자 인터페이스를 통해 모든 포리스트에서 동시에 DSSO 사용 하도록 수정 되었습니다.

## <a name="14320"></a>1.4.32.0
### <a name="release-status"></a>릴리스 상태
2019년 11월 8일: 다운로드용으로 릴리스되었습니다. 자동 업그레이드를 통해 사용할 수 없습니다.

>[!IMPORTANT]
>이번 Azure AD Connect 릴리스의 내부 스키마가 변경되어 MSOnline PowerShell을 사용하여 AD FS 트러스트 관계 구성 설정을 관리하는 경우에는 MSOnline PowerShell 모듈을 1.1.183.57 이상 버전으로 업데이트해야 합니다.

### <a name="fixed-issues"></a>해결된 문제

이 버전은 기존 하이브리드 Azure AD 조인 디바이스 관련 문제를 해결합니다. 이 릴리스에는 이 문제를 해결하는 새로운 디바이스 동기화 규칙이 포함되어 있습니다.
이렇게 규칙이 변경되어 Azure AD에서 사용되지 않는 디바이스가 삭제될 수 있습니다. 조건부 액세스 권한 부여 중에는 Azure AD에서 해당 디바이스 개체가 사용되지 않기 때문에 걱정할 필요가 없습니다. 일부 고객의 경우 규칙 변경을 통해 삭제되는 디바이스 수가 삭제 임계값을 초과할 수 있습니다. Azure AD에서 디바이스 개체를 삭제하는 것이 내보내기 삭제 임계값을 초과하는 것으로 보이면, 삭제가 진행되도록 허용하는 것이 좋습니다. [삭제 임계값을 초과하는 경우 삭제가 진행되도록 하는 방법](how-to-connect-sync-feature-prevent-accidental-deletes.md)

## <a name="14250"></a>1.4.25.0

### <a name="release-status"></a>릴리스 상태
2019년 9월 28일: 테넌트를 선택하는 자동 업그레이드용으로 릴리스되었습니다. 다운로드할 수 없습니다.

이 버전은 이전 버전에서 1.4.18.0으로 자동 업그레이드된 일부 서버에서 SSPR(셀프 서비스 암호 재설정) 및 암호 쓰기 저장 관련 문제가 발생하는 버그를 수정합니다.

### <a name="fixed-issues"></a>해결된 문제

특정 상황에서 버전 1.4.18.0으로 자동 업그레이드된 서버가 업그레이드가 완료된 후 셀프 서비스 암호 재설정 및 암호 쓰기 저장이 다시 활성화되지 않았습니다. 이번 자동 업그레이드 릴리스는 이 문제를 해결하고 셀프 서비스 암호 재설정 및 암호 쓰기 저장을 다시 활성화합니다.

서로게이트 문자를 올바르게 처리하지 못하는 동기화 오류 압축 유틸리티의 버그가 수정되었습니다.

## <a name="14180"></a>1.4.18.0

>[!WARNING]
>이 버전의 Azure AD Connect로 업그레이드한 후 일부 고객에게 기존 하이브리드 Azure AD 조인 디바이스 관련 문제가 발생하는 인시던트를 조사하고 있습니다. 하이브리드 Azure AD 조인을 배포한 고객은 문제의 근본 원인이 완전히 파악되어 완화될 때까지 이 버전으로 업그레이드하는 것을 연기하는 것이 좋습니다. 가능한 빨리 자세한 정보를 제공하겠습니다.

>[!IMPORTANT]
>이 버전의 Azure AD Connect를 사용하면 일부 고객이 Windows 디바이스의 일부 또는 전부가 Azure AD에서 사라지는 것을 볼 수 있습니다. 조건부 액세스 권한 부여 중에는 Azure AD에서 해당 디바이스 ID가 사용되지 않기 때문에 걱정할 필요가 없습니다. 자세한 내용은 [Azure AD Connect 1.4.xx.x 디바이스 누락 이해](reference-connect-device-disappearance.md)를 참조하세요.


### <a name="release-status"></a>릴리스 상태
2019년 9월 25일: 자동 업그레이드 전용으로 릴리스되었습니다.

### <a name="new-features-and-improvements"></a>새로운 기능 및 향상 기능
- 새로운 문제 해결 도구는 "사용자 동기화 안 됨", "그룹 동기화 안 됨" 또는 "그룹 구성원 동기화 안 됨" 시나리오에서 문제를 해결하는 데 유용합니다.
- Azure AD Connect 문제 해결 스크립트에서 국가별 클라우드에 대 한 지원을 추가 합니다.
- MIIS_Service에 더 이상 사용되지 않는 WMI 엔드포인트가 이제 제거된 것을 고객에게 알려야 합니다. 모든 WMI 작업은 이제 PS cmdlet을 통해 수행됩니다.
- AZUREADSSOACC 개체에 대 한 제한 된 위임을 다시 설정 하 여 보안을 개선 합니다.
- 동기화 규칙을 추가/편집하는 경우 커넥터 스키마에 있지만 커넥터에 추가되지 않은 규칙에 사용된 특성이 있으면 이 특성은 커넥터에 자동으로 추가됩니다. 규칙이 적용되는 개체 형식에 대해서도 마찬가지입니다. 커넥터에 추가된 항목이 있으면 다음 동기화 주기에 커넥터가 전체 가져오기로 표시됩니다.
- 새 Azure AD Connect 배포에서는 엔터프라이즈 또는 도메인 관리자를 커넥터 계정으로 사용하는 것이 더 이상 지원되지 않습니다. 엔터프라이즈 또는 도메인 관리자를 커넥터 계정으로 사용 하는 현재 Azure AD Connect 배포는이 릴리스의 영향을 받지 않습니다.
- 동기화 관리자에서 규칙 생성/편집/삭제 시 전체 동기화가 실행됩니다. 규칙 변경 시 팝업이 나타나서 전체 가져오기 또는 전체 동기화가 실행되는 경우 사용자에게 알립니다.
- ' 커넥터 > 속성 > 연결 ' 페이지에 암호 오류에 대 한 완화 단계를 추가 했습니다.
- 커넥터 속성 페이지에 동기화 서비스 관리자에 대한 사용 중단 경고가 추가되었습니다. 이 경고는 Azure AD Connect 마법사를 통해 변경해야 한다고 사용자에게 알려줍니다.
- 사용자의 암호 정책과 관련된 문제에 대해 새 오류가 추가되었습니다.
- 도메인 및 OU 필터로 그룹 필터링이 잘못 구성되지 않도록 합니다. 입력한 그룹의 도메인/OU가 이미 필터링된 경우에는 그룹 필터링에 오류가 표시되고 문제가 해결될 때까지 사용자가 계속 진행하지 못하도록 합니다.
- Synchronization Service Manager UI에서 사용자가 Active Directory Domain Services 또는 Windows Azure Active Directory용 커넥터를 더 이상 만들 수 없습니다.
- Synchronization Service Manager에서 사용자 지정 UI 컨트롤에 대한 접근성을 수정했습니다.
- Azure AD Connect의 모든 로그인 메서드에 대해 페더레이션 관리 작업을 6개 사용할 수 있습니다.  (이전에는, “AD FS TLS/SSL 인증서 업데이트” 작업만 모든 로그인에 사용할 수 있었습니다.)
- 로그인 방법을 페더레이션에서 PHS 또는 PTA로 변경하면 모든 Azure AD 도메인 및 사용자가 관리 인증으로 변환된다는 경고가 추가되었습니다.
- “Azure AD 및 AD FS 트러스트 재설정” 작업에서 토큰 서명 인증서가 제거되고 해당 인증서를 업데이트하는 별도의 하위 작업이 추가되었습니다.
- AD FS 팜의 TLS 또는 토큰 서명 인증서를 업데이트 하는 하위 작업이 포함된 “인증서 관리”라는 페더레이션 관리 작업이 새로 추가되었습니다.
- "주 서버 지정"이라는 페더레이션 관리 하위 작업이 새로 추가되어, 관리자가 AD FS 팜에 대한 주 서버를 새로 지정할 수 있습니다.
- “서버 관리”라는 페더레이션 관리 작업이 새로 추가되었습니다. 여기에는 AD FS 서버 배포, 웹 애플리케이션 프록시 서버 배포, 주 서버 지정을 위한 하위 작업이 포함되어 있습니다.
- 현재 AD FS 설정을 표시하는 "페더레이션 구성 보기"라는 페더레이션 관리 작업이 새로 추가되었습니다.  (이 작업이 추가되면서, AD FS 설정이 “솔루션 검토” 페이지에서 제거되었습니다.)

### <a name="fixed-issues"></a>해결된 문제
- 해당 연락처 개체를 사용하는 사용자 개체에 자체 참조가 있는(예: 사용자가 자신의 관리자임) 시나리오의 동기화 오류 문제가 해결되었습니다.
- 도움말 팝업이 이제 키보드 포커스에 표시됩니다.
- 자동 업그레이드의 경우, 충돌하는 앱이 6시간 이상 실행되면 종료하고 업그레이드를 계속합니다.
- 디렉터리 확장을 선택할 때 고객이 선택할 수 있는 특성의 수가 개체당 100개로 제한됩니다. 이렇게 하면 내보내기 중에 오류가 발생하지 않습니다. Azure에서 개체당 최대 확장 특성이 최대 100개이기 때문입니다.
- AD 연결 스크립트를 보다 강력 하 게 만드는 버그를 수정 했습니다.
- 기존 명명 된 파이프 WCF 서비스를 보다 강력 하 게 사용 하 여 컴퓨터에 Azure AD Connect 설치 하는 버그를 수정 했습니다.
- 처음 설치할 때 ADSync 서비스를 시작할 수 없는 그룹 정책 관련 진단 및 문제 해결 기능이 향상되었습니다.
- Windows 컴퓨터의 표시 이름이 잘못 쓰여지는 버그를 수정했습니다.
- Windows 컴퓨터의 OS 유형이 잘못 쓰여지는 버그를 수정했습니다.
- Windows 10 이외의 컴퓨터가 예기치 않게 동기화되는 버그를 수정했습니다. 그에 따라 이전에 동기화되던 Windows 10 이외의 컴퓨터가 이제는 삭제됩니다. 이러한 변화는 어떤 기능에도 영향을 주지 않습니다. Windows 컴퓨터의 동기화는 하이브리드 Azure AD 도메인 가입에만 사용되고, 이 기능은 Windows-10 디바이스에 대해서만 작동하기 때문입니다.
- ADSync PowerShell 모듈에 몇 가지 새로운(내부) cmdlet이 추가되었습니다.


## <a name="13210"></a>1.3.21.0
>[!IMPORTANT]
>이전 버전에서 1.3.21.0로 Azure AD Connect 업그레이드 하는 것과 관련 된 알려진 문제가 있습니다. 이러한 문제는 Microsoft 365 포털에서 성공적으로 업그레이드 Azure AD Connect 경우에도 업데이트 된 버전을 반영 하지 않습니다.
>
> 이 문제를 해결 하려면 **Adsync** 모듈을 가져온 다음 `Set-ADSyncDirSyncConfiguration` Azure AD Connect 서버에서 PowerShell cmdlet을 실행 해야 합니다.  다음 단계를 사용할 수 있습니다.
>
>1. 관리자 모드에서 PowerShell을 엽니다.
>2. `Import-Module "ADSync"`을 실행합니다.
>3. `Set-ADSyncDirSyncConfiguration -AnchorAttribute ""`을 실행합니다.
 
### <a name="release-status"></a>릴리스 상태 

2019년 5월 14일: 다운로드용으로 릴리스됨

### <a name="fixed-issues"></a>해결된 문제 

- Microsoft Azure Active Directory Connect 빌드 1.3.20.0에 존재하는 권한 상승 취약성을 수정했습니다.  이 취약성은, 특정 조건에서, 공격자가 권한 있는 계정의 컨텍스트에서 PowerShell cmdlet 두 가지를 실행하여 권한 있는 작업을 수행할 수 있도록 합니다.  이 보안 업데이트는 해당 cmdlet을 사용할 수 없도록 설정하여 문제를 해결합니다. 자세한 내용은 [보안 업데이트](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1000)를 참조하세요.


## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
