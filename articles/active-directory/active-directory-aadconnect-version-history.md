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
	ms.date="08/24/2015"
	ms.author="andkjell"/>

# Azure AD Connect: 버전 릴리스 내역

Azure Active Directory 팀은 새로운 기능과 성능으로 Azure AD Connect를 정기적으로 업데이트합니다. 모든 추가 내용이 모든 대상에 적용되는 것은 아닙니다.

이 문서는 릴리스된 버전을 추적하고 최신 버전으로 업데이트해야 하는지 여부를 파악할 수 있도록 도와줍니다.

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

## 1\.0.8641.0
릴리스 날짜: 2015년 6월

**Azure AD Connect의 초기 릴리스입니다.**

Azure AD Sync에서 Azure AD Connect로 이름을 변경했습니다.

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

- 이제 특성 기반 필터링으로 암호 동기화가 지원됩니다. 자세한 내용은 필터링으로 암호 동기화를 참조하세요.
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

## 추가 리소스
[Azure AD Connect Sync: 동기화 옵션 사용자 지정](active-directory-aadconnectsync-whatis.md)

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=August15_HO9-->