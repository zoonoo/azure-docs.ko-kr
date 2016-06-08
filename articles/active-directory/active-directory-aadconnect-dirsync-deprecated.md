<properties
	pageTitle="DirSync 및 Azure AD Sync에서 업그레이드 | Microsoft Azure"
	description="DirSync 및 Azure AD Sync에서 Azure AD Connect로 업그레이드하는 방법을 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="andkjell"/>


# Microsoft Azure Active Directory 동기화("DirSync") 및 Azure Active Directory 동기화("Azure AD Sync") 업그레이드
Azure AD Connect는 온-프레미스 디렉터리를 Azure AD와 Office 365에 연결하는 가장 좋은 방법입니다. 이제 이러한 도구가 사용되지 않으며 2017년 4월 13일에 지원이 종료될 예정이므로 Microsoft Azure Active Directory 동기화(DirSync) 또는 Azure AD Sync에서 Azure AD Connect로 지금 업그레이드하는 것이 좋습니다.

사용 중단되는 이 두 가지 ID 동기화 도구는 단일 포리스트 고객(DirSync)과 다중 포리스트 및 기타 고급 고객(Azure AD Sync)을 위해 제공되었습니다. 이러한 이전 도구는 모든 시나리오에 사용할 수 있는 단일 솔루션(Azure AD Connect)으로 대체되었습니다. 이 도구는 새로운 기능, 향상된 기능 및 새로운 시나리오에 대한 지원을 제공합니다. 온-프레미스 ID 데이터를 Azure AD 및 Office 365로 계속 동기화하려면 Azure AD Connect로 업그레이드하는 것이 좋습니다.

DirSync의 마지막 버전은 2014년 7월에 릴리스되었으며 Azure AD Sync의 마지막 버전은 2015년 5월에 릴리스되었습니다.

## Azure AD Connect의 정의
Azure AD Connect는 DirSync 및 Azure AD Sync의 후속 도구로서, 이 두 도구가 지원하는 모든 시나리오를 통합합니다. 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에서 확인할 수 있습니다.

## 사용 중단 일정

Date | 주석
 --- | ---
2016년 4월 13일 | Microsoft Azure Active Directory 동기화("DirSync") 및 Azure Active Directory 동기화("Azure AD Sync")가 사용 중단될 예정입니다.
2017년 4월 13일 | 지원이 종료됩니다. 이제 고객은 Azure AD Connect로 업그레이드해야만 지원 사례를 개설할 수 없습니다.

## Azure AD Connect로 전환하는 방법
DirSync를 실행 중인 경우 전체 업그레이드와 병렬 배포의 두 가지 방법으로 업그레이드할 수 있습니다. 전체 업그레이드는 대부분의 고객에게 권장되며, 최신 운영 체제가 설치되어 있고 개체 수가 50,000개 미만인 경우에 사용하는 것이 좋습니다. 그렇지 않은 경우에는 DirSync 구성이 Azure AD Connect를 실행하는 새 서버로 전환되는 병렬 배포를 수행하는 것이 좋습니다.

Azure AD Sync를 사용하는 경우에는 전체 업그레이드가 권장됩니다. 필요한 경우 새 Azure AD Connect 서버를 병렬로 설치하고 Azure AD Sync 서버에서 Azure AD Connect로 스윙 마이그레이션을 수행할 수 있습니다.

해결 방법 | 시나리오
----- | -----
[DirSync에서 업그레이드](active-directory-aadconnect-dirsync-upgrade-get-started.md) | <li>기존 DirSync 서버를 이미 실행 중인 경우입니다.</li>
[Azure AD Sync에서 업그레이드](active-directory-aadconnect-upgrade-previous-version.md)| <li>Azure AD Sync에서 전환하는 경우</li>

DirSync에서 Azure AD Connect로 전체 업그레이드를 수행하는 방법은 이 Channel 9 동영상을 참조하세요.

> [AZURE.VIDEO azure-active-directory-connect-in-place-upgrade-from-legacy-tools]

## FAQ
**Q: Azure 팀에서 메일 알림을 받았거나 Office 365 메시지 센터에서 메시지를 받았지만 Connect를 사용하고 있습니다.** 빌드 번호 1.0.*.0(사전 1.1 릴리스 사용)의 Azure AD Connect를 사용하는 고객에게도 알림을 보냈습니다. 고객은 Azure AD Connect 릴리스를 최신 상태로 유지하는 것이 좋습니다. 1.1에서 [자동 업그레이드](active-directory-aadconnect-feature-automatic-upgrade.md) 기능은 Azure AD Connect가 항상 최신 버전으로 설치되도록 합니다.

**Q: DirSync/Azure AD Sync의 작동이 2017년 4월 13일에 중지됩니까?** 아니요. Azure AD와 더 이상 통신할 수 없는 날짜는 이후에 발표될 예정입니다. 사용 가능한 경우 이 항목에서 해당 정보를 찾을 수 있습니다.

**Q: 어떤 DirSync 버전에서 업그레이드할 수 있나요?** 현재 사용 중인 모든 DirSync 릴리스의 업그레이드를 지원합니다.

**Q: FIM/MIM용 Azure AD 커넥터는 어떻게 됩니까?** FIM/MIM용 Azure AD 커넥터는 사용 중단되는 것으로 발표되지 **않았습니다**. 현재 **기능 동결** 상태입니다. 즉, 새 기능이 추가되지 않으며 버그 수정이 제공되지 않습니다. 고객은 Azure AD Connect로 전환할 계획을 수립하고, 이 커넥터를 사용하여 새 배포를 시작하지 않는 것이 좋습니다. 이 커넥터는 향후에 사용 중단될 예정입니다.

## 추가 리소스

* [Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)

<!---HONumber=AcomDC_0525_2016-->