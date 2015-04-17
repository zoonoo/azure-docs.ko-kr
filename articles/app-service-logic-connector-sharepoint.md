<properties 
   pageTitle="논리 앱에서 SharePoint 커넥터 사용" 
   description="논리 앱에서 SharePoint 커넥터 사용" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# 논리 앱에서 SharePoint 커넥터 사용

논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. Microsoft SharePoint 커넥터를 사용하여 Microsoft SharePoint Server/SharePoint Online에 연결하고 문서를 관리하고 항목을 나열할 수 있습니다. 문서에 대해 만들기, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행하고 항목을 나열할 수 있습니다. 온-프레미스 SharePoint 서버의 경우 서비스 버스 연결 문자열을 커넥터 구성의 일부로 제공하고, 온-프레미스 수신기 에이전트를 설치하여 서버에 연결할 수 있습니다.

SharePoint Online 커넥터 및 SharePoint Server 커넥터 갤러리 앱은 SharePoint를 조작하는 메커니즘으로 트리거 및 동작을 제공합니다.

## 논리 앱용 SharePoint Online 커넥터 만들기

SharePoint Online 커넥터를 사용하려면 먼저 SharePoint Online 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1. Azure 포털의 오른쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.

2. "웹 및 모바일 > API 앱"으로 이동하여 "SharePoint Online 커넥터"를 검색합니다.

3. SharePoint Online 커넥터를 구성하고 만들기를 클릭합니다. 다음은 커넥터를 만드는 데 제공해야 하는 매개 변수입니다.

	<table>
	  <tr>
	    <td><b>이름</b></td>
	    <td><b>필수 여부</b></td>
	    <td><b>설명</b></td>
	  </tr>
	  <tr>
	    <td>사이트 URL</td>
	    <td>예</td>
	    <td>SharePoint 웹 사이트의 전체 URL을 지정합니다. 예제: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>문서 라이브러리/목록 상대 URL</td>
	    <td>예</td>
	    <td>SharePoint 사이트 URL에 상대적이며 커넥터에서 수정할 수 있는 문서 라이브러리/목록 URL을 지정합니다. 예제: Lists/Task, Shared Documents</td>
	  </tr>
	</table>
	![][1]


4. 작업을 완료하면 이제 동일한 리소스 그룹에서 논리 앱을 만들어 SharePoint Online 커넥터를 사용할 수 있습니다.

## 논리 앱용 SharePoint Server 커넥터 만들기

SharePoint Server 커넥터를 사용하려면 먼저 SharePoint Server 커넥터 API 앱의 인스턴스를 만들어야 합니다. 이 작업은 다음과 같이 수행할 수 있습니다.

1. Azure 포털의 오른쪽 아래에 있는 + 새로 만들기 옵션을 사용하여 Azure 마켓플레이스를 엽니다.

2. "웹 및 모바일 > API 앱"으로 이동하여 "SharePoint Server 커넥터"를 검색합니다.

3. SharePoint Server 커넥터를 구성하고 만들기를 클릭합니다. 다음은 커넥터를 만드는 데 제공해야 하는 매개 변수입니다.

	<table>
	  <tr>
	    <td><b>이름</b></td>
	    <td><b>필수 여부</b></td>
	    <td><b>설명</b></td>
	  </tr>
	  <tr>
	    <td>사이트 URL</td>
	    <td>예</td>
	    <td>SharePoint 웹 사이트의 전체 URL을 지정합니다. 예제: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>인증 모드</td>
	    <td>예</td>
	    <td>SharePoint 사이트에 연결하기 위한 인증 모드를 지정합니다. 허용된 값은 다음과 같습니다.<br><br>
			기본값<br>
			OAuth2<br>
			WindowsAuthentication<br>
			FormBasedAuthentication<br><br>
	
	기본 자격 증명을 선택할 경우 SharePoint Microservice가 실행되는 기본 자격 증명이 사용되며 사용자 이름/암호는 필요하지 않습니다. 다른 인증 유형의 경우에는 사용자 이름 및 암호 필드를 입력해야 합니다. <br><br>참고: 익명 인증은 지원되지 않습니다.</td>
	  </tr>
	  <tr>
	    <td>사용자 이름</td>
	    <td>아니요</td>
	    <td>인증 모드가 기본값/OAuth2가 아닌 경우 SharePoint 사이트에 연결하려면 유효한 사용자 이름을 지정합니다.</td>
	  </tr>
	  <tr>
	    <td>암호</td>
	    <td>아니요</td>
	    <td>인증 모드가 기본값/OAuth2가 아닌 경우 SharePoint 사이트에 연결하려면 유효한 암호를 지정합니다.</td>
	  </tr>
	  <tr>
	    <td>문서 라이브러리/목록 상대 URL</td>
	    <td>예</td>
	    <td>SharePoint 사이트 URL에 상대적이며 커넥터에서 수정할 수 있는 문서 라이브러리/목록 URL을 지정합니다. 예제: Lists/Task, Shared Documents</td>
	  </tr>
	  <tr>
	    <td>서비스 버스 연결 문자열</td>
	    <td>아니요</td>
	    <td>이 문자열은 유효한 서비스 버스 네임스페이스 연결 문자열이어야 합니다.<br><br>
	
	SharePoint Server에 액세스할 수 있는 서버에 수신기 에이전트를 설치해야 합니다. <br>API 앱 통계 페이지로 이동하고 'Hybrid Connection'을 클릭하여 에이전트를 설치할 수 있습니다.</td>
	  </tr>
	</table>


	![][2]

4. 작업을 완료하면 이제 동일한 리소스 그룹에서 논리 앱을 만들어 SharePoint Server 커넥터를 사용할 수 있습니다.
5. SharePoint Server에 액세스할 수 있는 서버에 수신기 에이전트를 설치해야 합니다. API 앱 통계 페이지로 이동하고 'Hybrid Connection'을 클릭하여 에이전트를 설치할 수 있습니다.

## 논리 앱에서 SharePoint 커넥터 사용

API 앱을 만들면 이제 SharePoint 커넥터를 논리 앱의 트리거 또는 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.

1. 새 논리 앱을 만들고 SharePoint 커넥터가 있는 동일한 리소스 그룹을 선택합니다.

2. "트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. SharePoint 커넥터가 오른쪽에 있는 갤러리의 "최근에 사용됨" 섹션에 표시됩니다. 해당 커넥터를 선택합니다.

3. 논리 앱의 시작에서 SharePoint 커넥터를 선택한 경우 트리거처럼 작동합니다. 그렇지 않으면 커넥터를 사용하여 SharePoint 계정에서 작업을 수행할 수 있습니다. 

4. SharePoint Online 커넥터를 사용한 경우 또는 SharePoint Server 커넥터에서 인증이 OAuth2인 경우 사용자 대신 작업을 수행하려면 논리 앱을 인증하고 논리 앱에 권한을 부여해야 합니다. 권한 부여를 시작하려면 SharePoint 커넥터에서 권한 부여를 클릭합니다. 

	![][3]

5. 권한 부여를 클릭하면 SharePoint의 인증 대화 상자가 열립니다. 작업을 수행하려는 SharePoint 계정의 로그인 세부 정보를 제공합니다. 

	![][4]
6. 논리 앱에 계정에 대한 액세스 권한을 부여하여 사용자를 대신하여 작업을 수행하도록 합니다. 

	![][5]

7. SharePoint 커넥터를 트리거로 구성한 경우 트리거가 표시되고, 그렇지 않으면 동작 목록이 표시되며 이 목록에서 수행하려는 적절한 작업을 선택할 수 있습니다.  

	![][6]

	<b>문서 bibrary에 대해 구성된 상대 URL</b><br><br>

	![][7]

	<b>문서 목록에 대해 구성된 상대 URL</b>

	<b>참고:</b> 아래 트리거의 경우 사용자가 커넥터 패키지 설정에서 'Shared Documents, Lists/Task'를 지정한 경우 'Shared Documents'는 문서 라이브러리이고 'Lists/Task'는 목록이라고 가정합니다. 

##  트리거
논리 앱을 시작하려는 경우 트리거를 사용합니다. 

### 1.	공유 문서의 새 문서(JSON)
이 트리거는 'Shared Documents'에서 새 문서를 사용할 수 있는 경우 발생합니다. 

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>뷰 이름</td>
    <td>아니요</td>
    <td>선택할 문서를 필터링하는 데 사용되는 유효한 뷰를 지정합니다. 예제: 'Approved Orders'. 기존의 모든 문서를 처리하려면 이 필드를 비워 둡니다. </td>
  </tr>
  <tr>
    <td>보관 위치</td>
    <td>아니요</td>
    <td>SharePoint 사이트에 상대적이며 처리된 문서가 보관되는 유효한 폴더 URL을 지정합니다. </td>
  </tr>
  <tr>
    <td>보관의 덮어쓰기</td>
    <td>아니요</td>
    <td>보관 경로의 파일이 이미 있는 경우 해당 파일을 덮어쓰려면 이 옵션을 선택합니다. </td>
  </tr>
  <tr>
    <td>Caml 쿼리</td>
    <td>아니요, 고급</td>
    <td>문서를 필터링하려면 유효한 Caml 쿼리를 지정합니다. 예제: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**출력:**
<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>이름  </td>
    <td>문서의 이름입니다.</td>
  </tr>
  <tr>
    <td>콘텐츠</td>
    <td>문서의 내용입니다.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</
  </tr>
</table>


참고: 문서 항목의 모든 열은 'Advanced' 출력 속성에 표시됩니다.


### 2. 작업의 새 항목(JSON)
이 트리거는 새 항목이 'Tasks' 목록에 추가된 경우 발생합니다.

**입력:**
<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
 </tr>
  <tr>
    <td>뷰 이름</td>
    <td>아니요</td>
    <td>목록에서 항목을 필터링하는 데 사용되는 유효한 뷰를 지정합니다. 예제: 'Approved Orders'. 새로운 모든 항목을 처리하려면 이 필드를 비워 둡니다. </td>
  </tr>
  <tr>
    <td>보관 위치</td>
    <td>아니요</td>
    <td>SharePoint 사이트에 상대적이며 처리된 목록 항목이 보관되는 유효한 폴더 URL을 지정합니다. </td>
  </tr>
  <tr>
    <td>Caml 쿼리</td>
    <td>아니요, 고급</td>
    <td>목록 항목을 필터링하려면 유효한 Caml 쿼리를 지정합니다. 예제: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>목록의 열이 동적으로 채워지고 출력 매개 변수에 표시됩니다.</td>
    <td> </td>
  </tr>

</table>


### 3. 공유 문서의 새 문서(XML)

이 트리거는 'Shared Documents'에서 새 문서를 사용할 수 있는 경우 발생합니다. 새 문서가 XML 메시지로 반환됩니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>뷰 이름</td>
    <td>아니요</td>
    <td>선택할 문서를 필터링하는 데 사용되는 유효한 뷰를 지정합니다. 예제: 'Approved Orders'. 기존의 모든 문서를 처리하려면 이 필드를 비워 둡니다. </td>
  </tr>
  <tr>
    <td>보관 위치</td>
    <td>아니요</td>
    <td>SharePoint 사이트에 상대적이며 처리된 문서가 보관되는 유효한 폴더 URL을 지정합니다. </td>
  </tr>
  <tr>
    <td>보관의 덮어쓰기</td>
    <td>아니요</td>
    <td>보관 경로의 파일이 이미 있는 경우 해당 파일을 덮어쓰려면 이 옵션을 선택합니다. </td>
  </tr>
  <tr>
    <td>Caml 쿼리</td>
    <td>아니요, 고급</td>
    <td>문서를 필터링하려면 유효한 Caml 쿼리를 지정합니다. 예제: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>콘텐츠</td>
    <td>문서의 내용입니다.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</td>
  </tr>
</table>


### 4. 작업의 새 항목(XML)

이 트리거는 새 항목이 'Tasks' 목록에 추가된 경우 발생합니다. 새 목록 항목이 XML 메시지로 반환됩니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>뷰 이름</td>
    <td>아니요</td>
    <td>목록에서 항목을 필터링하는 데 사용되는 유효한 뷰를 지정합니다. 예제: 'Approved Orders'. 새로운 모든 항목을 처리하려면 이 필드를 비워 둡니다. </td>
  </tr>
  <tr>
    <td>보관 위치</td>
    <td>아니요</td>
    <td>SharePoint 사이트에 상대적이며 처리된 목록 항목이 보관되는 유효한 폴더 URL을 지정합니다. </td>
  </tr>
  <tr>
    <td>Caml 쿼리</td>
    <td>아니요, 고급</td>
    <td>목록 항목을 필터링하려면 유효한 Caml 쿼리를 지정합니다. 예제: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>콘텐츠</td>
    <td>문서의 내용입니다.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</td>
  </tr>
</table>


##  작업
아래 작업의 경우 사용자가 커넥터 패키지 설정에서 'Shared Documents, Lists/Task'를 지정한 경우 'Shared Documents'는 문서 라이브러리이고 'Lists/Task'는 목록이라고 가정합니다. 

### 1. 공유 문서에 업로드(JSON)

이 작업은 새 문서를 'Shared Documents'에 업로드합니다. 입력은 문서 라이브러리의 모든 열 필드가 있는 강력한 형식의 JSON 개체입니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
 </tr>
  <tr>
    <td>이름</td>
    <td>예</td>
    <td>문서의 이름입니다.</td>
  </tr>
  <tr>
    <td>콘텐츠</td>
    <td>예</td>
    <td>문서의 내용입니다.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>예</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</td>
  </tr>
  <tr>
    <td>무조건 덮어쓰기</td>
    <td>예</td>
    <td>TRUE로 설정했는데 지정된 이름의 문서가 존재하는 경우 해당 문서를 덮어씁니다.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>예</td>
    <td>문서 라이브러리에서 문서를 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>예</td>
    <td>문서 라이브러리에서 문서를 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>아니요. 고급</td>
    <td>문서 라이브러리에서 문서를 추가하기 위한 선택적 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>아니요. 고급</td>
    <td>문서 라이브러리에서 문서를 추가하기 위한 선택적 매개 변수 중 하나입니다.</td>
  </tr>
</table>

<b>참고:</b> 문서 라이브러리의 모든 매개 변수는 동적으로 채워집니다. 필수 매개 변수가 표시되며 선택적 매개 변수는 고급 섹션에 있습니다.


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>문서 라이브러리에 추가된 문서의 ItemId입니다.</td>
  </tr>
  <tr>
    <td>상태</td>
    <td>문서가 제대로 업로드되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


 

### 2. 공유 문서에서 가져오기(JSON)
이 작업은 문서의 상대 URL(폴더 구조)을 지정한 문서 라이브러리에서 문서를 가져옵니다.


**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>문서 상대 URI</td>
    <td>아니요</td>
    <td>이 매개 변수는 'Shared Documents'에 상대적인 문서 URL을 지정합니다. 예제: myspec1,myfolder/orders</td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>콘텐츠</td>
    <td>문서 내용</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</td>
  </tr>
  <tr>
    <td>상태</td>
    <td>작업이 제대로 실행되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>문서 라이브러리에서 문서의 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>문서 라이브러리에서 문서의 매개 변수 중 하나입니다.</td>
  </tr>
</table>

<b>참고:</b> 문서 라이브러리의 모든 매개 변수는 동적으로 채워지며, 고급 섹션에 있습니다.

 

### 3. 공유 문서에서 삭제

이 작업은 문서의 상대 URL(폴더 구조)을 지정한 문서 라이브러리에서 문서를 삭제합니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>문서 상대 URI</td>
    <td>아니요</td>
    <td>이 매개 변수는 'Shared Documents'에 상대적인 문서 URL을 지정합니다. 예제: myspec1,myfolder/orders</td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>상태</td>
    <td>작업이 제대로 실행되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 4. 작업에 삽입(JSON)

이 작업은 항목 목록에서 항목을 추가합니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>예</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>예</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>아니요. 고급</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>아니요. 고급</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
</table>


<b>참고:</b> 'List'의 모든 매개 변수는 동적으로 채워집니다. 필수 매개 변수는 표시되며, 선택적 매개 변수는 고급 섹션에 있습니다.

 
**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>추가된 목록 항목의 ItemId입니다.</td>
  </tr>
  <tr>
    <td>상태</td>
    <td>목록 항목이 제대로 삽입되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 5. 작업 업데이트(JSON)

이 작업은 항목 목록에서 항목을 업데이트합니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>예</td>
    <td>목록 항목의 ItemId입니다.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>아니요</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>아니요</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>아니요. 고급</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>아니요. 고급</td>
    <td>목록에서 항목을 추가하는 데 필요한 매개 변수 중 하나입니다.</td>
  </tr>
</table>

<b>참고:</b> 'List'의 모든 매개 변수는 동적으로 채워집니다. 필수 매개 변수는 표시되며, 선택적 매개 변수는 고급 섹션에 있습니다.


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>목록 항목이 제대로 업데이트되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 6. 작업에서 항목 가져오기(JSON)

이 작업은 항목 목록에서 항목을 가져옵니다.


**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>예</td>
    <td>목록 항목의 ItemId입니다.</td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>목록의 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>목록의 매개 변수 중 하나입니다.</td>
  </tr>
  <tr>
    <td>상태</td>
    <td>작업이 제대로 실행되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>

<b>참고:</b> 목록의 열이 동적으로 채워지고 출력 매개 변수에 표시됩니다.


### 7. 작업에서 항목 삭제

이 작업은 항목 목록에서 항목을 삭제합니다.

 
**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>예</td>
    <td>목록 항목의 ItemId입니다.</td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>목록 항목이 제대로 삭제되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 8. 공유 문서 나열(JSON)

이 작업은 문서 라이브러리의 모든 문서를 나열합니다. 뷰 또는 Caml 쿼리를 사용하여 문서를 필터링할 수 있습니다.  

 
**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>뷰 이름</td>
    <td>아니요</td>
    <td>선택할 문서를 필터링하는 데 사용되는 유효한 뷰를 지정합니다. 예제: 'Approved Orders'. 기존의 모든 문서를 처리하려면 이 필드를 비워 둡니다. </td>
  </tr>
  <tr>
    <td>Caml 쿼리</td>
    <td>아니요</td>
    <td>문서를 필터링하려면 유효한 Caml 쿼리를 지정합니다. 예제: <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>문서</td>
    <td>모든 문서의 배열입니다. 각 문서에는 아래 필드가 있습니다. <br><br>
	문서 []<br>
	이름<br>
	항목 ID<br>
	항목 전체 URL<br>
	고급<br>
	항목 편집 URL<br>
	목록 이름<br>
	목록 전체 URL<br>
	</td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>목록 항목이 제대로 삽입되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 9. 공유 문서에 업로드(XML)

이 작업은 새 문서를 'Shared Documents'에 업로드합니다. 입력 문서는 XML 페이로드이어야 합니다. 작업의 응답은 XML 페이로드입니다.
 

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>이름</td>
    <td>예</td>
    <td>문서의 이름입니다.</td>
  </tr>
  <tr>
    <td>콘텐츠</td>
    <td>예</td>
    <td>문서의 내용입니다.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>예</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</td>
  </tr>
  <tr>
    <td>무조건 덮어쓰기</td>
    <td>예</td>
    <td>TRUE로 설정했는데 지정된 이름의 문서가 존재하는 경우 해당 문서를 덮어씁니다.</td>
  </tr>
</table>
 

**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>출력 XML</td>
    <td>XML 형식의 업로드 작업 응답입니다.</td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>문서가 제대로 업로드되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>

### 10. 공유 문서에서 가져오기(XML)

이 작업은 문서의 상대 URL(폴더 구조)을 지정한 문서 라이브러리에서 문서를 가져옵니다.

 
**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>문서 상대 URI</td>
    <td>아니요</td>
    <td>이 매개 변수는 'Shared Documents'에 상대적인 문서 URL을 지정합니다. 예제: myspec1,myfolder/orders</td>
  </tr>
  <tr>
    <td>파일 형식</td>
    <td>예</td>
    <td>파일이 이진 파일인지 텍스트 파일인지를 지정합니다.</td>
  </tr>
</table>


**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>출력 XML</td>
    <td>문서 내용</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>메시지의 콘텐츠 전송 인코딩입니다. ("없음"|"base64")</td>
  </tr>
  <tr>
    <td>상태</td>
    <td>작업이 제대로 실행되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>

### 11. 작업에 삽입(XML)

이 작업은 항목 목록에서 항목을 추가합니다. 입력은 XML 페이로드이어야 합니다.

**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>입력 XML</td>
    <td>예</td>
    <td>삽입될 목록 항목의 필드 값을 포함하는 XML 메시지입니다. XML 메시지를 생성하는 데 변환 API 앱을 사용할 수 있습니다.</td>
  </tr>
</table>
 
<b>참고:</b> 'List'의 모든 매개 변수는 동적으로 채워집니다. 필수 매개 변수는 표시되며, 선택적 매개 변수는 고급 섹션에 있습니다.

 
**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>추가된 목록 항목의 ItemId입니다.</td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>목록 항목이 제대로 삽입되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 12. 작업 업데이트(XML)

이 작업은 항목 목록에서 항목을 업데이트합니다. 입력은 XML 페이로드이어야 합니다.


**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>예</td>
    <td>목록 항목의 ItemId입니다.</td>
  </tr>
  <tr>
    <td>입력 XML</td>
    <td>예</td>
    <td>삽입될 목록 항목의 필드 값을 포함하는 XML 메시지입니다. XML 메시지를 생성하는 데 변환 API 앱을 사용할 수 있습니다.</td>
  </tr>
</table>

<b>참고:</b> 'List'의 모든 매개 변수는 동적으로 채워집니다. 필수 매개 변수는 표시되며, 선택적 매개 변수는 고급 섹션에 있습니다.

 
**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>목록 항목이 제대로 업데이트되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


### 13. 작업에서 항목 가져오기(XML)

이 작업은 항목 목록에서 항목을 가져옵니다.


**입력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>필수 여부</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>예</td>
    <td>목록 항목의 ItemId입니다.</td>
  </tr>
</table>

**출력:**

<table>
  <tr>
    <td><b>이름</b></td>
    <td><b>설명</b></td>
  </tr>
  <tr>
    <td>출력 XML</td>
    <td>선택된 목록 항목의 필드 값을 포함하는 XML 메시지입니다. </td>
  </tr>
  <tr>
    <td>상태  </td>
    <td>작업이 제대로 실행되면 상태 코드 200(정상)이 반환됩니다.</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=49-->