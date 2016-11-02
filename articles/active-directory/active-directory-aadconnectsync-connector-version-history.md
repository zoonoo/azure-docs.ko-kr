<properties
   pageTitle="커넥터 버전 릴리스 내역 | Microsoft Azure"
   description="이 항목에서는 FIM(Forefront Identity Manager) 및 MIM(Microsoft Identity Manager)에 대한 커넥터의 모든 버전을 보여 줍니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/17/2016"
   ms.author="billmath"/>


# <a name="connector-version-release-history"></a>커넥터 버전 릴리스 내역
FIM(Forefront Identity Manager) 및 MIM(Microsoft Identity Manager)의 커넥터는 자주 업데이트됩니다.

>[AZURE.NOTE]
이 항목은 FIM 및 MIM에만 있습니다. 이러한 커넥터는 Azure AD Connect에서 지원되지 않습니다.

이 항목은 출시된 커넥터의 모든 버전을 나열합니다.

관련 링크:

- [최신 커넥터 다운로드](http://go.microsoft.com/fwlink/?LinkId=717495)
- [일반 LDAP 커넥터](active-directory-aadconnectsync-connector-genericldap.md) 참조 설명서
- [일반 SQL 커넥터](active-directory-aadconnectsync-connector-genericsql.md) 참조 설명서
- [웹 서비스 커넥터](http://go.microsoft.com/fwlink/?LinkID=226245) 참조 설명서
- [PowerShell 커넥터](active-directory-aadconnectsync-connector-powershell.md) 참조 설명서
- [Lotus Domino 커넥터](active-directory-aadconnectsync-connector-domino.md) 참조 설명서

## <a name="1.1.117.0"></a>1.1.117.0
출시 날짜: 2016년 3월

**일반 SQL 커넥터**  
의 [일반 SQL 커넥터](active-directory-aadconnectsync-connector-genericsql.md)초기 릴리스입니다.

**새로운 기능:**

- 일반 LDAP 커넥터:
    - Isode에서 델타 가져오기에 대한 지원을 추가했습니다.
- 웹 서비스 커넥터:
    - 개체 수준 오류가 동기화 엔진으로 다시 반환될 수 있도록 csEntryChangeResult 작업 및 setImportErrorCode 작업을 업데이트했습니다.
    - 새 개체 수준 오류 기능을 사용하도록 SAP6 및 SAP6User 템플릿을 업데이트했습니다.
- Lotus Domino 커넥터:
    - 내보내는 경우 주소록당 하나의 인증자가 있어야 합니다. 이제 관리를 쉽게 수행할 수 있도록 모든 인증자에 대해 동일한 암호를 사용할 수 있습니다.

**수정된 문제:**

- 일반 LDAP 커넥터:
    - IBM Tivoli DS의 경우 일부 참조 특성이 제대로 검색되지 않았습니다.
    - 델타 가져오기 동안 열린 LDAP의 경우 문자열의 시작과 끝에 있는 공백이 잘렸습니다.
    - Novell과 NetIQ의 경우 OU/컨테이너 사이의 개체를 이동하고 동시에 개체의 이름도 바꾼 내보내기가 실패했습니다.
- 웹 서비스 커넥터:
    - 웹 서비스에 동일한 바인딩에 대한 여러 개의 끝점이 있으면 커넥터에서 이러한 끝점을 올바르게 검색하지 않았습니다.
- Lotus Domino 커넥터:
    - fullName 특성을 메일 내 데이터베이스에 내보내지 못했습니다.
    - 그룹에서 멤버를 추가하고 제거한 내보내기에서 추가된 멤버만 내보냈습니다.
    - Notes Document가 잘못된 경우(isValid 특성이 false로 설정) 커넥터가 실패합니다.

## <a name="older-releases"></a>이전 릴리스
2016년 3월 이전에 커넥터가 지원 항목으로 출시되었습니다.

**일반 LDAP**

- [KB3078617](https://support.microsoft.com/kb/3078617) - 1.0.0597, 2015년 9월
- [KB3044896](https://support.microsoft.com/kb/3044896) - 1.0.0549, 2015년 3월
- [KB3031009](https://support.microsoft.com/kb/3031009) - 1.0.0534, 2015년 1월
- [KB3008177](https://support.microsoft.com/kb/3008177) - 1.0.0419, 2014년 9월
- [KB2936070](https://support.microsoft.com/kb/2936070) - 4.3.1082, 2014년 3월

**WebServices**

- [KB3008178](https://support.microsoft.com/kb/3008178) - 1.0.0419, 2014년 9월

**PowerShell**

- [KB3008179](https://support.microsoft.com/kb/3008179) - 1.0.0419, 2014년 9월

**Lotus Domino**

- [KB3096533](https://support.microsoft.com/kb/3096533) - 1.0.0597, 2015년 9월
- [KB3044895](https://support.microsoft.com/kb/3044895) - 1.0.0549, 2015년 3월
- [KB2977286](https://support.microsoft.com/kb/2977286) - 5.3.0712, 2014년 8월
- [KB2932635](https://support.microsoft.com/kb/2932635) - 5.3.1003, 2014년 2월  
- [KB2899874](https://support.microsoft.com/kb/2899874) - 5.3.0721, 2013년 10월
- [KB2875551](https://support.microsoft.com/kb/2875551) - 5.3.0534, 2013년 8월

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](active-directory-aadconnectsync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](active-directory-aadconnect.md)에 대해 자세히 알아봅니다.



<!--HONumber=Oct16_HO2-->


