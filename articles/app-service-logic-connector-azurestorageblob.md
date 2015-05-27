<properties 
   pageTitle="Azure Storage Blob Connector" 
   description="Azure Storage Blob Connector 시작" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="anuragdalmia" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
#Azure Storage Blob Connector

##개요
Azure Storage Blob Connector를 사용하면 Blob 컨테이너의 Blob을 업로드, 다운로드 및 삭제할 수 있습니다.

##새 Azure Storage Blob Connector 만들기
새 Azure Storage Connector를 만들려면 아래에 설명된 단계를 따르세요. <ul> <li>Azure 포털을 시작합니다. <li>+새로 만들기(페이지 아래쪽에 있음) -> 웹 + 모바일 -> Azure 마켓플레이스를 사용하여 Azure 마켓플레이스를 엽니다. </ul>

![Azure 마켓플레이스 시작][1]<br> <ul> <li>API 앱을 클릭합니다. <li><i>Blob</i>을 검색하고 Azure 저장소 Blob 커넥터를 선택합니다. </ul>

![Azure 저장소 Blob 커넥터 선택][2] <br> <ul> <li>만들기를 클릭합니다. <li>열리는 Azure 저장소 Blob 커넥터 블레이드에서 다음 데이터를 입력합니다. </ul>

![Azure 저장소 Blob 커넥터 만들기][3]

- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
- **구독** - 이 커넥터를 만들 구독을 선택합니다.
- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
- **이름** - Blob 저장소 커넥터의 이름을 지정합니다.
- **패키지 설정** 
	- **컨테이너/SAS URI** - Blob 컨테이너의 URI를 지정합니다. URI는 SAS 토큰을 포함할 수도 있습니다. 예: http://storageaccountname.blob.core.windows.net/containername 또는 http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **액세스 키** - 유효한 기본/보조 저장소 계정 액세스 키를 지정합니다. 인증에 SAS 토큰을 사용하는 경우 이 필드를 비워 둡니다.
- 만들기를 클릭합니다. 새 Azure Storage Blob Connector가 만들어집니다.

##논리 앱에 Azure Storage Blob Connector 사용
Azure Storage Blob Connector를 만든 후 흐름에서 사용할 수 있습니다.

\+새로 만들기 -> 웹 + 모바일 -> 논리 앱을 통해 새 흐름을 만듭니다. 리소스 그룹을 비롯하여 흐름에 대한 메타데이터를 입력합니다.

![논리 앱 만들기][4]

*트리거 및 동작*을 클릭합니다. 흐름 디자이너가 열립니다.

![논리 앱 빈 흐름 디자이너][5]

Azure 저장소 Blob 커넥터는 동작으로 사용할 수 있습니다.

###동작
오른쪽 창에서 Azure Storage Blob Connector를 클릭합니다. 커넥터에 지원되는 동작이 나열됩니다.

![Azure 저장소 Blob 동작 목록][10]

Azure Storage Blob Connector는 4가지 동작을 지원합니다. 이러한 동작은 다음과 같습니다.

- **Get Blob(Blob 가져오기)** - 컨테이너에서 특정 Blob을 가져옵니다.
- **Upload Blob(Blob 업로드)** - 새 Blob을 업로드하거나 기존 Blob을 업데이트합니다.
- **Delete Blob(Blob 삭제)** - 컨테이너에서 특정 Blob을 삭제합니다.
- **List Blobs(Blob 나열)** - 디렉터리의 모든 Blob을 나열합니다.
- **Snapshot Blob(Blob 스냅숏 만들기)** - 특정 Blob의 읽기 전용 스냅숏을 만듭니다.
- **Copy Blob(Blob 복사)** - 다른 Blob에서 복사하여 새 Blob을 만듭니다. 원본 Blob은 동일한 계정 또는 다른 계정에 있을 수 있습니다.

Blob 업로드를 한 가지 예로 들겠습니다. Upload Blob(Blob 업로드)을 클릭합니다.

![Blob 업로드 동작의 입력][11]


- **Blob Path(Blob 경로)** - 업로드할 Blob의 경로를 지정합니다. 경로는 구성된 컨테이너 경로에 대해 상대적으로 해석됩니다.
- **Blob Write Content(Blob 콘텐츠 작성)** - 업로드할 Blob의 콘텐츠 및 속성을 지정합니다.
- **Content Transfer Encoding(콘텐츠 전송 인코딩)** - none(없음) 또는 base64를 지정합니다.
- **Overwrite(덮어쓰기)** - true로 설정된 경우 기존 Blob을 덮어씁니다. 그렇지 않은 경우 Blob이 동일한 경로에 이미 있으면 오류를 반환합니다.

입력을 입력하고 확인 표시를 클릭하여 입력 구성을 완료합니다.


구성된 Azure 저장소 Blob Blob 업로드 동작에는 입력 매개 변수와 출력 매개 변수가 둘 다 표시되어 있습니다.

####이전 동작의 출력을 Azure 저장소 Blob 동작에 대한 입력으로 사용
구성된 스크린샷에서 Content는 식으로 설정된 값입니다.

	@triggers().outputs.body.Content


Content를 원하는 값으로 설정할 수 있습니다. 이것은 예로 든 것일 뿐입니다. 식은 논리 앱 트리거의 출력을 업로드할 파일의 내용으로 사용합니다. 이전 동작(예: 변환)의 출력을 사용한다고 가정해 보겠습니다. 이 경우 식은 다음과 같습니다.

	@actions('transformservice').outputs.body.OutputXML


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-azurestorageblob/LaunchAzureMarketplace.PNG
[2]: ./media/app-service-logic-connector-azurestorageblob/SelectAzureStorageBlobConnector.PNG
[3]: ./media/app-service-logic-connector-azurestorageblob/CreateAzureStorageBlobConnector.PNG
[4]: ./media/app-service-logic-connector-azurestorageblob/CreateLogicApp.PNG
[5]: ./media/app-service-logic-connector-azurestorageblob/LogicAppEmptyFlowDesigner.PNG
[6]: ./media/app-service-logic-connector-azurestorageblob/ChooseBlobAvailableTrigger.PNG
[7]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsBlobAvailableTrigger.PNG
[8]: ./media/app-service-logic-connector-azurestorageblob/AdvancedInputsBlobAvailableTrigger.PNG
[9]: ./media/app-service-logic-connector-azurestorageblob/ConfiguredBlobAvailableTrigger.PNG
[10]: ./media/app-service-logic-connector-azurestorageblob/ListOfAzureStorageBlobActions.PNG
[11]: ./media/app-service-logic-connector-azurestorageblob/BasicInputsUploadBlob.PNG

<!--HONumber=54-->