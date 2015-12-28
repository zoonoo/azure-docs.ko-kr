<properties
   pageTitle="Azure AD Connect 동기화: 일반 LDAP 커넥터"
   description="이 문서에서는 Microsoft의 일반 LDAP 커넥터를 구성하는 방법을 설명합니다."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="12/16/2015"
   ms.author="andkjell"/>

# 일반 LDAP 커넥터 기술 참조

이 문서에서는 일반 LDAP 커넥터를 설명합니다. 이 문서는 다음 제품에 적용됩니다.

- Microsoft Identity Manager 2016 (MIM2016)
- Forefront Identity Manager 2010 R2 (FIM2010R2)
    -   핫픽스 4.1.3461.0 이상 [KB2870703](https://support.microsoft.com/kb/2870703)을 사용해야 합니다.

MIM2016와 FIM2010R2의 경우 커넥터는 [Microsoft 다운로드 센터](http://go.microsoft.com/fwlink/?LinkId=717495)에서 다운로드로 사용할 수 있습니다.

IETF RFC를 참조할 때 이 문서는 (RFC [RFC number]/[section in RFC document])형식을 사용합니다. 예: (RFC 4512/4.3). http://tools.ietf.org/html/rfc4500에서 더 많은 정보를 찾을 수 있습니다.(올바른 RFC 번호로 4500을 바꿔야 합니다)

## 일반 LDAP 커넥터의 개요

일반 LDAP 커넥터를 사용하면 LDAP v3 서버를 통해 동기화 서비스를 통합할 수 있습니다.

델타 가져오기를 수행하는 데 필요한 것과 같은 특정 작업 및 스키마 요소는 IETF RFC에 지정되지 않습니다. 이러한 작업의 경우 명시적으로 지정된 LDAP 디렉터리만 지원됩니다.

전체적인 관점에서 보면 커넥터의 현재 릴리스에서 다음과 같은 기능이 지원됩니다.

| 기능 | 지원 |
| --- | --- |
| 연결된 데이터 원본 | 이 커넥터는 모든 LDAP v3 서버(RFC 4510 규격)를 통해 지원됩니다. 다음으로 테스트되었습니다: <li>Microsoft Active Directory Lightweight Directory Services (AD LDS)</li><li>Microsoft Active Directory 글로벌 카탈로그(AD GC)</li><li>389 디렉터리 서버</li><li>Apache 디렉터리 서버</li><li>IBM Tivoli DS</li><li>Isode 디렉터리</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>DJ 열기</li><li>DS 열기</li><li>LDAP 열기(openldap.org)</li><li>Oracle (이전에 Sun) 디렉터리 서버 Enterprise Edition</li><li>RadiantOne 가상 디렉터리 서버(VDS)</li><li>Sun One 디렉터리 서버</li>주목할 만한 디렉터리가 지원되지 않음: <li>Microsoft Active Directory 도메인 서비스(AD DS) [대신 기본 제공 Active Directory 커넥터 사용]</li><li>Oracle Internet Directory(OID)</li> |
| 시나리오 | <li>개체 수명 주기 관리</li><li>그룹 관리</li><li>암호 관리</li> |
| 작업 |모든 LDAP 디렉터리에서 다음 작업이 지원됩니다: <li>전체 가져오기</li><li>내보내기</li>다음 작업은 지정된 디렉터리에서만 지원됩니다:<li>델타 가져오기</li><li>암호 설정, 암호 변경</li> |
| 스키마 | <li>스키마는 LDAP 스키마에서 검색됩니다(RFC3673 및 RFC4512/4.2)</li><li>구조 클래스, aux 클래스 및 extensibleObject 개체 클래스를 지원합니다(RFC4512/4.3)</li>

### 델타 가져오기 및 암호 관리 지원

델타 가져오기 및 암호 관리에 지원되는 디렉터리:

- Microsoft Active Directory Lightweight Directory Services(AD LDS)
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원
- Microsoft Active Directory 글로벌 카탈로그(AD GC)
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원
- 389 디렉터리 서버
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 지원
- Apache 디렉터리 서버
    - 이 디렉터리에 영구 변경 로그가 없으므로 델타 가져오기를 지원하지 않습니다.
    - 암호 설정 지원
- IBM Tivoli DS
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원
- Isode 디렉터리
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원
- Novell eDirectory 및 NetIQ eDirectory
    - 델타 가져오기에 대한 추가, 업데이트 및 이름 바꾸기 작업을 지원합니다.
    - 델타 가져오기에 대한 삭제 작업을 지원하지 않습니다.
    - 암호 설정 및 암호 변경 지원
- DJ 열기
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 지원
- DS 열기
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 지원
- LDAP 열기(openldap.org)
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 지원
    - 암호 변경을 지원하지 않습니다.
- Oracle(이전에 Sun) 디렉터리 서버 Enterprise Edition
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원
- RadiantOne 가상 디렉터리 서버(VDS)
    - 버전 7.1.1 이상을 사용해야 합니다.
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원
-  Sun One 디렉터리 서버
    - 델타 가져오기에 모든 작업을 지원합니다.
    - 암호 설정 및 암호 변경 지원

### 필수 조건

커넥터를 사용하기 전에 위에서 언급 한 핫픽스 외에도 동기화 서버에 다음 사항이 있는지 확인합니다.

- Microsoft.NET 4.5.2 Framework 이상

### LDAP 서버 검색

커넥터는 LDAP 서버를 검색하고 식별하는 다양한 기술을 사용합니다. 커넥터는 루트 DSE를 사용하여 공급 업체 이름 및 버전을 찾고 특정 LDAP 서버에 있다고 알려진 고유 개체 및 특성을 찾을 스키마를 검사합니다. 이 데이터가 발견되면 커넥터에서 구성 옵션을 미리 채우는 데 사용됩니다.

### 연결된 데이터 원본 사용 권한

연결된 디렉터리에서 개체에 작업을 가져오고 내보내도록 수행하려면 커넥터 계정에 충분한 권한이 있어야 합니다. 커넥터가 내보낼 수 있는 쓰기 권한 및 가져올 수 있는 읽기 권한이 필요합니다. 사용 권한 구성은 대상 디렉터리 자체의 관리 환경 내에서 수행됩니다.

### 포트 및 프로토콜

커넥터는 구성에 지정된 포트 번호를 사용하며 이는 기본적으로 LDAP에 389 및 LDAPS에 636을 사용합니다.

LDAPS의 경우 SSL 3.0 또는 TLS를 사용해야 합니다. SSL 2.0은 지원되지 않으며 활성화될 수 없습니다.

### 필수 제어 및 기능

커넥터가 제대로 작동하려면 다음 LDAP 제어/기능은 LDAP 서버에 사용할 수 있어야 합니다.

- 1\.3.6.1.4.1.4203.1.5.3 True/False 필터

True/False 필터는 LDAP 디렉터리에서 지원하는 만큼 자주 보고되지 않고 **필수 기능을 찾을 수 없습니다.**의 **전역 페이지**에서 보여줍니다. 예를 들어 여러 개체 형식을 가져올 때 LDAP 쿼리에 **OR** 필터를 만드는 데 사용됩니다. 하나 이상의 개체 형식을 가져올 수 있으면 LDAP 서버가 지원합니다.

고유 식별자가 앵커인 디렉터리를 사용하는 경우 다음에서도 사용할 수 있어야 합니다.(자세한 내용은 이 문서의 뒷부분에 나오는 [앵커 구성](#configure-anchors) 섹션을 참조하세요)

- 1\.3.6.1.4.1.4203.1.5.1 모든 작업 특성

디렉터리가 디렉터리에 한 번 호출에 들어갈 수 있는 것보다 더 많은 개체를 가진 경우 페이징을 사용하는 것이 좋습니다. 작업을 페이징할 경우 다음 옵션 중 하나가 필요합니다.

**옵션 1:**

- 1\.2.840.113556.1.4.319 pagedResultsControl

**옵션 2:**

- 2\.16.840.1.113730.3.4.9 VLVControl
- 1\.2.840.113556.1.4.473 SortControl

두 옵션 모두 커넥터 구성에서 사용하도록 설정된 경우 pagedResultsControl이 사용됩니다.

- 1\.2.840.113556.1.4.417 ShowDeletedControl

ShowDeletedControl은 삭제된 개체를 볼 수 있는 USNChanged 델타 가져오기 메서드와 함께 사용됩니다.

커넥터는 옵션이 서버에 있는 경우 검색하려고 시도합니다. 옵션을 검색할 수 없는 경우 경고는 커넥터의 속성에 있는 전역 페이지에 표시됩니다. 모든 LDAP 서버가 지원하는 모든 제어/기능을 나타내지는 않습니다. 경고가 나타나더라도 커넥터는 문제 없이 작동할 수 있습니다.

### 델타 가져오기

델타 가져오기는 지원 디렉터리를 감지한 때 사용할 수 있습니다. 다음과 같은 메서드가 현재 사용됩니다.

- LDAP Accesslog. [http://www.openldap.org/doc/admin24/overlays.html#Access Logging](http://www.openldap.org/doc/admin24/overlays.html#Access 로깅)을 참조하세요.
- LDAP Changelog. [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)을 참조하세요.
- TimeStamp. Novell/NetIQ eDirectory의 경우 커넥터는 마지막 날짜/시간을 사용하여 개체를 생성 및 업데이트합니다. Novell/NetIQ eDirectory는 삭제된 개체를 검색하는 동등한 도구를 제공하지 않습니다. 다른 델타 가져오기 메서드가 LDAP 서버에서 활성화되지 않으면 이 옵션을 사용할 수도 있습니다. 이 옵션은 삭제된 개체를 가져올 수 없습니다.
- USNChanged. [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)를 참조하세요.

### 지원되지 않음

다음 LDAP 기능은 원되지 않습니다.

- 서버 간 LDAP 조회(RFC 4511/4.1.10)

## 새 커넥터 만들기

일반 LDAP을 만들려면 **동기화 서비스**에서 **관리 에이전트** 및 **만들기**를 선택합니다. **일반 LDAP(Microsoft)** 커넥터를 선택합니다.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### 연결

연결 페이지에서 호스트, 포트 및 바인딩 정보를 지정해야 합니다. 바인딩이 선택되는지에 따라 추가 정보는 다음 섹션에 제공될 수도 있습니다.

![연결](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- 스키마를 검색하는 경우 연결 제한 시간 설정은 첫 번째 연결 서버에만 사용됩니다.
- 바인딩이 익명인 경우 사용자 이름/암호 또는 인증서 어느 것도 사용할 수 없습니다.
- 다른 바인딩의 경우 사용자 이름/암호 중 하나에 정보를 입력하거나 인증서를 선택합니다.
- Kerberos를 사용하여 인증하는 경우 사용자의 영역/도메인을 제공합니다.

**특성 별칭** 텍스트 상자는 RFC4522 구문을 사용하여 스키마에 정의된 특성에 사용됩니다. 이러한 사항은 스키마 검색하는 동안 검색할 수 없고 커넥터는 해당 항목을 정의하기 위해 도움이 필요합니다. 예를 들어 다음은 특성 별칭 상자에 입력하여 userCertificate 특성을 이진 특성으로 올바르게 식별하는 데 필요합니다.

`userCertificate;binary`

다음은 이를 보여주는 예제입니다.

![연결](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

**스키마에서 작업 특성 포함** 확인란을 선택하여 서버에서 만든 특성을 포함합니다. 여기에는 개체를 만든 시점과 마지막 업데이트 시간 등의 특성을 포함합니다.

확장 가능한 개체(RFC4512/4.3)를 사용하고 이 옵션을 사용하면 모든 개체에 사용될 수 있는 모든 특성을 허용하는 경우 **스키마에 확장 가능한 특성 포함**을 선택합니다. 이 옵션을 선택하면 스키마가 매우 커지므로 연결된 디렉터리가 이 기능을 사용하지 않는 한 옵션을 선택하지 않는 것이 좋습니다.

### 글로벌 매개 변수

글로벌 매개 변수 페이지에서 델타 변경 로그 및 추가 LDAP 기능에 DN을 구성합니다. 페이지는 LDAP 서버에서 제공하는 정보로 미리 채워집니다.

![연결](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

맨 위 섹션에는 서버 이름과 같은 서버 자체에서 제공하는 정보가 표시됩니다. 또한 커넥터는 필수 제어가 루트 DSE에 있는지 확인합니다. 이러한 항목이 나열되지 않으면 경고가 표시됩니다. 일부 LDAP 디렉터리는 루트 DSE의 모든 기능을 나열하지는 않고 경고가 있는 경우에 커넥터가 문제 없이 작동할 수 있습니다.

**지원되는 제어** 확인란은 특정 작업에 대한 동작을 제어합니다.

- 트리 삭제를 선택한 경우 계층은 LDAP 호출을 사용하여 삭제됩니다. 트리 삭제의 선택을 취소한 경우 필요하면 커넥터는 재귀 삭제를 수행합니다.
- 선택한 페이지 결과를 사용하여 커넥터는 실행 단계에 지정된 크기로 페이징된 가져오기를 수행합니다.
- VLVControl 및 SortControl은 pagedResultsControl의 대안으로 LDAP 디렉터리에서 데이터를 읽습니다.
- 세 가지 옵션(pagedResultsControl, VLVControl, 및 SortControl)이 모두 선택되지 않으면 커넥터는 하나의 작업으로 모든 개체를 가져오며 이는 큰 디렉터리인 경우 실패할 수 있습니다.
- ShowDeletedControl은 델타 가져오기 메서드가 USNChanged일 때 사용됩니다.

변경 로그 DN은 델타 변경 로그에서 사용하는 명명 컨텍스트입니다. 예: **cn = changelog**. 델타 가져오기를 수행하려면 이 값을 지정해야 합니다.

다음은 기본 변경 로그 DN의 목록입니다.

| 디렉터리 | 델타 변경 로그 |
| --- | --- |
| Microsoft AD LDS 및 AD GC | 자동으로 검색됨. USNChanged. |
| Apache 디렉터리 서버 | 사용할 수 없음. |
| 디렉터리 389 | 변경 로그. 사용할 기본값: **cn=changelog** |
| IBM Tivoli DS | 변경 로그. 사용할 기본값: **cn=changelog** |
| Isode 디렉터리 | 변경 로그. 사용할 기본값: **cn=ChangeLog**
| Novell/NetIQ eDirectory | 사용할 수 없음. TimeStamp. 커넥터는 마지막 업데이트된 날짜/시간을 사용하여 추가되거나 업데이트된 레코드를 가져옵니다. |
| DJ/DS 열기 | 변경 로그. 사용할 기본값: **cn=Changelog** |
| LDAP 열기 | 액세스 로그. 사용할 기본값: **cn=accesslog** |
| Oracle DSEE | 변경 로그. 사용할 기본값: **cn=changelog** |
| RadiantOne VDS | 가상 디렉터리. VDS에 연결된 디렉터리에 따라 다릅니다. |
| Sun One 디렉터리 서버 | 변경 로그. 사용할 기본값: **cn=Changelog** |

암호 특성은 커넥터가 암호 변경 및 암호 집합 작업에서 암호를 설정하는 데 사용해야 하는 특성의 이름입니다. 기본적으로 **userPassword**로 설정하지만 특정 LDAP 시스템에 필요한 경우 변경할 수 있습니다.

추가 파티션 목록에서 자동으로 검색된 추가 네임스페이스를 추가할 수 있습니다. 예를 들어 여러 서버가 동시에 모두 가져와야 하는 논리 클러스터를 구성하는 경우 사용될 수 있습니다. Active Directory가 하나의 포리스트에서 여러 도메인을 가질 수 있지만 모든 도메인이 하나의 스키마를 공유하는 것처럼 동일하게 이 상자에 추가 네임스페이스를 입력하여 시뮬레이션할 수 있습니다. 각 네임스페이스를 서로 다른 서버에서 가져올 수 있으며 파티션 및 계층 구조 구성 페이지에서 구성됩니다. Ctrl + Enter를 사용하여 새 줄을 가져옵니다.

### 프로비전 계층 구성

이 페이지는 프로비전되어야 하는 DN 구성 요소(예: OU)를 개체 형식에 매핑합니다. 예: organizationalUnit

![프로비전 계층 구조](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

프로비전 계층 구조를 구성하여 필요할 때 자동으로 구조를 만들기 위해 커넥터를 구성할 수 있습니다. 예를 들어 네임스페이스 dc=contoso,dc=컴퓨터 및 새 개체 cn=Joe, ou=시애틀, c=미국, dc=contoso, dc=com을 프로비전된 경우 해당 사항이 디렉터리에 없으면 커넥터는 미국이라는 국가 및 시애틀이라는 organizationalUnit로 새 개체를 만들 수 있습니다.

### 파티션 및 계층 구조 구성

파티션 및 계층 구조 페이지에서 가져오기 및 내보내기하려는 개체를 사용하여 모든 네임스페이스를 선택합니다.

![파티션](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

각 네임스페이스의 경우 연결 화면에 지정된 값을 재정의하는 연결 설정을 구성할 수 있습니다. 이러한 값을 기본 빈 값으로 남긴 경우 연결 화면의 정보가 사용됩니다.

커넥터를 가져오고 내보낼 컨테이너 및 OU를 선택하는 것도 가능합니다.

### 앵커 구성

이 페이지는 미리 구성된 값이 있으며 변경될 수 없습니다. 서버 공급 업체 및 버전이 확인된 경우 변경할 수 없는 특성(예: 개체에 대한 GUID)으로 채워질 수 있습니다. 검색되지 않거나 변경할 수 없는 특성이 없는 것으로 알려져 있다면 커넥터는 고유 이름을 앵커로 사용합니다.

![anchors](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

다음은 사용되는 LDAP 서버 목록 및 앵커입니다.

| 디렉터리 | 앵커 특성 |
| --- | --- |
| Microsoft AD LDS 및 AD GC | objectGUID |
| 389 디렉터리 서버 | dn |
| Apache 디렉터리 | dn |
| IBM Tivoli DS | dn |
| Isode 디렉터리 | dn |
| Novell/NetIQ eDirectory | GUID |
| DJ/DS 열기 | dn |
| LDAP 열기 | dn |
| Oracle ODSEE | dn |
| RadiantOne VDS | dn |
| Sun One 디렉터리 서버 | dn |

## 기타 참고 사항

이 섹션은 이 커넥터에 지정되거나 다른 이유로 중요한 측면의 정보를 제공합니다.

### 델타 가져오기

LDAP 열기의 델타 워터 마크는 UTC 날짜/시간입니다. 이러한 이유로 FIM 동기화 서비스와 LDAP 열기 간에 시계가 동기화되어야 합니다. 그렇지 않은 경우 델타 변경 로그의 일부 항목은 생략될 수 있습니다.

Novell eDirectory의 경우 델타 가져오기는 개체 삭제를 검색하지 않습니다. 이러한 이유로 모든 삭제된 개체를 찾기 위해 주기적으로 전체 가져오기가 실행될 필요가 있습니다.

날짜/시간에 기반한 델타 변경 로그를 사용하는 디렉터리의 경우 LDAP 서버와 현재 커넥터 공간 정의 간의 차이점을 찾으려고 주기적인 시간에 전체 가져오기를 실행하는 것이 좋습니다.

## 문제 해결

-	커넥터의 문제를 해결하기 위해 로깅을 사용하는 방법에 대한 자세한 내용은 [커넥터에 ETW 추적을 사용하는 방법](http://go.microsoft.com/fwlink/?LinkId=335731) 참조하세요.

<!---HONumber=AcomDC_1217_2015-->