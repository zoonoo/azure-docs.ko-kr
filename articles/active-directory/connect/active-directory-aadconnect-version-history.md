---
title: "Azure AD Connect: 버전 릴리스 내역 | Microsoft Docs"
description: "이 문서에는 Azure AD Connect 및 Azure AD Sync의 모든 릴리스가 나열되어 있습니다."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 9553c9ed02fa198d210fcb64f4657f84ef3df801
ms.openlocfilehash: d89135c8f3d5011d7549158a29050e3569defbcc
ms.lasthandoff: 03/23/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect: 버전 릴리스 내역
Azure AD(Azure Active Directory) 팀은 새로운 기능과 성능으로 Azure AD Connect를 정기적으로 업데이트합니다. 모든 추가 내용이 모든 대상에 적용되는 것은 아닙니다.

이 문서는 릴리스된 버전을 추적하고 최신 버전으로 업데이트해야 하는지 여부를 파악할 수 있도록 도와줍니다.

다음은 관련 항목 목록입니다.


항목 |  세부 정보
--------- | --------- |
Azure AD Connect에서 업그레이드하는 단계 | Azure AD Connect 릴리스를 [이전 버전에서 최신 버전으로 업그레이드](active-directory-aadconnect-upgrade-previous-version.md) 하는 다른 방법입니다.
필요한 사용 권한 | 업데이트를 적용하는 데 필요한 사용 권한은 [계정 및 사용 권한](./active-directory-aadconnect-accounts-permissions.md#upgrade)을 참조하세요.
다운로드| [Azure AD Connect 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="114430"></a>1.1.443.0
릴리스 날짜: 2017년 3월

**수정된 문제:**

Azure AD Connect 동기화
* Azure AD Connect의 표시 이름에 Azure AD 테넌트에 할당된 초기 onmicrosoft.com 도메인이 포함되어 있지 않을 경우 Azure AD Connect 마법사가 작동되지 않는 문제가 해결되었습니다.
* 동기화 서비스 계정의 암호에 아포스트로피, 콜론 및 공백 등의 특수 문자가 포함될 경우 SQL Database에 연결하는 동안 Azure AD Connect 마법사가 실패하는 문제가 해결되었습니다.
* 온-프레미스 AD 개체를 동기화에서 일시적으로 제외시킨 다음 동기화를 위해 다시 포함시킨 경우 스테이징 모드의 Azure AD Connect 서버에서 "dimage에 d이미지와는 다른 앵커가 있습니다." 오류를 발생하는 문제가 해결되었습니다.
* 온-프레미스 AD 개체를 동기화에서 일시적으로 제외시킨 다음 동기화를 위해 다시 포함시킨 경우 스테이징 모드의 Azure AD Connect 서버에서 "DN에서 찾은 개체가 가상입니다." 오류를 발생하는 문제가 해결되었습니다.

AD FS 관리
* 대체 로그인 ID가 구성된 후 Azure AD Connect 마법사가 신뢰 당사자 트러스터에 대해 AD FS 구성을 업데이트하지 않고 적절한 클레임을 설정하지 않는 문제가 해결되었습니다.
* Azure AD Connect 마법사가 해당 서비스 계정이 sAMAccountName 형식 대신 userPrincipalName 형식을 사용하여 구성된 AD FS 서버를 올바르게 처리할 수 없는 문제가 해결되었습니다.

통과 인증
* 통과 인증이 선택되었으나 해당 커넥터 등록이 실패하는 경우 Azure AD Connect 마법사가 실패하도록 하는 문제가 해결되었습니다.
* 데스크톱 SSO 기능이 사용되도록 설정된 경우 Azure AD Connect 마법사가 선택된 로그인 방법에 대한 유효성 검사를 무시하도록 하는 문제가 해결되었습니다.

암호 재설정
* 연결이 방화벽 또는 프록시에 의해 중단된 경우 Azure AAD Connect 서버가 다시 연결하려고 하지 않을 수 있는 문제를 해결했습니다.

**새 기능/향상된 기능:**

Azure AD Connect 동기화
* 이제 Get-ADSyncScheduler cmdlet은 SyncCycleInProgress라는 새 부울 속성을 반환합니다. 반환된 값이 true이면 진행 중인 예약된 동기화 주기가 있는 것입니다.
* 로그 파일에 대한 접근성 개선을 위해 Azure AD Connect 설치 및 설정 로그를 저장하기 위한 대상 폴더가 %localappdata%\AADConnect에서 %programdata%\AADConnect로 이동되었습니다.

AD FS 관리
* AD FS 팜 SSL 인증서를 업데이트하기 위한 지원이 추가되었습니다.
* AD FS 2016을 관리하기 위한 지원이 추가되었습니다.
* 이제 AD FS를 설치하는 동안 기존 gMSA(그룹 관리 서비스 계정)를 지정할 수 있습니다.
* 이제 Azure AD 신뢰 당사자 트러스트에 대한 서명 해시 알고리즘으로 SHA-256을 구성할 수 있습니다.

암호 재설정
* 제품이 더 엄격한 방화벽 규칙이 포함된 환경에서 작동할 수 있도록 향상된 기능을 도입했습니다.
* Azure Service Bus에 대한 향상된 연결 안정성.

## <a name="113800"></a>1.1.380.0
릴리스 날짜: 2016년 12월

**해결된 문제:**

* AD FS(Active Directory Federation Services)에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되는 문제를 해결했습니다.

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.

## <a name="113710"></a>1.1.371.0
릴리스 날짜: 2016년 12월

**알려진 문제:**

* AD FS에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되었습니다. issuerid 클레임 규칙은 Azure AD(Azure Active Directory)로 여러 도메인을 통합하려는 경우에 필요합니다. Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우 이 빌드로 업그레이드하면 AD FS 구성에서 기존 issuerid 클레임 규칙이 제거됩니다. 설치/업그레이드 후 issuerid 클레임 규칙을 추가하여 문제를 해결할 수 있습니다. issuerid 클레임 규칙 추가에 대한 자세한 내용은 [Azure AD와 통합에 다중 도메인 지원](active-directory-aadconnect-multiple-domains.md)에 대한 다음 문서를 참조하세요.

**해결된 문제:**

* 포트 9090이 아웃바운드 연결에 대해 열려있지 않으면 Azure AD Connect 설치 또는 업그레이드가 실패합니다.

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.

## <a name="113700"></a>1.1.370.0
릴리스 날짜: 2016년 12월

**알려진 문제:**

* AD FS에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되었습니다. issuerid 클레임 규칙은 Azure AD로 여러 도메인을 통합하려는 경우에 필요합니다. Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우 이 빌드로 업그레이드하면 AD FS 구성에서 기존 issuerid 클레임 규칙이 제거됩니다. 설치/업그레이드 후 issuerid 클레임 규칙을 추가하여 문제를 해결할 수 있습니다. issuerid 클레임 규칙 추가에 대한 자세한 내용은 [Azure AD와 통합에 다중 도메인 지원](active-directory-aadconnect-multiple-domains.md)에 대한 다음 문서를 참조하세요.
* 설치를 완료하려면 포트 9090은 아웃바운드에 열려있어야 합니다.

**새로운 기능:**

* 통과 인증(미리 보기)

>[!NOTE]
>이 빌드는 Azure AD Connect 자동 업그레이드 기능을 통해 고객에게 제공되지 않습니다.

## <a name="113430"></a>1.1.343.0
릴리스 날짜: 2016년 11월

**알려진 문제:**

* AD FS에 대한 issuerid 클레임 규칙이 이 빌드에서 누락되었습니다. issuerid 클레임 규칙은 Azure AD로 여러 도메인을 통합하려는 경우에 필요합니다. Azure AD Connect를 사용하여 온-프레미스 AD FS 배포를 관리하는 경우 이 빌드로 업그레이드하면 AD FS 구성에서 기존 issuerid 클레임 규칙이 제거됩니다. 설치/업그레이드 후 issuerid 클레임 규칙을 추가하여 문제를 해결할 수 있습니다. issuerid 클레임 규칙 추가에 대한 자세한 내용은 [Azure AD와 통합에 다중 도메인 지원](active-directory-aadconnect-multiple-domains.md)에 대한 다음 문서를 참조하세요.

**수정된 문제:**

* 조직의 암호 정책으로 지정된 복잡성 수준을 충족하는 로컬 서비스 계정을 만들 수 없기 때문에 Azure AD Connect 설치에 실패하는 경우가 있습니다.
* 커넥터 공간에 있는 개체가 동시에 하나의 조인 규칙의 범위를 벗어나게 되고 다른 규칙의 범위에 포함되는 경우 조인 규칙이 다시 평가되지 않는 문제가 해결되었습니다. 이런 상황은 조인 조건이 서로 배타적인 조인 규칙이 둘 이상인 경우 발생할 수 있습니다.
* 조인 규칙을 포함하지 않는 인바운드 동기화 규칙(Azure AD의)이 조인 규칙을 포함하는 규칙보다 낮은 우선 순위 값을 갖는 경우 처리되지 않는 문제가 해결되었습니다.

**향상된 기능:**

* Windows Server 2016 Standard 이상에 Azure AD Connect를 설치할 수 있도록 지원이 추가되었습니다.
* Azure AD Connect에 대한 원격 데이터베이스로 SQL Server 2016을 사용하는 데 대한 지원이 추가되었습니다.

## <a name="112810"></a>1.1.281.0
릴리스 날짜: 2016년 8월

**수정된 문제:**

* 다음 동기화 주기가 완료될 때까지 동기화 간격 변경이 발생하지 않습니다.
* Azure AD Connect 마법사는 사용자 이름이 밑줄(\_)로 시작하는 Azure AD 계정을 허용하지 않습니다.
* 계정 암호에 특수 문자가 너무 많으면 Azure AD Connect 마법사가 Azure AD 계정 인증에 실패합니다. 오류 메시지 "자격 증명의 유효성을 검사할 수 없습니다. 예기치 않은 오류가 발생했습니다." 가 반환됩니다.
* 스테이징 서버를 제거하면 Azure AD 테넌트의 암호 동기화가 비활성화되고 암호 동기화가 활성 서버와 함께 실패합니다.
* 암호 동기화는 사용자에 저장된 암호 해시가 없는 특수한 경우에 실패합니다.
* Azure AD Connect 서버가 준비 모드에서 활성화된 경우 비밀번호 쓰기 저장은 일시적으로 비활성화되지 않습니다.
* Azure AD Connect 마법사는 서버가 준비 모드에 있을 때 실제 암호 동기화 및 비밀번호 쓰기 저장 구성을 표시하지 않습니다. 항상 비활성화로 표시합니다.
* 서버가 준비 모드에 있을 때 암호 동기화 및 비밀번호 쓰기 저장에 대한 구성 변경 내용은 Azure AD Connect 마법사에서 유지되지 않습니다.

**향상된 기능:**

* 새 동기화 주기를 성공적으로 시작할 수 있는지 여부를 표시하도록 Start-ADSyncSyncCycle cmdlet이 업데이트되었습니다.
* 현재 진행 중인 동기화 주기 및 작업을 종료하는 Stop-ADSyncSyncCycle cmdlet이 추가되었습니다.
* 현재 진행 중인 동기화 주기 및 작업을 동기화 주기 및 작업을 종료하는 Stop-ADSyncScheduler cmdlet이 업데이트되었습니다.
* Azure AD Connect 마법사에서 [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)을 구성할 때 이제 "Teletex 문자열" 형식의 AD 특성을 선택할 수 있습니다.

## <a name="111890"></a>1.1.189.0
릴리스 날짜: 2016년 6월

**수정된 문제 및 향상된 기능:**

* 이제 Azure AD Connect를 FIPS 규격 서버에 설치할 수 있습니다.
  * 암호 동기화에 대해서는 [암호 동기화 및 FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)를 참조하세요.
* Active Directory Connector에서 NetBIOS 이름을 FQDN으로 확인할 수 없던 문제를 해결했습니다.

## <a name="111800"></a>1.1.180.0
릴리스 날짜: 2016년 5월

**새로운 기능:**

* 사용자가 Azure AD Connect를 실행하기 전에 도메인을 확인하지 않았으면 사용자에게 그 사실을 알리고 확인할 수 있도록 도와줍니다.
* [Microsoft 클라우드 독일](active-directory-aadconnect-instances.md#microsoft-cloud-germany)에 대한 지원을 추가했습니다.
* 새 URL 요구 사항을 갖는 최신 [Microsoft Azure Government 클라우드](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) 인프라에 대한 지원을 추가했습니다.

**수정된 문제 및 향상된 기능:**

* 쉽게 동기화 규칙을 찾을 수 있도록 동기화 규칙 편집기에 필터링을 추가했습니다.
* 커넥터 공간을 삭제할 경우 성능이 향상되었습니다.
* 동일한 개체가 동일한 실행에서 모두 삭제되고 추가되는 경우(삭제/추가라고 함) 발생하는 문제가 수정되었습니다.
* 비활성화된 동기화 규칙은 더 이상 업그레이드 또는 디렉터리 스키마 새로 고침 시 포함된 개체 및 특성을 다시 활성화하지 않습니다.

## <a name="111300"></a>1.1.130.0
릴리스 날짜: 2016년 4월

**새로운 기능:**

* [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)에 다중 값 특성에 대한 지원이 추가되었습니다.
* 업그레이드 가능한 것으로 간주되는 [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md) 의 자세한 구성 변형에 대한 지원이 추가되었습니다.
* [사용자 지정 스케줄러](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler)에 대한 일부 cmdlet이 추가되었습니다.

## <a name="111190"></a>1.1.119.0
릴리스 날짜: 2016년 3월

**수정된 문제:**

* Windows Server 2008(R2 이전 버전)에는 암호 동기화가 지원되지 않기 때문에 이 운영 체제에서는 빠른 설치를 사용할 수 없습니다.
* 사용자 지정 필터가 구성된 Dirsync에서 업그레이드가 예상대로 작동하지 않습니다.
* 새 릴리스로 업그레이드하고 구성이 변경되지 않은 경우 전체 가져오기/동기화를 예약하지 않아야 합니다.

## <a name="111100"></a>1.1.110.0
릴리스 날짜: 2016년 2월

**수정된 문제:**

* 설치 파일이 기본 C:\Program Files 폴더에 없는 경우 이전 릴리스에서 업그레이드되지 않습니다.
* 설치하고 설치 마법사의 마지막 단계에서 **동기화 프로세스 시작**을 선택 취소하면 설치 마법사를 다시 실행해도 스케줄러를 사용할 수 없습니다.
* 날짜/시간 형식이 US-en이 아닌 서버에서는 스케줄러가 작동하지 않습니다. 또한 올바른 시간을 반환하도록 `Get-ADSyncScheduler`도 차단합니다.
* 로그인 옵션으로 AD FS를 사용하는 Azure AD Connect의 이전 릴리스를 설치하고 업그레이드하는 경우 설치 마법사를 다시 실행할 수 없습니다.

## <a name="111050"></a>1.1.105.0
릴리스 날짜: 2016년 2월

**새로운 기능:**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) 기능
* 설치 마법사에서 Azure Multi-Factor Authentication 및 Privileged Identity Management를 사용하여 전역 관리자를 지원합니다.
  * Multi-Factor Authentication을 사용하는 경우 프록시에서 https://secure.aadcdn.microsoftonline-p.com에 대한 트래픽을 허용하도록 설정해야 합니다.
  * Multi-Factor Authentication이 제대로 작동하려면 신뢰할 수 있는 사이트 목록에 https://secure.aadcdn.microsoftonline-p.com을 추가해야 합니다.
* 초기 설치 후 사용자의 로그인 방법 변경을 허용합니다.
* 설치 마법사에서 [도메인 및 OU 필터링](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) 을 허용합니다. 이는 일부 도메인을 사용할 수 없는 포리스트로의 연결도 허용합니다.
* 동기화 엔진에 [스케줄러](active-directory-aadconnectsync-feature-scheduler.md)가 기본 제공됩니다.

**미리 보기에서 GA로 승격되는 기능:**

* [장치 쓰기 저장](active-directory-aadconnect-feature-device-writeback.md)
* [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)

**새로운 미리 보기 기능:**

* 새 기본 동기화 주기 간격은 30분입니다. 이전 릴리스에서는 3시간이었습니다. [스케줄러](active-directory-aadconnectsync-feature-scheduler.md) 동작 변경 지원이 추가됩니다.

**수정된 문제:**

* DNS 도메인 확인 페이지에서 도메인을 항상 인식하지 않았습니다.
* AD FS를 구성할 때 도메인 관리자 자격 증명을 묻는 메시지가 표시됩니다.
* 온-프레미스 AD 계정이 루트 도메인이 아닌 다른 DNS 트리를 사용하는 도메인에 있는 경우 설치 마법사에서 인식하지 못합니다.

## <a name="1091310"></a>1.0.9131.0
릴리스 날짜: 2015년 12월

**수정된 문제:**

* AD DS(Active Directory Domain Services)에서 암호를 변경할 때는 암호 동기화가 작동하지 않을 수도 있지만 암호를 설정할 때는 작동합니다.
* 프록시 서버를 보유한 경우 설치하는 동안 또는 구성 페이지에서 업그레이드를 취소하면 Azure AD 인증이 실패할 수 있습니다.
* SQL Server SA(시스템 관리자)가 아닌 경우 전체 SQL Server 인스턴스를 사용하여 Azure AD Connect 이전 릴리스를 업데이트하면 업데이트가 실패합니다.
* 원격 SQL Server를 사용하여 Azure AD Connect 이전 릴리스를 업데이트하면 "ADSync SQL 데이터베이스에 액세스할 수 없습니다."라는 오류가 표시됩니다.

## <a name="1091250"></a>1.0.9125.0
릴리스 날짜: 2015년 11월

**새로운 기능:**

* AD FS와 Azure AD 간의 트러스트를 다시 구성할 수 있습니다.
* Active Directory 스키마를 새로 고치고 동기화 규칙을 다시 생성할 수 있습니다.
* 동기화 규칙을 사용하지 않도록 설정할 수 있습니다.
* 동기화 규칙에서 새 리터럴로 "AuthoritativeNull"을 정의할 수 있습니다.

**새로운 미리 보기 기능:**

* [동기화를 위한 Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-sync.md).
* [Azure AD 도메인 서비스](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) 암호 동기화를 지원합니다.

**지원되는 새 시나리오:**

* 여러 온-프레미스 Exchange 조직을 지원합니다. 자세한 내용은 [여러 Active Directory 포리스트가 있는 하이브리드 배포](https://technet.microsoft.com/library/jj873754.aspx)를 참조하세요.

**수정된 문제:**

* 암호 동기화 문제:
  * 범위 외부에서 범위 내부로 이동된 개체는 해당 암호가 동기화되지 않습니다. 여기에는 OU와 특성 필터링이 모두 포함됩니다.
  * 동기화에 포함할 새 OU를 선택하는 데에는 전체 암호 동기화가 필요하지 않습니다.
  * 비활성화된 사용자가 설정된 경우 암호가 동기화되지 않습니다.
  * 암호 재시도 큐에는 제한이 없으며, 사용 중지될 개체 5, 000개에 대한 이전의 제한이 제거되었습니다.
  * [개선된 문제 해결](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization).
* Windows Server 2016 포리스트 기능 수준으로 Active Directory에 연결할 수 없습니다.
* 초기 설치 후 그룹 필터링에 사용된 그룹을 변경할 수 없습니다.
* 비밀번호 쓰기 저장을 활성화하고 암호 변경을 수행하는 모든 사용자에 대해 더 이상 Azure AD Connect 서버에 새로운 사용자 프로필을 만들지 않습니다.
* 동기화 규칙 범위 내 정수(Long) 값을 사용할 수 없습니다.
* 연결할 수 없는 도메인 컨트롤러가 있는 경우 "장치 쓰기 저장" 확인란이 비활성화된 상태로 유지됩니다.

## <a name="1086670"></a>1.0.8667.0
릴리스 날짜: 2015년 8월

**새로운 기능:**

* Azure AD Connect 설치 마법사가 이제 모든 Windows Server 언어로 현지화되었습니다.
* Azure AD 암호 관리 사용 시 계정 잠금 해제에 대한 지원이 추가되었습니다.

**수정된 문제:**

* 설치를 처음 시작한 사용자가 아닌 다른 사용자가 설치를 계속하면 Azure AD Connect 설치 마법사가 충돌합니다.
* 이전에 Azure AD Connect를 제거할 때 Azure AD Connect Sync가 완전히 제거되지 않았다면 다시 설치할 수 없습니다.
* 사용자가 포리스트 루트 도메인에 없거나 Active Directory의 비영어 버전을 사용할 때 빠른 설치를 사용하여 Azure AD Connect를 설치할 수 없습니다.
* Active Directory 사용자 계정의 FQDN을 확인할 수 없을 때 "스키마 커밋 실패"라는 잘못된 오류 메시지가 표시됩니다.
* Active Directory Connector에 사용되는 계정이 마법사 밖에서 변경되면 마법사가 후속 실행에 실패합니다.
* Azure AD Connect가 도메인 컨트롤러에서 설치에 실패하는 경우가 있습니다.
* 확장 특성을 추가하지 않은 경우 “스테이징 모드"를 활성화 및 비활성화할 수 없습니다.
* Active Directory Connector의 잘못된 암호 때문에 일부 구성에서는 비밀번호 쓰기 저장이 실패합니다.
* 특성 필터링에 DN(고유 이름)이 사용되는 경우 DirSync를 업그레이드할 수 없습니다.
* 암호 재설정을 사용할 경우 CPU가 과도하게 사용됩니다.

**제거된 미리 보기 기능:**

* 미리 보기 기능 [사용자 쓰기 저장](active-directory-aadconnect-feature-preview.md#user-writeback) 은 미리 보기 고객의 의견에 따라 일시적으로 제거되었습니다. 고객에게 받은 피드백을 해결한 후 다시 추가할 예정입니다.

## <a name="1086410"></a>1.0.8641.0
릴리스 날짜: 2015년 6월

**Azure AD Connect의 초기 릴리스입니다.**

Azure AD Sync에서 Azure AD Connect로 이름을 변경했습니다.

**새로운 기능:**

* [Express 설정](active-directory-aadconnect-get-started-express.md) 설치
* [AD FS 구성](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 가능
* [DirSync에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md) 가능
* [실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* [스테이징 모드](active-directory-aadconnectsync-operations.md#staging-mode)

**새로운 미리 보기 기능:**

* [사용자 쓰기 저장](active-directory-aadconnect-feature-preview.md#user-writeback)
* [그룹 쓰기 저장](active-directory-aadconnect-feature-preview.md#group-writeback)
* [장치 쓰기 저장](active-directory-aadconnect-feature-device-writeback.md)
* [디렉터리 확장](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
릴리스 날짜: 2015년 5월

**새로운 요구 사항:**

* Azure AD Sync는 이제 .NET Framework 버전 4.5.1을 설치해야 합니다.

**수정된 문제:**

* Azure Service Bus 연결 오류와 함께 Azure AD에서 비밀번호 쓰기 저장이 실패합니다.

## <a name="104910413"></a>1.0.491.0413
릴리스 날짜: 2015년 4월

**수정된 문제 및 향상된 기능:**

* 휴지통이 활성화되어 있고 포리스트에 여러 도메인이 있는 경우 Active Directory Connect가 삭제를 올바르게 처리하지 못했습니다.
* Azure Active Directory Connector에 대해 가져오기 작업의 성능이 향상되었습니다.
* 그룹의 구성원 제한이 초과되면(기본적으로 개체 50,000개로 설정됨) Azure Active Directory에서 해당 그룹이 삭제되었습니다. 이제는 그룹이 삭제되지 않고 오류가 throw되며, 새로운 구성원 변경 내용을 내보내지 않습니다.
* 동일한 DN을 가진 단계적 삭제가 이미 커넥터 공간에 있으면 새 개체를 프로비저닝할 수 없습니다.
* 개체에 스테이징된 변경 사항이 없는데도 일부 개체가 델타 동기화 중에 동기화 대상으로 표시됩니다.
* 강제로 암호를 동기화해도 기본 설정된 DC 목록이 제거됩니다.
* CSExportAnalyzer에 일부 개체 상태에 문제가 있습니다.

**새로운 기능:**

* 이제 조인이 MV에 있는 "ANY" 개체 유형에 연결할 수 있습니다.

## <a name="104850222"></a>1.0.485.0222
릴리스 날짜: 2015년 2월

**향상된 기능:**

* 가져오기 성능이 향상되었습니다.

**수정된 문제:**

* 암호 동기화는 특성 필터링에 사용되는 cloudFiltered 특성을 인식합니다. 필터링된 개체는 더 이상 암호 동기화 범위에 속하지 않습니다.
* 드문 경우지만 토폴로지에 도메인 컨트롤러가 여러 개 있으면 암호 동기화가 작동하지 않습니다.
* Azure AD/Intune에서 장치 관리가 활성화된 후 Azure AD Connector로부터 가져오기 시 “중지된-서버"가 발생합니다.
* 동일한 포리스트 내 여러 도메인으로부터 외부 보안 주체(FSP)를 조인하면 모호한 오류가 발생합니다.

## <a name="104751202"></a>1.0.475.1202
릴리스 날짜: 2014년 12월

**새로운 기능:**

* 이제 특성 기반 필터링을 사용하는 암호 동기화가 지원됩니다. 자세한 내용은 [필터링으로 암호 동기화](active-directory-aadconnectsync-configure-filtering.md)를 참조하세요.
* msDS-ExternalDirectoryObjectID 특성이 다시 Active Directory에 기록됩니다. 이 기능을 통해 Office 365 응용 프로그램에 대한 지원이 추가됩니다. 이 기능은 OAuth2를 사용하여 하이브리드 Exchange 배포의 온라인 및 온-프레미스 사서함에 액세스합니다.

**수정된 업그레이드 문제:**

* 로그인 도우미의 새 버전을 서버에서 사용할 수 있습니다.
* 사용자 지정 설치 경로를 사용하여Azure AD Sync를 설치했습니다.
* 잘못된 사용자 지정 조인 조건이 업그레이드를 차단합니다.

**기타 수정 사항:**

* Office Pro Plus에 대한 템플릿이 수정되었습니다.
* 대시로 시작하는 사용자 이름에 의해 발생한 설치 문제가 수정되었습니다.
* 설치 마법사를 두 번째로 실행할 때 sourceAnchor 설정이 손실되는 문제가 수정되었습니다.
* 암호 동기화를 위한 ETW 추적이 수정되었습니다.

## <a name="104701023"></a>1.0.470.1023
릴리스 날짜: 2014년 10월

**새로운 기능:**

* 여러 온-프레미스 Active Directory에서 Azure AD로 암호 동기화
* 모든 Windows Server 언어로 설치 UI 현지화

**AADSync 1.0 GA에서 업그레이드**

Azure AD Sync가 이미 설치되어 있는 경우 기본적인 동기화 규칙을 하나라도 변경했다면 실행해야 하는 단계가 하나 더 있습니다. 1.0.470.1023 릴리스로 업그레이드한 후에는 수정한 동기화 규칙이 중복됩니다. 수정된 각 동기화 규칙에 대해 다음을 수행합니다.

1.  수정한 동기화 규칙을 찾아 변경 내용을 기록해둡니다.
* 해당 동기화 규칙을 삭제합니다.
* Azure AD Sync에서 생성한 새로운 동기화 규칙을 찾아 변경 내용을 다시 적용합니다.

**Active Directory 계정에 대한 권한**

Active Directory에서 암호 해시를 읽을 수 있으려면 Active Directory 계정에 추가 권한을 부여해야 합니다. 부여할 권한의 이름은 "디렉터리 변경 내용 복제" 및 “디렉터리 변경 내용 모두 복제”입니다. 두 권한이 모두 있어야 암호 해시를 읽을 수 있습니다.

## <a name="104190911"></a>1.0.419.0911
릴리스 날짜: 2014년 9월

**Azure AD Sync의 초기 릴리스**

## <a name="next-steps"></a>다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

