<properties
   pageTitle="논리 앱에서 Box 커넥터 사용 | Microsoft Azure 앱 서비스"
   description="Box 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
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
   ms.date="08/23/2015"
   ms.author="andalmia"/>

# Box 커넥터 시작 및 논리 앱에 추가 
Box에 연결하여 파일을 가져오고, 업로드, 삭제 등을 수행합니다. 커넥터는 논리 앱에서 "워크플로"의 일부로 사용됩니다.

방화벽 외부에 있는 사용자와도 안전하게 데이터를 공유할 수 있게 해주는 Box를 사용해야 하는 시나리오가 있을 수 있습니다. 논리 앱은 다양한 데이터 원본을 기반으로 트리거하고 흐름의 일부로 데이터를 가져오고 처리하기 위한 커넥터를 제공할 수 있습니다.


## 트리거 및 작업
Box 갤러리 앱은 다음과 같이 Box와 상호 작용하기 위한 메커니즘으로 동작을 제공합니다.

**동작**: 동작을 사용하여 논리 앱으로 구성된 Box 계정에 대해 미리 정의된 동작을 수행할 수 있습니다. Box Connector를 사용하여 Box 계정에 대해 수행할 수 있는 동작은 다음과 같습니다.

a. *파일 나열*: 이 작업은 폴더의 모든 파일에 대한 정보를 반환합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

매개 변수 이름 | 설명 | 필수
--- | --- | ---
폴더 경로 | 나열할 폴더의 경로입니다. | 예

> [AZURE.NOTE]파일 내용은 반환하지 않습니다.

b. *파일 가져오기*: 이 작업은 내용 및 속성을 비롯하여 파일을 검색합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

매개 변수 이름 | 설명 | 필수
--- | --- | ---
File Path(파일 경로) | 파일이 있는 폴더의 경로입니다. | 예
파일 형식 | 파일이 텍스트인지 또는 이진인지를 지정합니다. | 아니요

> [AZURE.NOTE]이 작업에서는 파일을 읽은 후 삭제하지 않습니다.


c. *파일 업로드*: 이름에서 알 수 있듯이 이 동작은 파일을 Box 계정에 업로드합니다. 파일이 이미 있는 경우 파일을 덮어쓰지 않고 오류가 발생합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

매개 변수 이름 | 설명 | 필수
--- | --- | ---
File Path(파일 경로) | 파일의 경로입니다. | 예
File Content(파일 내용) | 업로드할 파일 내용입니다. | 예
Content Transfer Encoding(콘텐츠 전송 인코딩) | 콘텐츠의 인코딩 형식이며, Base64 또는 None(없음)입니다. | 

d. *파일 삭제*: 이 동작은 폴더에서 지정된 파일을 삭제합니다. 파일/폴더를 찾을 수 없는 경우 예외가 발생합니다. 동작에 필요한 매개 변수 목록은 다음과 같습니다.

매개 변수 이름 | 설명 | 필수
--- | --- | ---
File Path(파일 경로) | 폴더를 포함하는 파일 경로를 완료합니다. | 예


## 논리 앱에 대한 Box 커넥터 만들기

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. "Box 커넥터"를 검색하여 선택하고 **만들기**를 선택합니다.
3. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다. ![][1]
4. **만들기**를 선택합니다.


## 논리 앱에 Box Connector 사용

API 앱을 만들고 나면 이제 Box 커넥터를 논리 앱의 동작으로 사용할 수 있습니다. 다음을 수행합니다.

1. 논리 앱에서 **트리거 및 동작**을 열어 논리 앱 디자이너를 열고 흐름을 구성합니다. 갤러리에 Box 커넥터가 나열됩니다. 커넥터를 선택하면 논리 앱 디자이너에 자동으로 추가됩니다.

	> [AZURE.NOTE]논리 앱의 시작 부분에 Box 커넥터를 선택하면 트리거처럼 작동합니다. 그렇지 않은 경우 커넥터를 사용하는 Box 계정에서 작업을 수행할 수 있습니다. 이 문서의 작성 시점에 Box 커넥터에는 트리거가 없습니다.

2. 사용자를 대신하여 작업을 수행하도록 논리 앱을 인증하고 논리 앱에 권한을 부여하세요. Box 커넥터에서 **권한 부여**를 선택합니다. ![][2]

3. 작업을 수행하려는 Box 계정의 로그인 세부 정보를 입력합니다. ![][3]

4. 논리 앱에 계정에 대한 액세스 권한을 부여하여 사용자를 대신하여 작업을 수행하도록 합니다. ![][4]

5. 동작 목록이 표시되며 수행하려는 적절한 작업을 선택할 수 있습니다. ![][5]

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=Oct15_HO3-->