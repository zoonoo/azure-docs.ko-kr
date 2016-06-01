<properties
   pageTitle="Azure AD Connect: 버전 릴리스 내역 | Microsoft Azure"
   description="이 항목에서는 Azure AD Connect 및 Azure AD Sync의 모든 릴리스를 나열합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="05/11/2016"
   ms.author="andkjell"/>

# Azure AD Connect: 버전 릴리스 내역

Azure Active Directory 팀은 새로운 기능과 성능으로 Azure AD Connect를 정기적으로 업데이트합니다. 모든 추가 내용이 모든 대상에 적용되는 것은 아닙니다.

이 문서는 릴리스된 버전을 추적하고 최신 버전으로 업데이트해야 하는지 여부를 파악할 수 있도록 도와줍니다.

다음은 관련된 항목의 목록입니다.

| 항목 | |
| --------- | --------- |
| Azure AD Connect에서 업그레이드하는 단계 | Azure AD Connect 릴리스를 [이전 버전에서 최신 버전으로 업그레이드](active-directory-aadconnect-upgrade-previous-version.md)하는 다른 방법입니다. |
| 필요한 사용 권한 | 업데이트를 적용하는 데 필요한 사용 권한은 [계정 및 사용 권한](active-directory-aadconnect-accounts-permissions.md#upgrade)을 참조하세요. |
| 다운로드| [Azure AD Connect 다운로드](http://go.microsoft.com/fwlink/?LinkId=615771) |

## 1\.1.180.0
릴리스 날짜: 2016년 5월

**새로운 기능:**

- Azure AD Connect를 실행하기 전에 수행하지 않은 경우 경고를 표시하고 도메인을 확인할 수 있습니다.
- [Microsoft 클라우드 독일](active-directory-aadconnect-instances.md#microsoft-cloud-germany)에 대한 지원을 추가되었습니다.
- 새 URL 요구 사항을 포함한 최신 [Microsoft Azure Government 클라우드](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) 인프라에 대한 지원을 추가했습니다.

**수정된 문제 및 향상된 기능:**

- 쉽게 동기화 규칙을 찾을 수 있도록 동기화 규칙 편집기에 필터링을 추가했습니다.
- 커넥터 공간을 삭제할 경우 성능이 향상되었습니다.
- 동일한 개체가 동일한 실행에서 모두 삭제되고 추가되는 경우(삭제/추가라고 함) 발생하는 문제가 수정되었습니다.
- 사용할 수 없는 동기화 규칙은 업그레이드 또는 디렉터리 스키마 새로 고침에 포함된 개체 및 특성을 더 이상 다시 사용하지 않습니다.

## 1\.1.130.0
릴리스 날짜: 2016년 4월

**새로운 기능:**

- 다중 값 특성에 대한 지원이 [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)에 추가되었습니다.
- 업그레이드 가능한 것으로 간주되는 [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md)의 자세한 구성 변형에 대한 지원이 추가되었습니다.
- [사용자 지정 스케줄러](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler)에 대한 일부 cmdlet이 추가되었습니다.

## 1\.1.119.0
출시 날짜: 2016년 3월

**수정된 문제:**

- Windows Server 2008(R2 이전 버전)에는 암호 동기화가 지원되지 않기 때문에 이 운영 체제에서는 빠른 설치를 사용할 수 없습니다.
- 사용자 지정 필터가 구성된 Dirsync에서 업그레이드가 예상대로 작동하지 않습니다.
- 새 릴리스로 업그레이드하고 구성이 변경되지 않은 경우 전체 가져오기/동기화를 예약하지 않아야 합니다.

## 1\.1.110.0
발표 날짜: 2016년 2월

**수정된 문제:**

- 설치 파일이 기본 **C:\\Program Files** 폴더에 없는 경우 이전 릴리스에서 업그레이드되지 않습니다.
- 설치하고 설치 마법사의 마지막에서 **동기화 프로세스 시작..**을 선택 취소하는 경우 설치 마법사를 다시 실행해도 스케줄러를 사용할 수 없습니다.
- 스케줄러는 날짜/시간 형식이 US-en이 아닌 서버에서는 작동하지 않습니다. 또한 올바른 시간을 반환하도록 `Get-ADSyncScheduler`도 차단합니다.
- 로그인 옵션으로 ADFS를 사용하는 Azure AD Connect의 이전 릴리스를 설치했고 업그레이드하는 경우 설치 마법사를 다시 실행할 수 없습니다.

## 1\.1.105.0
발표 날짜: 2016년 2월

**새로운 기능:**

- Express 설정 고객용 [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md) 기능
- 설치 마법사에서 MFA 및 PIM을 사용하여 전역 관리자 지원
    - MFA를 사용하는 경우 https://secure.aadcdn.microsoftonline-p.com에 트래픽을 허용하려면 프록시도 허용해야 합니다.
    - MFA가 올바르게 작동하려면 신뢰할 수 있는 사이트 목록에 https://secure.aadcdn.microsoftonline-p.com을 추가해야 합니다.
- 초기 설치 후 사용자의 로그인 방법 변경 허용
- 설치 마법사에서 [도메인 및 OU 필터링](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) 허용. 이는 일부 도메인을 사용할 수 없는 포리스트로의 연결도 허용합니다.
- 동기화 엔진에 [스케줄러](active-directory-aadconnectsync-feature-scheduler.md) 기본 제공

**미리 보기에서 GA로 승격되는 기능:**

- [장치 쓰기 저장](active-directory-aadconnect-feature-device-writeback.md)
- [디렉터리 확장](active-directory-aadconnectsync-feature-directory-extensions.md)

**새로운 미리 보기 기능:**

- 새 기본 동기화 주기 간격은 30분입니다. 이전 모든 릴리스에서는 3시간이었습니다. [스케줄러](active-directory-aadconnectsync-feature-scheduler.md) 동작 변경 지원이 추가됩니다.

**수정된 문제:**

- DNS 도메인 확인 페이지에서 도메인을 항상 인식하지 않았습니다.
- ADFS를 구성할 때 도메인 관리자 자격 증명을 묻는 메시지가 표시됩니다.
- 온-프레미스 AD 계정이 루트 도메인이 아닌 다른 DNS 트리를 사용하는 도메인에 있는 경우 설치 마법사에서 인식하지 못합니다.

## 1\.0.9131.0
릴리스 날짜: 2015년 12월

**수정된 문제:**

- 암호 동기화는 암호를 설정할 때는 작동하지만 AD DS에서 암호를 변경하면 작동하지 않을 수 있습니다.
- 프록시 서버를 보유한 경우 구성 페이지에서 설치 또는 업그레이드하는 동안 Azure AD에 대한 인증이 실패할 수 있습니다.
- 전체 SQL Server를 사용하여 Azure AD Connect의 이전 릴리스에서 업데이트를 하면 SQL에 SA가 없는 경우 실패합니다.
- 원격 SQL Server를 사용하여 Azure AD Connect의 이전 릴리스에서 업데이트를 하면 "ADSync SQL 데이터베이스에 액세스할 수 없습니다."라는 오류가 표시됩니다.

## 1\.0.9125.0
릴리스 날짜: 2015년 11월

**새로운 기능:**

- ADFS와 Azure AD 간의 트러스트를 다시 구성할 수 있습니다.
- Active Directory 스키마를 새로 고치고 동기화 규칙을 다시 생성할 수 있습니다.
- 동기화 규칙을 사용하지 않도록 설정할 수 있습니다.
- 동기화 규칙에서 새 리터럴로 "AuthoritativeNull"을 정의할 수 있습니다.

**새로운 미리 보기 기능:**

- [동기화를 위한 Azure AD Connect Health](active-directory-aadconnect-health-sync.md).
- [Azure AD 도메인 서비스](active-directory-get-started.md) 암호 동기화를 지원합니다.

**지원되는 새 시나리오:**

- 여러 온-프레미스 Exchange 조직을 지원합니다. 자세한 내용은 [여러 Active Directory 포리스트가 있는 하이브리드 배포](https://technet.microsoft.com/library/jj873754.aspx)를 참조하세요.

**수정된 문제:**

- 암호 동기화 문제:
    - 범위 외부에서 범위 내부로 이동된 개체는 해당 암호가 동기화되지 않습니다. 여기에는 OU와 특성 필터링이 모두 포함됩니다.
    - 동기화에 포함할 새 OU를 선택하는 데에는 전체 암호 동기화가 필요하지 않습니다.
    - 비활성화된 사용자가 설정된 경우 암호가 동기화되지 않습니다.
    - 암호 다시 시도 큐에는 제한이 없으며, 사용 중지될 개체 5, 000개에 대한 이전의 제한이 제거되었습니다.
    - [개선된 문제 해결](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Windows Server 2016 포리스트 기능 수준으로 Active Directory에 연결할 수 없습니다.
- 초기 설치 후 그룹 필터링에 사용된 그룹을 변경할 수 없습니다.
- 비밀번호 쓰기 저장을 사용하도록 설정한 경우 암호 변경을 수행하는 모든 사용자에 대해 새 사용자 프로필을 Azure AD Connect 서버에 더 이상 만들 수 없습니다.
- 동기화 규칙 범위 내 정수(Long) 값을 사용할 수 없습니다.
- 연결할 수 없는 도메인 컨트롤러가 있는 경우 "장치 쓰기 저장" 확인란을 비활성화된 상태로 유지합니다.

## 1\.0.8667.0
릴리스 날짜: 2015년 8월

**새로운 기능:**

- Azure AD Connect 설치 마법사가 이제 모든 Windows Server 언어로 현지화되었습니다.
- Azure AD 암호 관리 사용 시 계정 잠금 해제에 대한 지원이 추가되었습니다.

**수정된 문제:**

- 설치를 처음 시작한 사용자가 아닌 다른 사용자가 설치를 계속하면 Azure AD Connect 설치 마법사가 충돌합니다.
- 이전에 Azure AD Connect를 제거할 때 Azure AD Connect Sync가 완전히 제거되지 않았다면 다시 설치할 수 없습니다.
- 사용자가 포리스트 루트 도메인에 없거나 Active Directory의 비영어 버전을 사용할 때 Express 설치를 사용하여 Azure AD Connect를 설치할 수 없습니다.
- Active Directory 사용자 계정의 FQDN을 확인할 수 없을 때 "스키마 커밋 실패"라는 잘못된 오류 메시지가 표시됩니다.
- Active Directory Connector에 사용되는 계정이 마법사 밖에서 변경되면 마법사가 후속 실행에 실패합니다.
- Azure AD Connect가 도메인 컨트롤러에서 설치에 실패하는 경우가 있습니다.
- 확장 특성을 추가하지 않은 경우 “스테이징 모드"를 활성화 및 비활성화할 수 없습니다.
- Active Directory Connector에 잘못된 암호 때문에 일부 구성에서 암호 쓰기 저장이 실패합니다.
- 특성 필터링에 dn이 사용되는 경우 DirSync를 업그레이드할 수 없습니다.
- 암호 재설정을 사용할 경우 CPU가 과도하게 사용됩니다.

**제거된 미리 보기 기능:**

- 미리 보기 기능 [사용자 쓰기 저장](active-directory-aadconnect-feature-preview.md#user-writeback)은 미리 보기 고객의 의견에 따라 일시적으로 제거되었습니다. 나중에 제공된 의견을 해결하면 다시 추가될 예정입니다.

## 1\.0.8641.0
릴리스 날짜: 2015년 6월

**Azure AD Connect의 초기 릴리스입니다.**

Azure AD Sync에서 Azure AD Connect로 이름을 변경했습니다.

**새로운 기능:**

- [Express 설정](active-directory-aadconnect-get-started-express.md) 설치
- [ADFS 구성](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 가능
- [DirSync에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md) 가능
- [실수로 인한 삭제 방지](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- [스테이징 모드](active-directory-aadconnectsync-operations.md#staging-mode) 도입

**새로운 미리 보기 기능:**

- [사용자 쓰기 저장](active-directory-aadconnect-feature-preview.md#user-writeback)
- [그룹 쓰기 저장](active-directory-aadconnect-feature-preview.md#group-writeback)
- [장치 쓰기 저장](active-directory-aadconnect-feature-device-writeback.md)
- [디렉터리 확장](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
릴리스 날짜: 2015년 5월

**새로운 요구 사항:**

- Azure AD Sync는 이제 .Net framework 버전 4.5.1을 설치해야 합니다.

**수정된 문제:**

- servicebus 연결 오류와 함께 Azure AD에서 암호 쓰기 저장이 실패했습니다.

## 1\.0.491.0413
릴리스 날짜: 2015년 4월

**수정된 문제 및 향상된 기능:**

- 휴지통이 활성화되어 있고 포리스트에 여러 도메인이 있는 경우 Active Directory Connect가 삭제를 올바르게 처리하지 못했습니다.
- Azure Active Directory Connector에 대해 가져오기 작업의 성능이 향상되었습니다.
- 그룹의 구성원 제한이 초과되면(기본적으로 50,000개의 개체로 설정됨) Azure Active Directory에서 해당 그룹이 삭제되었습니다. 새 동작에서는 그룹이 남아 있으며 오류가 발생되며 새로운 멤버 자격 변경 내용이 내보내기됩니다.
- 동일한 DN을 가진 단계적 삭제가 이미 커넥터 공간에 있으면 새 개체를 프로비저닝할 수 없습니다.
- 개체에 대해 변경 사항이 없는데도 일부 개체가 델타 동기화 중에 동기화되는 것으로 표시됩니다.
- 강제로 암호를 동기화해도 기본 설정된 DC 목록이 제거됩니다.
- CSExportAnalyzer에 일부 개체 상태에 문제가 있습니다.

**새로운 기능:**

- 이제 조인이 MV에 있는 "ANY" 개체 유형에 연결할 수 있습니다.

## 1\.0.485.0222
릴리스 날짜: 2015년 2월

**향상된 기능:**

- 가져오기 성능이 향상되었습니다.

**수정된 문제:**

- 암호 동기화는 특성 필터링에 의해 사용되는 cloudFiltered 특성을 인식합니다. 필터링된 개체는 더 이상 암호 동기화 범위에 속하지 않습니다.
- 드문 경우지만 토폴로지에 너무 많은 도메인 컨트롤러가 있어서 암호 동기화가 작동하지 않습니다.
- Azure AD/Intune에서 장치 관리가 활성화된 후 Azure AD Connector로부터 가져오기 시 “중지된-서버"가 발생합니다.
- 동일한 포리스트 내 여러 도메인으로부터 외부 보안 주체(FSP)를 조인하면 모호한 오류가 발생합니다.

## 1\.0.475.1202
릴리스 날짜: 2014년 12월

**새로운 기능:**

- 이제 특성 기반 필터링으로 암호 동기화가 지원됩니다. 자세한 내용은 [필터링으로 암호 동기화](active-directory-aadconnectsync-configure-filtering.md)를 참조하세요.
- 특성 msDS-ExternalDirectoryObjectID가 AD로 다시 기록됩니다. 그러면 OAuth2를 사용하는 Office 365 응용 프로그램이 Hybrid Exchange Deployment 내의 온라인 및 온-프레미스 사서함 모두에 액세스할 수 있게 됩니다.

**수정된 업그레이드 문제:**

- 로그인 도우미의 새 버전을 서버에서 사용할 수 있습니다.
- 사용자 지정 설치 경로를 사용하여Azure AD Sync를 설치했습니다.
- 잘못된 사용자 지정 조인 조건이 업그레이드를 차단합니다.

**기타 수정 사항:**

- Office Pro Plus에 대한 템플릿이 수정되었습니다.
- 대시로 시작하는 사용자 이름에 의해 발생한 설치 문제가 수정되었습니다.
- 설치 마법사를 두 번째로 실행할 때 sourceAnchor 설정이 손실되는 문제가 수정되었습니다.
- 암호 동기화를 위한 ETW 추적이 수정되었습니다.

## 1\.0.470.1023
릴리스 날짜: 2014 10월

**새로운 기능:**

- 여러 온-프레미스 AD에서 Azure AD로 암호 동기화
- 모든 Windows Server 언어로 설치 UI 현지화

**AADSync 1.0 GA에서 업그레이드**

Azure AD Sync가 이미 설치되어 있는 경우 기본적인 동기화 규칙을 하나라도 변경했다면 실행해야 하는 단계가 하나 더 있습니다. 1.0.470.1023 릴리스로 업그레이드한 후에는 수정한 동기화 규칙이 중복됩니다. 수정된 각 동기화 규칙에 대해 다음을 수행합니다.

- 수정한 동기화 규칙을 찾아 변경 내용을 기록해둡니다.
- 해당 동기화 규칙을 삭제합니다.
- Azure AD Sync에서 생성한 새로운 동기화 규칙을 찾아 변경 내용을 다시 적용합니다.

**AD 계정에 대한 사용 권한**

AD로부터 암호 해시를 읽을 수 있으려면 AD 계정에 추가 권한을 부여해야 합니다. 부여할 사용 권한의 이름은 "디렉터리 변경 내용 복제" 및 "디렉터리 변경 내용 모두 복제”입니다. 두 권한이 모두 있어야 암호 해시를 읽을 수 있습니다.

## 1\.0.419.0911
릴리스 날짜: 2014년 9월

**Azure AD Sync의 초기 릴리스**

## 다음 단계
[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.

<!---HONumber=AcomDC_0518_2016-->