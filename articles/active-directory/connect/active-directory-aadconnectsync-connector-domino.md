---
title: "Lotus Domino 커넥터 | Microsoft Docs"
description: "이 문서에서는 Microsoft의 Lotus Domino 커넥터를 구성하는 방법을 설명합니다."
services: active-directory
documentationcenter: 
author: AndKjell
manager: femila
editor: 
ms.assetid: e07fd469-d862-470f-a3c6-3ed2a8d745bf
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 328e2039bbaa8988dc35b46712fd0de6f8bcbee8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="lotus-domino-connector-technical-reference"></a>Lotus Domino 커넥터 기술 참조
이 문서에서는 Lotus Domino 커넥터를 설명합니다. 이 문서는 다음 제품에 적용됩니다.

* Microsoft Identity Manager 2016 (MIM2016)
* Forefront Identity Manager 2010 R2 (FIM2010R2)
  * 핫픽스 4.1.3671.0 이상 [KB3092178](https://support.microsoft.com/kb/3092178)을 사용해야 합니다.

MIM2016와 FIM2010R2의 경우 커넥터는 [Microsoft 다운로드 센터](http://go.microsoft.com/fwlink/?LinkId=717495)에서 다운로드로 사용할 수 있습니다.

## <a name="overview-of-the-lotus-domino-connector"></a>Lotus Domino 커넥터의 개요
Lotus Domino 커넥터를 사용하면 IBM Lotus Domino 서버를 통해 동기화 서비스를 통합할 수 있습니다.

전체적인 관점에서 보면 커넥터의 현재 릴리스에서 다음과 같은 기능이 지원됩니다.

| 기능 | 지원 |
| --- | --- |
| 연결된 데이터 원본 |서버:  <li>Lotus Domino 8.5.x</li><li>Lotus Domino 9.x</li>클라이언트:<li>Lotus Domino 8.5.x</li><li>Lotus Notes 9.x</li> |
| 시나리오 |<li>개체 수명 주기 관리</li><li>그룹 관리</li><li>암호 관리</li> |
| 작업 |<li>전체 및 델타 가져오기</li><li>내보내기</li><li>HTTP 암호에 대한 암호 설정 및 변경</li> |
| 스키마 |<li>사람(로밍 사용자, 연락처(인증서가 없는 사람))</li><li>그룹</li><li>리소스(리소스, 회의실, 온라인 모임)</li><li>메일 내 데이터베이스</li><li>지원되는 개체에 대한 특성의 동적 검색</li> |

Lotus Domino 커넥터는 Lotus Notes 클라이언트를 사용하여 Lotus Domino 서버와 통신합니다. 이 종속성의 결과로 지원되는 Lotus Notes 클라이언트는 동기화 서버에 설치되어야 합니다. 클라이언트와 서버 간의 통신은 Lotus Notes .NET Interop(Interop.domino.dll) 인터페이스를 통해 구현됩니다. 이 인터페이스는 Microsoft.NET 플랫폼과 Lotus Notes 클라이언트 간의 통신을 용이하게 하고 Lotus Domino 문서 및 뷰에 대한 액세스를 지원합니다. 또한 델타 가져오기의 경우(선택한 델타 가져오기 방법에 따라) c++ 네이티브 인터페이스를 사용할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
커넥터를 사용하기 전에 동기화 서버에 다음 필수 구성 요소가 충족되었는지 확인합니다.

* Microsoft.NET 4.5.2 Framework 이상
* Lotus Notes 클라이언트는 동기화 서버에 설치되어야 합니다.
* Lotus Domino 커넥터는 기본 Lotus Domino LDAP 스키마 데이터베이스(schema.nsf)가 Domino 디렉터리 서버에 있어야 합니다. 없을 경우 Domino 서버에서 LDAP 서비스를 실행하거나 다시 시작하여 설치할 수 있습니다.

### <a name="connected-data-source-permissions"></a>연결된 데이터 원본 사용 권한
Lotus Domino 커넥터에서 지원되는 작업을 수행하려면 다음 그룹의 멤버여야 합니다.

* 모든 권한 관리자
* 관리자
* 데이터베이스 관리자

다음 테이블에서는 각 작업에 필요한 사용 권한을 나열합니다.

| 작업 | 액세스 권한 |
| --- | --- |
| 가져오기 |<li>공용 문서 읽기</li><li> 모든 권한 관리자(모든 권한 관리자 그룹의 멤버인 경우 자동으로 ACL에 대한 유효한 액세스를 가집니다.)</li> |
| 암호 내보내기 및 설정 |유효한 액세스: <li>문서 만들기</li><li>문서 삭제</li><li>공용 문서 읽기</li><li>공용 문서 작성</li><li>문서 복제 또는 복사</li>작업을 내보내려면 다음 역할도 필요합니다. <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li> |

### <a name="direct-operations-and-adminp"></a>직접 작업 및 AdminP
작업은 Domino 디렉터리에 직접 또는 AdminP 프로세스를 통해 이동합니다. 다음 테이블에서는 모든 지원되는 개체, 작업 및 해당하는 경우 관련된 구현 방법을 나열합니다.

**기본 주소록**

| Object | 생성 | 업데이트 | 삭제 |
| --- | --- | --- | --- |
| 사람 |AdminP |직접 |AdminP |
| 그룹 |AdminP |직접 |AdminP |
| MailInDB |직접 |직접 |직접 |
| 리소스 |AdminP |직접 |AdminP |

**보조 주소록**

| Object | 생성 | 업데이트 | 삭제 |
| --- | --- | --- | --- |
| 사람 |해당 없음 |직접 |직접 |
| 그룹 |직접 |직접 |직접 |
| MailInDB |직접 |직접 |직접 |
| 리소스 |해당 없음 |해당 없음 |해당 없음 |

리소스를 만들 때 Notes 문서가 생성됩니다. 마찬가지로 리소스가 삭제되면 Notes 문서가 삭제됩니다.

### <a name="ports-and-protocols"></a>포트 및 프로토콜
IBM Lotus Notes 클라이언트 및 Domino 서버는 NRPC가 TCP/IP를 사용해야 하는 NRPC(Notes Remote Procedure Call)을 사용하여 통신합니다. 기본 포트 번호는 1352지만 Domino 관리자가 변경할 수 있습니다.

### <a name="not-supported"></a>지원되지 않음
Lotus Domino 커넥터의 현재 릴리스에서 다음 작업은 지원되지 않습니다.

* 서버 간에 사서함을 이동합니다.

## <a name="create-a-new-connector"></a>새 커넥터 만들기
### <a name="client-software-installation-and-configuration"></a>클라이언트 소프트웨어 설치 및 구성
커넥터를 설치하기 **전에** Lotus Notes를 서버에 설치해야 합니다.

설치할 때 **단일 사용자 설치**를 수행하도록 합니다. 기본 **다중 사용자 설치** 는 작동하지 않습니다.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

기능 페이지에서 필요한 Lotus Notes 기능 및 **클라이언트 단일 로그온**만 설치합니다. 커넥터가 Domino 서버에 로그온 할 수 있으려면 단일 로그온이 필요합니다.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**참고:** 커넥터의 서비스 계정으로 사용할 계정과 동일한 서버에 있는 사용자를 사용하여 Lotus Notes를 시작합니다. 또한 서버의 Lotus Notes 클라이언트를 닫습니다. 커넥터를 Domino 서버에 연결하려는 동시에 실행할 수 없습니다.

### <a name="create-connector"></a>커넥터 만들기
Lotus Domino 커넥터를 만들려면 **동기화 서비스**에서 **관리 에이전트** 및 **만들기**를 선택합니다. **Lotus Domino(Microsoft)** 커넥터를 선택합니다.  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

동기화 서비스의 버전이 **아키텍처**를 구성하는 기능을 제공하는 경우 커넥터가 **프로세스**에서 실행할 기본값으로 설정되도록 합니다.

### <a name="connectivity"></a>연결
연결 페이지에서 Lotus Domino 서버 이름을 지정하고 로그온 자격 증명을 입력해야 합니다.  
![연결](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

Domino 서버 속성은 서버 이름에 대한 다음 두 가지 형식을 지원합니다.

* ServerName
* ServerName/DirectoryName

커넥터가 Domino 서버에 연결하는 경우 더 빠른 응답을 제공하기 때문에 **ServerName/DirectoryName** 형식이 이 특성에 대해 기본 설정된 형식입니다.

제공된 UserID 파일은 동기화 서비스의 구성 데이터베이스에 저장됩니다.

**델타 가져오기** 의 경우 다음과 같은 옵션이 있습니다.

* **없음**. 커넥터는 델타 가져오기를 수행하지 않습니다.
* **추가/업데이트**. 커넥터는 델타 가져오기 추가 및 업데이트 작업을 수행합니다. 삭제의 경우 **전체 가져오기** 작업이 필요합니다. 이 작업은 .Net interop을 사용합니다.
* **추가/업데이트/삭제**. 커넥터는 델타 가져오기 추가, 업데이트 및 삭제 작업을 수행합니다. 이 작업은 네이티브 C++ 인터페이스를 사용합니다.

**스키마 옵션** 에는 다음과 같은 옵션이 있습니다.

* **기본 스키마**. 커넥터는 Domino 서버에서 스키마를 검색합니다. 이 선택 항목은 기본 옵션입니다.
* **DSML-스키마**. Domino 서버가 스키마를 표시하지 않는 경우 사용합니다. 그런 다음 스키마를 사용하여 DSML 파일을 만들고 대신 가져올 수 있습니다. DSML에 대한 자세한 내용은 [OASIS](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml)를 참조하세요.

다음을 클릭하는 경우 사용자 ID 및 암호 구성 매개 변수를 확인합니다.

### <a name="global-parameters"></a>글로벌 매개 변수
글로벌 매개 변수 페이지에서 표준 시간대를 구성하고 작업 옵션을 가져오고 내보냅니다.  
![글로벌 매개 변수](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

**Domino 서버 표준 시간대** 매개 변수는 Domino 서버의 위치를 정의합니다.

동기화 서비스가 마지막 두 가져오기 간의 변경 내용을 확인 수 있기 때문에 이 구성 옵션은 **델타 가져오기** 작업을 지원하는 데 요구됩니다.

>[!Note]
2017년 3월 업데이트부터 글로벌 매개 변수 화면에 사용자가 삭제하는 동안 사용자의 메일 데이터베이스의 삭제 옵션이 포함됩니다.

![사용자 사서함 삭제](./media/active-directory-aadconnectsync-connector-domino/AdminP.png)

#### <a name="import-settings-method"></a>설정 가져오기 메서드
**다음으로 전체 가져오기 수행** 에는 다음과 같은 옵션이 있습니다.

* 검색
* 보기(권장)

**검색** 은 Domino의 인덱싱을 사용하지만 인덱스가 실시간으로 업데이트되지 않고 서버에서 반환된 데이터가 항상 올바르지 않은 것이 일반적입니다. 변경 사항이 많은 시스템의 경우 이 옵션은 일반적으로 잘 작동하지 않고 상황에 따라 잘못된 삭제가 발생합니다. 그러나 **검색**은 **보기**보다 빠릅니다.

**보기** 는 데이터의 올바른 상태를 제공하기 때문에 권장 옵션입니다. 검색 보다 다소 느립니다.

#### <a name="creation-of-virtual-contact-objects"></a>가상 연락처 개체 만들기
**\_Contact 개체의 생성 사용**에는 다음과 같은 옵션이 있습니다.

* 없음
* 비참조 값
* 참조 및 비참조 값

Domino에서 참조 특성은 다양한 형식을 포함하여 다른 개체를 참조할 수 있습니다. 다른 버전을 나타낼 수 있으려면 커넥터는 **가상 연락처**(VC)라고도 하는 \_Contact 개체를 구현합니다. 기존 MV 개체에 참여할 수 있도록 이러한 개체를 생성하거나 새 개체로 프로젝션합니다. 이러한 방식으로 특성 참조를 보존할 수 있습니다.

이 설정을 사용하여 참조 특성의 내용이 DN 형식이 아니면 \_Contact 개체는 생성됩니다. 예를 들어 그룹의 멤버 특성은 SMTP 주소를 포함할 수 있습니다. 또한 참조 특성에 shortName 및 다른 특성이 드러날 수 있습니다. 이 시나리오의 경우 **비참조 값**을 선택합니다. 이 구성은 Domino 구현에 대한 가장 일반적인 설정입니다.

또한 Lotus Domino가 동일한 개체를 나타내는 다른 고유 이름을 통해 별도 주소록을 갖도록 구성한 경우 주소록에 있는 모든 참조 값에 \_Contact 개체를 만들 수 있습니다. 이 시나리오의 경우 **참조 및 비참조 값** 옵션을 선택합니다.

Domino에서 **FullName** 특성에 여러 값이 있는 경우 참조를 해결할 수 있도록 가상 연락처를 생성하도록 합니다. 예를 들어, 이 특성은 결혼 또는 이혼 후에 여러 값을 가질 수 있습니다. 확인란 선택 **사용... FullName은 이 시나리오에 대해 여러 값**을 포함합니다.

올바른 특성에 연결하면 \_Contact 개체는 MV 개체에 연결됩니다.

이러한 개체에는 해당 DN에 추가된 VC= \_Contact가 있습니다.

#### <a name="import-settings-conflict-object"></a>설정 가져오기, 충돌 개체
**충돌 개체 제외**

큰 Domino 구현에서 복제 문제로 인해 여러 개체에 동일한 DN가 있을 수 있습니다. 이러한 경우 커넥터는 다른 UniversalIDs와 동일한 DN을 사용하여 두 개체를 표시합니다. 이 충돌은 커넥터 공간에 생성되는 임시 개체를 발생시킵니다. 커넥터는 Domino에서 복제당하도록 선택된 개체를 무시할 수 있습니다. 이 확인란을 선택하는 것이 좋습니다.

#### <a name="export-settings"></a>설정 내보내기
**참조를 업데이트하기 위해 AdminP 사용** 옵션을 선택하지 않으면 멤버와 같은 참조 특성의 내보내기는 직접 호출이 되고 AdminP 프로세스를 사용하지 않습니다. AdminP이 참조 무결성을 유지하도록 구성되지 않는 경우에만 이 옵션을 사용합니다.

#### <a name="routing-information"></a>라우팅 정보
Domino에서 참조 특성에 DN에 접미사로 포함된 라우팅 정보가 있을 수 있습니다. 예를 들어 그룹의 멤버 특성은 **CN=example/organization@ABC**를 포함할 수 있습니다. 접미사 @ABC은(는) 라우팅 정보입니다. 라우팅 정보는 Domino에서 사용되어 다른 조직에 있는 시스템일 수 있는 올바른 Domino 시스템에 메일을 보냅니다. 라우팅 정보 필드에서는 커넥터의 범위에 있는 조직 내에서 사용된 라우팅 접미사를 지정할 수 있습니다. 다음의 값 중 하나가 참조 특성에서 접미사에 있으면 라우팅 정보는 참조에서 제거됩니다. 참조 값의 라우팅 접미사가 지정된 해당 값 중 하나와 일치될 수 없으면 \_Contact 개체가 생성됩니다. 이러한 \_Contact 개체는 DN에 삽입된 **RO=@<RoutingSuffix>**를 사용하여 만들어집니다. 이러한 \_Contact 개체의 경우 필요에 따라 \_routingName, \_contactName, \_displayName 및 UniversalID 특성이 실제 개체에 연결되도록 추가됩니다.

#### <a name="additional-address-books"></a>추가 주소록
보조 주소록의 이름을 제공하는 **디렉터리 지원** 이 설치되지 않은 경우 이러한 주소록을 수동으로 입력할 수 있습니다.

#### <a name="multivalued-transformation"></a>다중값 변환
Lotus Domino의 많은 특성은 다중값입니다. 해당하는 메타버스 특성은 일반적으로 단일 값입니다. 가져오기 및 내보내기 작업 옵션을 구성하여 커넥터가 영향을 받는 특성의 필요한 번역을 돕도록 할 수 있습니다.

**내보내기**  
내보내기 작업 옵션은 다음과 같은 두 가지 모드를 지원합니다.

* 항목 추가
* 항목 바꾸기

**항목 바꾸기** – 이 옵션을 선택하면 커넥터는 항상 Domino에서 특성의 현재 값을 제거하고 제공된 값으로 바꿉니다. 제공된 값은 단일 값 또는 다중값일 수 있습니다.

예제: 사용자 개체의 도우미 특성에는 다음과 같은 값이 있습니다.

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**David Alexander** 라는 새 길잡이가 이 사용자 개체에 할당된 경우 결과는 다음과 같습니다.

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**추가 항목** – 이 옵션을 선택하면 커넥터는 Domino에서 특성의 기존 값을 유지하고 데이터 목록 맨 위에 있는 새 값을 삽입합니다.

예제: 사용자 개체의 도우미 특성에는 다음과 같은 값이 있습니다.

* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**David Alexander** 라는 새 길잡이가 이 사용자 개체에 할당된 경우 결과는 다음과 같습니다.

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

**가져오기**  
가져오기 작업 옵션은 다음과 같은 두 가지 모드를 지원합니다.

* 기본값
* 다중값에서 단일 값으로

**기본** – 기본 옵션을 선택하면 모든 특성의 모든 값을 가져올 수 있습니다.

**다중값에서 단일 값으로** – 이 옵션을 선택하면 다중값 특성이 단일 값 특성으로 변환됩니다. 둘 이상의 값이 있으면 위쪽의 값(이 값은 일반적으로 최신 값임)이 사용됩니다.

예제: 사용자 개체의 도우미 특성에는 다음과 같은 값이 있습니다.

* CN=David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
* CN=Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
* CN=John Smith/OU=Contoso/O=Americas,NAB=names.nsf

이 특성에 대한 최신 업데이트는 **David Alexander**입니다. 가져오기 작업 옵션이 다중값을 단일 값에 설정되어 있기 때문에 커넥터는 **David Alexander** 를 커넥터 공간으로 가져옵니다

다중값 특성을 단일 값 특성으로 변환하는 논리는 그룹 멤버 특성 및 개인 전체 이름 특성에 적용되지 않습니다.

전역 규칙의 예외로 특성 단위 다중값 특성에 대한 변환 규칙을 구성하고 가져오며 내보낼 수 있습니다. 이 옵션을 구성하려면 **제외 특성 목록 가져오기** 및 **제외 특성 목록 내보내기** 텍스트 상자에 [objecttype].[attributename]을 입력합니다. 예를 들어 Person.Assistant를 입력하고 전역 플래그가 경우 모든 값을 가져오도록 설정된 경우 첫 번째 값만 길잡이로 가져옵니다.

#### <a name="certifiers"></a>인증자
커넥터에 의해 모든 조직/조직 단위가 나열됩니다. 기본 주소록에 사용자 개체를 내보낼 수 있으려면 암호가 있는 인증자가 필요합니다.

모든 인증자가 동일한 암호를 갖는 경우 **모든 인증자에 대한 암호**를 사용할 수 있습니다. 그런 다음 여기에 암호를 입력하고 인증자 파일을 지정할 수 있습니다.

가져오기만 하는 경우 인증자를 지정할 필요가 없습니다.

### <a name="configure-provisioning-hierarchy"></a>프로비전 계층 구성
Lotus Domino 커넥터를 구성할 때 이 대화 상자 페이지를 건너뜁니다. Lotus Domino 커넥터가 계층 구조 프로비전을 지원하지 않습니다.  
![프로비전 계층 구조](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>파티션 및 계층 구조 구성
파티션 및 계층 구조를 구성할 때 NAB=names.nsf라는 기본 주소록을 선택해야 합니다. 기본 주소록 외에도 존재하는 경우 보조 주소록을 선택할 수 있습니다.  
![파티션](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>특성 선택
특성을 구성할 때 **\_MMS\_**라는 접두사가 붙은 모든 특성을 선택해야 합니다. Lotus Domino에 새 개체를 프로비전할 때 이러한 특성이 요구됩니다.

![특성](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>개체 수명 주기 관리
이 섹션에서는 Domino에서 여러 개체의 개요를 제공합니다.

### <a name="person-objects"></a>사용자 개체
사용자 개체는 조직 및 조직 단위에서 사용자를 나타냅니다. 기본 특성 외에도 Domino 관리자는 사용자 개체에 사용자 지정 특성을 추가할 수 있습니다. 최소한 사용자 개체는 모든 필수 특성을 포함해야 합니다. 필수 특성의 전체 목록은 [Lotus Notes 속성](#lotus-notes-properties)을 참조하세요. 사용자 개체를 등록하려면 다음과 같은 필수 조건을 충족해야 합니다.

* 주소록(names.nsf)을 정의해야 하며 기본 주소록이어야 합니다.
* 조직/조직 단위의 특정 사용자를 등록하기 위해 O/OU 인증자 ID 및 암호가 있어야 합니다.
* 사용자 개체에 대해 Lotus Notes 속성의 특정 집합을 설정해야 합니다. 이러한 속성은 사용자 개체를 프로비전하는 데 사용됩니다. 자세한 내용은 이 문서 뒷부분에서 [Lotus Notes 속성](#lotus-notes-properties) 섹션을 참조하세요.
* 사용자에 대한 초기 HTTP 암호는 특성이며 프로비전하는 동안 설정됩니다.
* 사용자 개체는 다음 세 가지 지원되는 형식 중 하나여야 합니다.
  1. 메일 파일 및 사용자 ID 파일을 포함하는 일반 사용자
  2. 로밍 사용자(로밍하는 모든 데이터베이스파일을 포함하는 일반 사용자)
  3. 연락처(ID 파일이 없는 사용자)

나아가 개인(연락처 제외)은 \_MMS\_IDRegType 속성의 값에서 정의된 대로 미국 사용자 및 국제 사용자로 그룹화될 수 있습니다. 이러한 사용자는 Notes 클라이언트를 사용하여 Lotus Domino 서버에 액세스하고 Notes ID 및 개인 문서를 갖습니다. Notes 메일을 사용 중인 경우 메일 파일도 갖게 됩니다. 사용자를 활성화하려면 등록되어야 합니다. 자세한 내용은 다음을 참조하세요.

* [Notes 사용자 설정](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
* [사용자 등록](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
* [사용자 관리](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
* [사용자 이름 바꾸기](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

이러한 모든 작업은 Lotus Domino에서 수행된 다음 동기화 서비스로 가져옵니다.

### <a name="resources-and-rooms"></a>리소스 및 공간
리소스는 Lotus Domino에 있는 데이터베이스의 다른 형식입니다. 리소스는 프로젝터와 같은 다양한 유형의 장비가 있는 회의실일 수 있습니다. 리소스 형식 특성으로 정의된 Lotus Domino 커넥터에서 지원하는 리소스의 하위 폼은 다음과 같습니다.

| 리소스의 형식 | 리소스 형식 특성 |
| --- | --- |
| 공간 |1 |
| 리소스(기타) |2 |
| 온라인 모임 |3 |

작업할 리소스 개체 형식의 경우 다음이 필요합니다.

* 리소스 예약 데이터베이스는 이미 연결된 Domino 서버에 있어야 합니다.
* 사이트는 리소스에 대해 이미 정의됩니다.

리소스 예약 데이터베이스는 다음과 같은 3가지 형식의 문서를 포함합니다.

* 사이트 프로필
* 리소스
* 예약

리소스 예약 데이터베이스의 설정에 대한 자세한 내용은 [리소스 예약 데이터베이스 설정](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html)을 참조하세요.

**리소스 만들기, 업데이트 및 삭제**  
만들기, 업데이트 및 삭제 작업은 리소스 예약 데이터베이스에서 Lotus Domino 커넥터를 통해 수행됩니다. 리소스는 Names.nsf(즉, 기본 주소록)에 문서로 만들어집니다. 리소스를 편집 및 삭제하는 방법에 대한 자세한 내용은 [리소스 문서 편집 및 삭제](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html)를 참조하세요.

**리소스에 대한 작업 가져오기 및 내보내기**  
리소스는 다른 개체 형식처럼 동기화 서비스로 가져오거나 동기화 서비스에서 내보낼 수 있습니다. 구성하는 동안 개체 형식을 리소스로 선택합니다. 성공적인 내보내기 작업의 경우 리소스 형식, 회의 데이터베이스 및 사이트 이름에 대한 세부 정보가 있어야 합니다.

### <a name="mail-in-databases"></a>메일 내 데이터베이스
메일 내 데이터베이스는 메일을 수신하도록 설계된 데이터베이스입니다. 특정 Lotus Domino 사용자 계정과 연결되지 않은 Lotus Domino 사서함입니다(즉, 고유한 파일 ID 및 암호 없음). 메일 내 데이터베이스는 자신 및 전자 메일 주소와 연관된 고유한 사용자 ID("짧은 이름")입니다.

여러 사용자들 간에 공유할 수 있는 고유한 전자 메일 주소를 사용하는 개별 사서함을 필요로 하지 않는 경우(예: group@contoso.com) 메일 내 데이터베이스를 만듭니다. 이 사서함에 대한 액세스는 해당 Access Control 목록(ACL)을 통해 제어되며 이는 사서함을 열수 있는 Notes 사용자의 이름을 포함합니다.

필수 특성 목록의 경우 이 문서의 뒷부분에 나오는 [필수 특성](#mandatory-attributes) 이라는 섹션을 참조하세요.

데이터베이스가 메일을 수신하도록 설계되면 메일 내 데이터베이스 문서는 Lotus Domino에 생성됩니다. 이 문서는 데이터베이스의 복사본을 저장하는 모든 서버의 Domino 디렉터리에 있어야 합니다. 메일 내 데이터베이스 문서를 만드는 자세한 내용은 [메일 내 데이터베이스 문서 만들기](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html)를 참조하세요.

메일 내 데이터베이스를 만들기 전에 데이터베이스는 이미 Domino 서버에 있어야 합니다.(Lotus 관리자가 생성했어야 함)

### <a name="group-management"></a>그룹 관리
다음 리소스에서 Lotus Domino 그룹 관리의 자세한 개요를 얻을 수 있습니다.

* [그룹 사용](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
* [그룹 만들기](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
* [그룹 만들기 및 수정](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
* [그룹 관리](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
* [그룹 이름 바꾸기](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>암호 관리
등록된 Lotus Domino 사용자의 경우 두 가지 형식의 암호가 있습니다.

1. 사용자 암호(User.id 파일에 저장됨)
2. 인터넷/HTTP 암호

Lotus Domino 커넥터는 HTTP 암호를 사용하는 작업만 지원합니다.

암호 관리를 수행하려면 관리 에이전트 설계자에서 커넥터에 대한 암호 관리를 사용하도록 설정해야 합니다. 암호 관리를 활성화하려면 **확장 구성** 대화 상자 페이지에서 **암호 관리 사용**을 선택합니다.  
![암호 관리 사용](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

Lotus Domino 커넥터는 인터넷 암호에 대한 다음 작업을 지원합니다.

* 암호 설정: 암호 설정 작업은 Domino에서 사용자가 새 HTTP/인터넷 암호를 설정하도록 합니다. 기본적으로 계정도 잠금이 해제됩니다. 잠금 해제 플래그는 동기화 엔진의 WMI 인터페이스에 노출됩니다.
* 암호 변경: 이 시나리오에서 사용자가 암호를 변경하려 하거나 지정된 시간이 지나서 암호를 변경하라는 메시지가 표시됩니다. 이 작업을 수행하려면 이전 및 새 암호는 모두 필수적입니다. 한 번 변경되면 새 암호는 Lotus Domino에서 업데이트됩니다.

자세한 내용은 다음을 참조하세요.

* [인터넷 잠금 기능 사용](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
* [인터넷 암호 관리](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>참조 정보
이 섹션에서는 Lotus Domino 커넥터에 대한 특성 설명 및 특성 요구 사항을 나열합니다.

### <a name="lotus-notes-properties"></a>Lotus Notes 속성
Lotus Domino 디렉터리에 사용자 개체를 프로비전할 때 개체에는 채워진 특정 값을 가진 특정 속성 집합이 있어야 합니다. 이러한 값은 만들기 작업에 필요합니다.

다음 테이블에서는 이러한 속성을 나열하고 설명을 제공합니다.

| 속성 | 설명 |
| --- | --- |
| \_MMS_AltFullName |사용자의 대체 전체 이름입니다. |
| \_MMS_AltFullNameLanguage |사용자의 대체 전체 이름을 지정하는 데 사용할 언어입니다. |
| \_MMS_CertDaysToExpire |인증서가 만료하기 전에 현재 날짜에서 남은 일 수입니다. 지정하지 않으면 기본 날짜는 현재 날짜로부터 2년입니다. |
| \_MMS_Certifier |인증자의 조직 계층 이름을 포함하는 속성입니다. 예: OU=OrganizationUnit, O=조직, C=국가. |
| \_MMS_IDPath |속성이 비어 있으면 어떤 사용자 식별 파일도 동기화 서버에 로컬로 만들어지지  않습니다. 속성이 파일 이름을 포함하면 사용자 ID 파일은 madata 폴더에 만들어집니다. 속성은 전체 경로 포함할 수도 있습니다. |
| \_MMS_IDRegType |개인은 연락처, 미국 사용자 및 국제 사용자로 분류할 수 있습니다. 다음 테이블에서는 가능한 값을 나열합니다. <li>0 - 연락처</li><li>1 - 미국 사용자</li><li>2 - 국제 사용자</li> |
| \_MMS_IDStoreType |미국 및 국제 사용자에 대한 필수 속성입니다. 속성은 사용자 ID가 Notes 주소록 또는 사용자의 메일 파일에서 첨부 파일로 저장되는지 여부를 지정하는 정수 값을 포함합니다. 사용자 ID 파일이 주소록의 첨부 파일인 경우 필요에 따라 \_MMS_IDPath를 사용하여 파일로 만들 수 있습니다. <li>비어 있음 - ID 자격 증명 모음의 저장소 ID 파일, ID 파일 없음(연락처에 사용됨)</li><li> 1 - Notes 주소록의 첨부 파일입니다. \_MMS_Password 속성은 첨부된 사용자 식별 파일에 대해 설정되어야 합니다.</li><li>2 - 사용자의 메일 파일의 저장소 ID입니다. 개인 등록 시 메일 파일을 만들 수 있도록 \_MMS_UseAdminP는 false로 설정되어야 합니다. \_MMS_Password 속성은 사용자 식별 파일에 대해 설정되어야 합니다.</li> |
| \_MMS_MailQuotaSizeLimit |전자 메일 파일 데이터베이스에 대해 허용되는 메가바이트 수입니다. |
| \_MMS_MailQuotaWarningThreshold |경고가 발생하기 전에 전자 메일 파일 데이터베이스에 대해 허용되는 메가바이트 수입니다. |
| \_MMS_MailTemplateName |사용자의 전자 메일 파일을 만드는 데 사용되는 전자 메일 템플릿 파일입니다. 템플릿이 지정된 경우 지정된 템플릿을 사용하여 메일 파일이 만들어집니다. 템플릿이 지정되지 않은 경우 기본 템플릿 파일이 파일을 만드는 데 사용됩니다. |
| \_MMS_OU |아래는 인증자 OU 이름을 나타내는 선택적 속성입니다. 이 속성은 연락처에 대해 비어 있어야 합니다. |
| \_MMS_Password |사용자에 대한 필수 속성입니다. 속성은 개체의 ID 파일에 대한 암호를 포함합니다. |
| \_MMS_UseAdminP |메일 파일이 Domino 서버(내보내기 프로세스에 비동기)의 AdminP 프로세스에 의해 생성되어야 하는 경우 속성은 true로 설정되어야 합니다. 속성이 false로 설정된 경우 메일 파일은 Domino 사용자를 통해 만들어집니다(내보내기 프로세스에 동기화됨). |

관련된 식별 파일을 가진 사용자의 경우 \_MMS_Password 속성은 값을 포함해야 합니다. Lotus Notes 클라이언트를 통한 전자 메일 액세스의 경우 사용자의 MailServer 및 MailFile 속성은 값을 포함해야 합니다.

웹 브라우저를 통해 전자 메일에 액세스하려면 다음 속성은 값을 포함해야 합니다.

* MailFile - 메일 파일이 저장된 Lotus Domino 서버의 경로를 포함하는 필수 속성입니다.
* MailServer - Lotus Domino 서버 이름을 포함하는 필수 속성입니다. 이 값은 Domino 서버에서 Lotus 메일 파일을 만들 때 사용할 이름입니다.
* HTTPPassword - 개체에 대한 웹 액세스 암호를 포함하는 선택적 속성입니다.

메일 기능 없이 Domino 서버에 액세스하려면 HTTPPassword 속성은 값을 포함해야 합니다. MailFile 속성 및 MailServer 속성은 비어 있을 수 있습니다.

\_MMS_ IDStoreType = 2(메일 파일의 저장소 ID)를 사용하여 NotesRegistrationclass의 MailSystem 속성은 REG_MAILSYSTEM_INOTES (3)으로 설정됩니다.

### <a name="mandatory-attributes"></a>필수 특성
Lotus Domino 커넥터는 주로 다음 형식의 개체(문서 형식)를 지원합니다.

* 그룹
* 메일 내 데이터베이스
* 사람
* 연락처(인증자가 없는 사용자)
* 리소스

이 섹션에서는 Domino 서버에 내보내도록 지원되는 개체 각각에 대해 필수인 특성을 나열합니다.

| 개체 유형 | 필수 특성 |
| --- | --- |
| 그룹 |<li>ListName</li> |
| Main-In 데이터베이스 |<li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |
| 사람 |<li>LastName</li><li>MailFile</li><li>ShortName</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li> |
| 연락처(인증자가 없는 사용자) |<li>\_MMS_IDRegType</li> |
| 리소스 |<li>FullName</li><li>ResourceType</li><li>ConfDB</li><li>ResourceCapacity</li><li>사이트</li><li>displayName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li> |

## <a name="common-issues-and-questions"></a>일반적인 문제 및 질문
### <a name="schema-detection-does-not-work"></a>스키마 검색이 작동하지 않습니다.
스키마를 검색할 수 있으려면 Domino 서버에 schema.nsf 파일이 있어야 합니다. 이 파일은 LDAP이 서버에 설치된 경우에 표시됩니다. 스키마를 검색할 수 없는 경우 다음을 확인합니다.

* 파일 schema.nsf는 Domino 서버의 루트 폴더에 있습니다.
* 사용자는 schema.nsf 파일을 볼 권한이 있습니다.
* LDAP 서버를 다시 시작하도록 강제합니다. **Lotus Domino 콘솔**을 열고 **Tell LDAP ReloadSchema** 명령을 사용하여 스키마를 다시 로드합니다.

### <a name="not-all-secondary-address-books-are-visible"></a>일부 보조 주소록이 표시되지 않습니다.
Domino 커넥터는 **디렉터리 지원** 기능에 의존하여 보조 주소록을 찾을 수 있습니다. 보조 주소록이 누락된 경우 [디렉터리 지원](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) 이 활성화되고 Domino 서버에 구성되었는지 확인합니다.

### <a name="custom-attributes-in-domino"></a>Domino의 사용자 지정 특성
Domino에서 스키마를 확장하는 몇 가지 방법이 있으므로 커넥터에서 사용할 수 있도록 사용자 지정 특성으로 표시합니다.

**접근 방식1: Lotus Domino 스키마 확장**

1. [다음 단계](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html)를 수행하여 Domino 디렉터리 템플릿 {PUBNAMES.NTF}의 복사본을 만듭니다. 기본 IBM Lotus Domino 디렉터리 템플릿을 사용자 지정하지 않아야 합니다.
2. Domino 디자이너에서 만든 Domino 디렉터리 템플릿 {CONTOSO.NTF} 템플릿의 복사본을 열고 다음 단계를 수행합니다.
   * 공유 요소를 클릭하고 하위 폼을 확장합니다.
   * ${ObjectName}InheritableSchema 하위 폼을 두 번 클릭합니다. 여기서 {ObjectName}은 기본 구조적 개체 클래스의 이름(예: 개인)입니다.
   * {MyPersonAtrribute} 스키마 및 해당 특성에 추가하려는 특성의 이름을 지정합니다. **만들기** 메뉴를 선택하여 필드를 만든 다음 메뉴에서 **필드**를 선택합니다.
   * 추가된 필드에서 필드 속성 창의 형식, 스타일, 크기, 글꼴 및 기타 관련된 매개 변수를 선택하여 해당 속성을 설정합니다.
   * 해당 특성에 대해 지정된 이름과 동일한 특성 기본값을 유지합니다.(예: 특성 이름이 MyPersonAttribute인 경우 같은 이름을 가진 기본값은 그대로 유지합니다)
   * 업데이트된 값으로 ${ObjectName}InheritableSchema 하위 폼을 저장합니다.
3. [다음 단계](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)를 수행하여 Domino 디렉터리 템플릿 {PUBNAMES.NTF}를 새로 사용자 지정된 템플릿 {CONTOSO.NTF}로 바꿉니다.
4. Domino 관리자를 닫고 Domino 콘솔을 열어서 LDAP 서비스를 다시 시작하고 LDAP 스키마를 다시 로드합니다.
   * Domino 콘솔에서 **Domino 명령** 텍스트 파일에 명령을 삽입하여 LDAP 서비스를 다시 시작합니다. - [작업 LDAP 다시 시작](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)
   * LDAP를 다시 로드하려면 Tell LDAP을 사용합니다. - Tell LDAP ReloadSchema
5. Domino 관리자를 열고 사용자 및 그룹 탭을 선택하여 추가된 특성이 domino 사용자 추가에 반영된 것을 확인합니다.
6. **파일** 탭에서 Schema.nsf를 열고 추가된 특성이 dominoPerson LDAP 개체 클래스에 반영된 것을 확인합니다.

**접근 방식2: 사용자 지정 특성을 사용하여 auxClass 생성 및 개체 클래스와 연결**

1. [다음 단계](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html)를 수행하여 Domino 디렉터리 템플릿 {PUBNAMES.NTF}의 복사본을 만듭니다. 기본 IBM Lotus Domino 디렉터리 서식 파일을 사용자 지정하지 마세요.
2. Domino 디자이너에서 만든 Domino 디렉터리 템플릿 {CONTOSO.NTF} 템플릿의 복사본을 엽니다.
3. 왼쪽 창에서 공유 코드 및 하위 폼을 선택합니다.
4. 새 하위 폼을 클릭합니다.
5. 새 하위 폼에 대한 속성을 지정하려면 다음을 수행합니다.
   * 새 하위 폼 열기를 사용하여 설계 - 하위 폼 속성을 선택합니다.
   * 이름 속성 옆에 있는 보조 개체 클래스에 대한 이름을 입력합니다.(예: TestSubform)
   * 선택된 옵션 속성 "하위 폼 삽입에 포함... 대화 상자"를 유지합니다.
   * 옵션 속성 "Notes의 HTML 통해 렌더링 패스" 선택을 취소합니다.
   * 다른 속성을 동일하게 두고 하위 폼 속성 상자를 닫습니다.
   * 새로운 하위 폼을 저장하고 닫습니다.
6. 보조 개체 클래스를 정의하기 위해 필드를 추가하려면 다음을 수행합니다.
   * 만든 하위 폼을 엽니다.
   * 만들기 - 필드를 선택합니다.
   * 필드 대화 상자의 기본 탭에서 이름 옆에 이름(예: {MyPersonTestAttribute})을 지정합니다.
   * 추가된 필드에서 형식, 스타일, 크기, 글꼴 및 관련된 속성을 선택하여 해당 속성을 설정합니다.
   * 해당 특성에 대해 지정된 이름과 동일한 특성 기본값을 유지합니다.(예: 특성 이름이 MyPersonTestAttribute인 경우 같은 이름을 가진 기본값은 그대로 유지합니다)
   * 업데이트된 값이 있는 하위 폼을 저장하고 다음을 수행합니다.
     * 왼쪽 창에서 공유 코드 및 하위 폼을 선택합니다.
     * 새 하위 폼을 선택하고 설계 - 설계 속성을 선택합니다.
     * 왼쪽에서 세 번째 탭을 클릭하고 **설계 변경의 금지 전파**를 선택합니다.
7. ${ObjectName}ExtensibleSchema 하위 폼을 엽니다. 여기서 {ObjectName}은 개인과 같은 기본 구조적 개체 클래스의 이름입니다.
8. 리소스를 삽입하고 하위 폼(예: 만든 TestSubform)을 선택하고 ${ObjectName}ExtensibleSchema 하위 폼을 저장합니다.
9. [다음 단계](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html)를 수행하여 Domino 디렉터리 템플릿 {PUBNAMES.NTF}를 새로 사용자 지정된 템플릿 {CONTOSO.NTF}로 바꿉니다.
10. Domino 관리자를 닫고 Domino 콘솔을 열어서 LDAP 서비스를 다시 시작하고 LDAP 스키마를 다시 로드합니다.
    * Domino 콘솔에서 **Domino 명령** 텍스트 파일에 명령을 삽입하여 LDAP 서비스를 다시 시작합니다. - [작업 LDAP 다시 시작](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html)
    * LDAP 스키마를 다시 로드하려면 Tell LDAP 명령인 **Tell LDAP ReloadSchema**를 사용합니다.
11. Domino 관리자를 열고 사용자 및 그룹 탭을 선택하여 추가된 특성이 domino 사용자 추가(기타 탭에서)에 반영된 것을 확인합니다.
12. **파일** 탭에서 Schema.nsf를 열고 추가된 특성이 TestSubform LDAP 보조 개체 클래스에 반영된 것을 확인합니다.

**접근 방식3: ExtensibleObject 클래스에 사용자 지정 특성 추가**

1. 루트 디렉터리에 배치된 {Schema.nsf} 파일을 엽니다.
2. **모든 스키마 문서**의 왼쪽 메뉴에서 LDAP 개체 클래스를 선택하고 **개체 추가 클래스** 단추를 클릭합니다.
3. {zzzExtensibleSchema}의 형식으로 LDAP 이름을 제공합니다. 여기서 zzz는 개인과 같은 기본 구조적 개체 클래스의 이름입니다. 예를 들어 Person 개체 클래스에 대한 스키마를 확장하려면 LDAP 이름 {PersonExtensibleSchema}를 제공합니다.
4. 스키마를 확장하기 위해 상위 개체 클래스 이름을 제공합니다. 예를 들어 Person 개체 클래스에 대한 스키마를 확장하려면 상위 개체 클래스 이름 {dominoPerson}을 제공합니다.
5. 개체 클래스에 해당하는 유효한 OID를 제공합니다.
6. 요구 사항에 따라 필수 또는 선택적 특성 형식 필드에서 확장된/사용자 지정 특성을 선택합니다.
7. 필수 특성을 ExtensibleObjectClass에 추가한 후에 **저장 및 닫기**를 클릭합니다.
8. ExtensibleObjectClass는 확장된 특성이 있는 각 기본 개체 클래스에 대해 생성됩니다.

## <a name="troubleshooting"></a>문제 해결
* 커넥터의 문제를 해결하기 위해 로깅을 사용하는 방법에 대한 자세한 내용은 [커넥터에 ETW 추적을 사용하는 방법](http://go.microsoft.com/fwlink/?LinkId=335731)참조하세요.
