<properties
	pageTitle="Azure Active Directory Reporting 검색"
	description="Azure Active Directory의 보안, 작업 및 감사 보고서를 검색하는 방법"
	services="active-directory"
	documentationCenter=""
	authors="kenhoff"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="07/07/2015"
	ms.author="kenhoff"/>

# Azure Active Directory Reporting 검색

Azure Active Directory는 디렉터리 관리자에게 여러 보고서에서 사용자 보안, 작업 및 감사 이벤트를 검색하는 기능을 제공합니다.

검색 패널을 찾으려면 **Azure 관리 포털 -> 사용자의 Azure Active Directory -> 보고서**로 이동합니다. 패널은 보고서 목록 위쪽에 있습니다.

특정 사용자에 대한 작업이나 감사 이벤트를 검색하려면 시작 및 끝 필드에서 날짜 범위를 선택하고, 사용자의 UPN 또는 표시 이름에 입력하고, 확인 표시 단추를 클릭합니다.

## 검색에 포함된 보고서

일부 보고서가 아직 검색 결과에 포함되지 않았습니다. 다음 표는 포함된 보고서를 나타냅니다.

|	보고서 |	포함됨 |
|	------												|	--------			|
|	알 수 없는 원본에서 로그인 |	아니요 |
|	여러 번의 실패 후 로그인 |	아니요 |
|	여러 지역에서의 로그인 |	아니요 |
|	의심스러운 활동을 포함하는 IP 주소의 로그인 |	아니요 |
|	감염 가능성이 있는 장치에서의 로그인 |	아니요 |
|	비정상적인 로그인 활동 |	아니요 |
|	비정상적인 로그인 활동을 포함하는 사용자 보고서 |	아니요 |
|	자격 증명이 손실된 사용자 |	아니요 |
|	감사 보고서 |	예 |
|	암호 재설정 활동 |	예 |
|	암호 재설정 등록 활동 |	예 |
|	셀프 서비스 그룹 활동 |	예 |
|	응용 프로그램 사용 현황 |	아니요 |
|	계정 프로비저닝 활동 |	예 |
|	암호 롤오버 상태 |	아니요 |
|	계정 프로비전 오류 |	아니요 |
|	RMS 사용 현황 |	아니요 |
|	가장 활동적인 RMS 사용자 |	아니요 |
|	RMS 장치 사용 현황 |	아니요 |

## 자세한 정보

 - [Azure Active Directory 보고서](active-directory-view-access-usage-reports.md)
 - [Azure Active Directory Reporting 감사 이벤트](active-directory-reporting-audit-events.md)

<!---HONumber=July15_HO3-->