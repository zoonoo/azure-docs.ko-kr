<properties
	pageTitle="논리 앱에서 FTP 커넥터 사용 | Microsoft Azure 앱 서비스"
	description="FTP 커넥터 또는 API 앱을 만들어서 구성하고 Azure 앱 서비스의 논리 앱에서 사용하는 방법"
	authors="rajram"
	manager="dwrede"
	editor=""
	services="app-service\logic"
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/23/2015"
	ms.author="rajram"/>

# FTP 커넥터 시작 및 논리 앱에 추가
FTP 서버에 연결하여 데이터 또는 파일을 이동합니다. FTP 커넥터의 주요 기능은 다음과 같습니다.

- 요청 시 FTP 서버에서 파일 끌어오기
- 구성 가능한 일정을 기반으로 폴링 실행
- FTP 서버의 새 문서를 기반으로 FTP 서버 폴링 및 논리 흐름 트리거
- FTP 서버를 IP 주소, 포트, 암호 및 호스트 이름으로 지정
- 요청 시 보내기를 실행하는 기능
- 요청 시 FTP 서버에서 파일을 삭제하는 기능

FTP 커넥터를 비즈니스 워크플로에 추가하고 논리 앱 내에서 이 워크플로의 일부로 데이터를 처리할 수 있습니다.

## 새 FTP Connector 만들기
새 FTP 커넥터를 만들려면 아래에 설명된 단계를 따르세요. - Azure 포털을 시작합니다. - +새로 만들기(페이지 아래쪽에 있음) -> 웹 + 모바일 -> Azure 마켓플레이스를 사용하여 Azure 마켓플레이스를 엽니다.![Azure 마켓플레이스 시작][1]

- API 앱을 클릭합니다.
- FTP를 검색하고 FTP Connector를 선택합니다.![FTP 커넥터 선택][2]

- 만들기를 클릭합니다.
- 열리는 FTP Connector 블레이드에서 다음 데이터를 입력합니다.![FTP 커넥터 만들기][3]

- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
- **구독** - 이 커넥터를 만들 구독을 선택합니다.
- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
- **이름** - FTP 커넥터의 이름을 지정합니다.
- **패키지 설정**
	- **서버 주소** - FTP 서버 이름 또는 IP 주소를 지정합니다.
	- **사용자 이름** - FTP 서버에 연결할 때 사용할 사용자 이름을 지정합니다.
	- **암호** - FTP 서버에 연결할 때 사용할 암호를 지정합니다.
	- **루트 폴더** - 루트 폴더 경로를 지정합니다.
	- **이진 사용** - 이진 전송 모드를 사용하려면 true를 지정하고, ASCII를 사용하려면 false를 지정합니다.
	- **SSL 사용** - SSL/TLS 보안 채널을 통해 FTP를 사용하려면 true를 지정합니다.
	- **서버 포트** - FTP 서버 포트 번호를 지정합니다.
- 만들기를 클릭합니다. 새 FTP Connector가 만들어집니다.

## 논리 앱에 FTP Connector 사용
FTP Connector를 만든 후 흐름에서 사용할 수 있습니다.

\+새로 만들기 -> 웹 + 모바일 -> 논리 앱을 통해 새 흐름을 만듭니다. 리소스 그룹을 비롯하여 흐름에 대한 메타데이터를 입력합니다.![논리 앱 만들기][4]

*트리거 및 동작*을 클릭합니다. 흐름 디자이너가 열립니다.![논리 앱 빈 흐름 디자이너][5]

FTP Connector는 트리거와 동작 둘 다로 사용할 수 있습니다.

### 트리거
빈 흐름 디자이너의 오른쪽 갤러리 창에서 FTP Connector를 클릭합니다.![FTP 트리거 선택][6]

FTP 커넥터에는 하나의 트리거 ‘File Available(파일을 사용할 수 있는 경우)(읽은 후 삭제)’가 있습니다. 이 트리거는

- 새 파일에 대한 폴더 경로를 폴링합니다.
- 새 파일이 만들어질 때마다 논리 흐름을 인스턴스화합니다.
- 논리 흐름이 인스턴스화된 후 폴더 경로에서 파일을 삭제합니다.

‘File Available(파일을 사용할 수 있는 경우)(읽은 후 삭제)’ 트리거를 클릭합니다.![기본 입력 FTP 트리거][7]

입력은 예약된 빈도로 폴링되도록 특정 폴더 경로를 구성하는 데 도움이 됩니다. 기본 입력은 다음과 같습니다. - Frequency(빈도) - FTP 폴링의 빈도를 지정합니다. - Interval(간격) - 예약된 빈도에 대한 간격을 지정합니다. - Folder Path(폴더 경로) - FTP 서버의 폴더 경로를 지정합니다. - Type of file(파일 형식) - 파일 형식이 텍스트인지 또는 이진인지를 지정합니다.

말줄임표 ‘...’를 클릭하면 고급 입력이 표시됩니다.![기본 입력 FTP 트리거][8]

고급 입력은 다음과 같습니다. - File mask(파일 마스크) - 폴링하는 동안 파일 마스크를 지정합니다. - Exclude file mask(파일 마스크 제외) - 폴링하는 동안 제외할 파일 마스크를 지정합니다.

입력을 입력하고 확인 표시를 클릭하여 입력 구성을 완료합니다.![기본 입력 FTP 트리거][9]

구성된 FTP 트리거에는 구성된 입력 매개 변수와 출력이 둘 다 표시되어 있습니다.

#### 후속 동작에 FTP 트리거의 출력 사용
FTP Connector의 출력을 흐름에 있는 다른 동작의 입력으로 사용할 수 있습니다.

동작의 입력 대화 상자에서 ‘...’를 클릭하고 드롭다운 상자에서 직접 FTP의 출력을 선택할 수 있습니다.

동작의 입력 상자에 직접 식을 작성할 수도 있습니다. FTP 트리거의 출력을 참조하는 흐름 식은 다음과 같습니다.

	@triggers('ftpconnector').outputs.body.Content

### 동작
오른쪽 창에서 FTP Connector를 클릭합니다. FTP Connector에 지원되는 동작이 나열됩니다.![FTP 동작 목록][10]

FTP 커넥터는 다음 작업을 지원합니다.

- **Get File(파일 가져오기)** - 특정 파일의 내용을 가져옵니다.
- **Upload File(파일 업로드)** - FTP 폴더 경로에 파일을 업로드합니다.
- **Delete File(파일 삭제)** - FTP 폴더 경로에서 파일을 삭제합니다.
- **List Files(파일 나열)** - FTP 폴더 경로에 있는 모든 파일을 나열합니다.

파일 업로드를 한 가지 예로 들겠습니다. Upload File(파일 업로드)을 클릭합니다.

기본 입력이 먼저 표시됩니다.![파일 업로드 동작의 기본 입력][11]


- **Content(내용)** - 업로드할 파일의 내용을 지정합니다.
- **Content Transfer Encoding(콘텐츠 전송 인코딩)** - none(없음) 또는 base64를 지정합니다.
- **File Path(파일 경로)** - 업로드할 파일의 경로를 지정합니다.

...를 클릭하면 고급 입력이 표시됩니다.![파일 업로드 동작의 기본 입력][12]


- **Append If Exists(있는 경우 추가)** - ‘Append If Exists(있는 경우 추가)’의 true나 false를 선택합니다. 사용하도록 설정하는 경우, 파일이 있으면 데이터가 파일에 추가됩니다. 사용하지 않도록 설정하는 경우, 파일이 있으면 덮어씁니다.
- **Temporary Folder(임시 폴더)** - 선택 사항입니다. 제공되는 경우, 어댑터가 해당 파일을 ‘임시 폴더 경로’로 업로드하고 업로드가 완료되면 해당 파일이 ‘폴더 경로’로 이동됩니다. 이동 작업이 원자성을 지니도록 하려면 ‘임시 폴더 경로’가 ‘폴더 경로’와 동일한 실제 디스크에 있어야 합니다. 임시 폴더는 ‘있는 경우 추가’ 속성을 사용하지 않도록 설정한 경우에만 사용할 수 있습니다.

입력을 입력하고 확인 표시를 클릭하여 입력 구성을 완료합니다.![구성된 파일 업로드 동작][13]

‘File Path(파일 경로)’ 매개 변수는 다음과 같이 설정됩니다.

	@concat('/Output/',triggers().outputs.body.FileName)

구성된 FTP 파일 업로드 동작에는 입력 매개 변수와 출력 매개 변수가 둘 다 표시되어 있습니다.

#### 이전 동작의 출력을 FTP 동작에 대한 입력으로 사용
구성된 스크린샷에서 Content는 식으로 설정된 값입니다.

	@triggers().outputs.body.Content


Content를 원하는 값으로 설정할 수 있습니다. 이것은 예로 든 것일 뿐입니다. 식은 논리 앱 트리거의 출력을 업로드할 파일의 내용으로 사용합니다. 이전 동작(예: 변환)의 출력을 사용한다고 가정해 보겠습니다. 이 경우 식은 다음과 같습니다.

	@actions('transformservice').outputs.body.OutputXML

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

>[AZURE.NOTE]Azure 계정을 등록하기 전에 Azure 논리 앱을 시작하려는 경우 [논리 앱 평가](https://tryappservice.azure.com/?appservice=logic)로 이동합니다. 앱 서비스에서 즉시 단기 스타터 논리 앱을 만들 수 있습니다. 신용 카드는 필요하지 않으며 약정도 필요하지 않습니다.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!-- Image reference -->
[1]: ./media/app-service-logic-connector-ftp/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-ftp/SelectFTPConnector.PNG
[3]: ./media/app-service-logic-connector-ftp/CreateFTPConnector.PNG
[4]: ./media/app-service-logic-connector-ftp/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-ftp/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-ftp/ChooseFTPTrigger.PNG
[7]: ./media/app-service-logic-connector-ftp/BasicInputsFTPTrigger.PNG
[8]: ./media/app-service-logic-connector-ftp/AdvancedInputsFTPTrigger.PNG
[9]: ./media/app-service-logic-connector-ftp/ConfiguredFTPTrigger.PNG
[10]: ./media/app-service-logic-connector-ftp/ListOfFTPActions.PNG
[11]: ./media/app-service-logic-connector-ftp/BasicInputsUploadFile.PNG
[12]: ./media/app-service-logic-connector-ftp/AdvancedInputsUploadFile.PNG
[13]: ./media/app-service-logic-connector-ftp/ConfiguredUploadFile.PNG
 

<!---HONumber=Oct15_HO1-->