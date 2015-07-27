<properties
   pageTitle="논리 앱에 Box Connector 사용"
   description="논리 앱에 Box Connector 사용"
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
   ms.date="07/02/2015"
   ms.author="andalmia"/>

# 논리 앱에 Box Connector 사용



논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다. 방화벽 외부에 있는 사용자와도 안전하게 데이터를 공유할 수 있게 해주는 Box를 사용해야 하는 시나리오가 있을 수 있습니다.



Box 갤러리 앱은 다음과 같이 Box와 상호 작용하기 위한 메커니즘으로 동작을 제공합니다.



1. **동작**: 동작을 사용하여 논리 앱으로 구성된 Box 계정에 대해 미리 정의된 동작을 수행할 수 있습니다. Box Connector를 사용하여 Box 계정에 대해 수행할 수 있는 동작은 다음과 같습니다.

	a. *파일 나열*: 이 작업은 폴더의 모든 파일에 대한 정보를 반환합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

	<table>
  <tr>
    <td><b>매개 변수 이름</b></td>
    <td><b>설명</b></td>
    <td><b>필수</b></td>
  </tr>
  <tr>
    <td>폴더 경로</td>
    <td>나열을 수행할 폴더의 경로입니다.</td>
    <td>예</td>
  </tr>
</table>>[AZURE.NOTE]파일 내용은 반환하지 않습니다.



    b. *파일 가져오기*: 이 작업은 내용 및 속성을 비롯하여 파일을 검색합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

	<table>
  <tr>
    <td><b>매개 변수 이름</b></td>
    <td><b>설명</b></td>
    <td><b>필수</b></td>
  </tr>
  <tr>
    <td>File Path(파일 경로)</td>
    <td>파일이 있는 폴더의 경로입니다.</td>
    <td>예</td>
  </tr>
  <tr>
    <td>파일 형식</td>
    <td>파일이 텍스트인지 또는 이진인지를 지정합니다.</td>
    <td>아니요</td>
  </tr>
</table>>[AZURE.NOTE]이 작업에서는 파일을 읽은 후 삭제하지 않습니다.



    c. 파일 업로드: 이름에서 알 수 있듯이 이 동작은 파일을 Box 계정에 업로드합니다. 파일이 이미 있는 경우 파일을 덮어쓰지 않고 오류가 발생합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

	<table>
  <tr>
    <td><b>매개 변수 이름</b></td>
    <td><b>설명</b></td>
    <td><b>필수</b></td>
  </tr>
  <tr>
    <td>File Path(파일 경로)</td>
    <td>파일의 경로입니다.</td>
    <td>예</td>
  </tr>
  <tr>
    <td>File Content(파일 내용)</td>
    <td>업로드할 파일 내용입니다.</td>
    <td>예</td>
  </tr>
  <tr>
    <td>Content Transfer Encoding(콘텐츠 전송 인코딩)</td>
    <td>콘텐츠의 인코딩 유형이며, Base64 또는 None(없음)일 수 있습니다.</td>
    <td> </td>
  </tr>
</table>d. 파일 삭제: 이 동작은 폴더에서 지정된 파일을 삭제합니다. 파일/폴더를 찾을 수 없는 경우 예외가 발생합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

 	<table>
  <tr>
    <td><b>매개 변수 이름</b></td>
    <td><b>설명</b></td>
    <td><b>필수</b></td>
  </tr>
  <tr>
    <td>File Path(파일 경로)</td>
    <td>폴더를 포함하는 파일 경로를 완료합니다.</td>
    <td>예</td>
  </tr>
</table>




## 논리 앱에 대한 Box Connector 만들기

Box Connector를 사용하려면 먼저 Box Connector API 앱의 인스턴스를 만들어야 합니다. 논리 앱 디자이너 내부는 물론 외부에서도 할 수 있습니다. 디자이너 외부에서는 다음과 같이 하여 만들 수 있습니다.

1. Azure 포털 홈페이지에서 Azure Marketplace를 엽니다.

2. “모든 항목”에서 “Box 커넥터”를 검색합니다.

3. Box 커넥터를 구성하고 만들기를 클릭합니다.

	![][1]

4. 이 작업을 완료하고 나면 이제 동일한 리소스 그룹에 Box Connector를 사용하기 위한 논리 앱을 만들 수 있습니다.


## 논리 앱에 Box Connector 사용

API 앱을 만들고 나면 이제 Box Connector를 논리 앱의 동작으로 사용할 수 있습니다. 이렇게 하려면 다음을 수행해야 합니다.


1. 새 논리 앱을 만들고 Box Connector가 있는 것과 동일한 리소스 그룹을 선택합니다.

2. "트리거 및 동작"을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. Box Connector가 오른쪽에 있는 갤러리의 "Recently Used(최근에 사용한 항목)" 섹션에 나타납니다. 해당 커넥터를 선택합니다.

3. 논리 앱 시작 시 Box Connector가 선택된 경우 이 커넥터는 트리거 역할을 하고, 그렇지 않으면 커넥터를 사용하여 Box 계정에 대해 동작을 수행할 수 있습니다. 이 문서의 작성 시점에 Box 커넥터에는 트리거가 없습니다.

4. 첫 번째 단계는 논리 앱을 인증하고 논리 앱에 권한을 부여하여 사용자를 대신하여 작업을 수행하도록 하는 것입니다. 권한 부여를 시작하려면 Box Connector에서 권한 부여를 클릭합니다.

	![][2]

5. 권한 부여를 클릭하면 Box의 인증 대화가 열립니다. 작업을 수행하려는 Box 계정의 로그인 세부 정보를 입력합니다.

	![][3]

6. 논리 앱에 계정에 대한 액세스 권한을 부여하여 사용자를 대신하여 작업을 수행하도록 합니다.

	![][4]

7. 동작 목록이 표시되며 수행하려는 적절한 작업을 선택할 수 있습니다.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=July15_HO3-->