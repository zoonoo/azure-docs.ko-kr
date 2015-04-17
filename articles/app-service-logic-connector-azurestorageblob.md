<properties 
   pageTitle="Azure Storage Blob Connector" 
   description="Azure Storage Blob Connector 시작" 
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
   ms.date="03/18/2015"
   ms.author="rajram"/>
   
# Azure Storage Blob Connector

## 개요
Azure Storage Blob Connector를 사용하면 Blob 컨테이너의 Blob을 업로드, 다운로드 및 삭제할 수 있습니다.

## 새 Azure Storage Blob Connector 만들기
새 Azure Storage Connector를 만들려면 아래에 설명된 단계를 따르세요.
- Azure 포털을 시작합니다.
- +새로 만들기(페이지 아래쪽에 있음) -> 웹 + 모바일 -> Azure 마켓플레이스를 사용하여 Azure 마켓플레이스를 엽니다.

![Launch Azure Marketplace][1]
- API 앱을 클릭합니다.
- _Blob_을 검색하고 Azure Storage Blob Connector를 선택합니다.

![Select Azure Storage Blob Connector][2]
- 만들기를 클릭합니다.
- 열리는 Azure Storage Blob Connector 블레이드에서 다음 데이터를 입력합니다.

![Create Azure Storage Blob Connector][3]

- **위치** - 커넥터를 배포할 지리적 위치를 선택합니다.
- **구독** - 이 커넥터를 만들 구독을 선택합니다.
- **리소스 그룹** - 커넥터가 상주할 리소스 그룹을 선택하거나 만듭니다.
- **웹 호스팅 계획** - 웹 호스팅 계획을 선택하거나 만듭니다.
- **가격 책정 계층** - 커넥터에 대한 가격 책정 계층을 선택합니다.
- **이름** - FTP Connector의 이름을 지정합니다.
- **패키지 설정** 
	- **컨테이너/SAS URI** - Blob 컨테이너의 URI를 지정합니다. URI는 SAS 토큰을 포함할 수 있습니다. 예: http://storageaccountname.blob.core.windows.net/containername 또는 http://storageaccountname.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah
	- **액세스 키** - 유효한 기본/보조 저장소 계정 액세스 키를 지정합니다. 인증에 SAS 토큰을 사용하는 경우 이 필드를 비워 둡니다.
- 만들기를 클릭합니다. 새 Azure Storage Blob Connector가 만들어집니다.

## 논리 앱에 Azure Storage Blob Connector 사용
Azure Storage Blob Connector를 만든 후 흐름에서 사용할 수 있습니다.

+새로 만들기 -> 웹 + 모바일 -> 논리 앱을 통해 새 흐름을 만듭니다. 리소스 그룹을 비롯하여 흐름에 대한 메타데이터를 입력합니다.

![Create Logic App][4]

 *triggers and actions트리거 및 동작*을 클릭합니다. 흐름 디자이너가 열립니다.

![Logic App empty flow designer][5]

Azure Storage Blob Connector는 트리거와 동작 둘 다로 사용할 수 있습니다. 

### 트리거
빈 흐름 디자이너의 오른쪽 갤러리 창에서 Azure Storage Blob Connector를 클릭합니다.

![Choose Blob Available Trigger][6]

Azure Storage Blob Connector에는 트리거 하나 _BlobAvailable_이 있습니다. 이 트리거는 지정된 컨테이너에 있는 Blob을 폴링합니다. Blob의 디렉터리 수준 폴링 및 필터링도 지원됩니다. 이 트리거는 Blob을 선택한 후에는 컨테이너에서 해당 Blob을 삭제합니다.

_BlobAvailable_ 트리거를 클릭합니다.

![Basic inputs Blob Available Trigger][7]

입력은 예약된 빈도로 폴링되도록 특정 폴더 경로를 구성하는 데 도움이 됩니다. 기본 입력은 다음과 같습니다.
- Frequency(빈도) - FTP 폴링의 빈도를 지정합니다.
- Interval(간격) - 예약된 빈도에 대한 간격을 지정합니다.
- Folder Path(폴더 경로) - 폴링할 가상 폴더 경로를 지정합니다. 루트 컨테이너 폴더에는 '.'을 사용하세요.
- 파일 형식 - Blob 파일 이름에 대해 일치할 파일 마스크를 지정합니다.  이 파일 마스크와 일치하는 파일 이름이 포함된 Blob만 폴링에 포함됩니다. 기본적으로 모든 Blob이 포함됩니다.

...를 클릭하면 고급 입력이 표시됩니다. 

![Advanced inputs Blob Available Trigger][8]

고급 입력은 다음과 같습니다.

- File mask(파일 마스크) - Blob 파일 이름에 대해 일치할 파일 마스크를 지정합니다.  이 파일 마스크와 일치하는 파일 이름이 포함된 Blob만 폴링에 포함됩니다. 기본적으로 모든 Blob이 포함됩니다.
- Exclude file mask(파일 마스크 제외) - Blob 파일 이름에 대해 일치할 파일 마스크를 지정합니다.  이 파일 마스크와 일치하는 Blob은 제외됩니다. 기본적으로 아무 Blob도 제외되지 않습니다.

입력을 입력하고 확인 표시를 클릭하여 입력 구성을 완료합니다.

![Configured Blob Available Trigger][9]

구성된 _Blob Available_ 트리거에는 구성된 입력 매개 변수와 출력 매개 변수가 둘 다 표시되어 있습니다. 

논리 앱을 만들고 나면 _Blob Available_ 트리거가 다음을 수행합니다. 


- 새 파일에 대한 폴더 경로를 폴링합니다.
- 새 파일이 만들어질 때마다 논리 흐름을 인스턴스화합니다.
- 논리 흐름이 인스턴스화된 후 폴더 경로에서 파일을 삭제합니다.

#### 후속 동작에 Blob Available 트리거의 출력 사용
_Blob Available_ 트리거의 출력을 흐름에 있는 다른 동작의 입력으로 사용할 수 있습니다. 

동작의 입력 대화 상자에서 +를 클릭하고 드롭다운 상자에서 직접 FTP의 출력을 선택할 수 있습니다.

동작의 입력 상자에 직접 식을 작성할 수도 있습니다. 트리거의 출력을 참조하는 흐름 식은 다음과 같습니다.

	@triggers().outputs.body.Content

### 동작
오른쪽 창에서 Azure Storage Blob Connector를 클릭합니다. 커넥터에 지원되는 동작이 나열됩니다.

![List of Azure Storage Blob Actions][10]

Azure Storage Blob Connector는 4가지 동작을 지원합니다. 이러한 동작은 다음과 같습니다.

- **Get Blob(Blob 가져오기)** - 컨테이너에서 특정 Blob을 가져옵니다.
- **Upload Blob(Blob 업로드)** - 새 Blob을 업로드하거나 기존 Blob을 업데이트합니다.
- **Delete Blob(Blob 삭제)**  - 컨테이너에서 특정 Blob을 삭제합니다.
- **List Blobs(Blob 나열)** - 디렉터리의 모든 Blob을 나열합니다.
- **Snapshot Blob(Blob 스냅숏 만들기)** - 특정 Blob의 읽기 전용 스냅숏을 만듭니다.
- **Copy Blob(Blob 복사)** - 다른 Blob에서 복사하여 새 Blob을 만듭니다.  원본 Blob은 동일한 계정 또는 다른 계정에 있을 수 있습니다.

Blob 업로드를 한 가지 예로 들겠습니다. Upload Blob(Blob 업로드)을 클릭합니다.

![Inputs of Upload Blob action][11]


- **Blob Path(Blob 경로)** - 업로드할 Blob의 경로를 지정합니다.  경로는 구성된 컨테이너 경로에 대해 상대적으로 해석됩니다.
- **Blob Write Content(Blob 콘텐츠 작성)** - 업로드할 Blob의 콘텐츠 및 속성을 지정합니다.
- **Content Transfer Encoding(콘텐츠 전송 인코딩)** - none(없음) 또는 Base64를 지정합니다.
- **Overwrite(덮어쓰기)** - true로 설정된 경우 기존 Blob을 덮어씁니다. 그렇지 않은 경우 Blob이 동일한 경로에 이미 있으면 오류를 반환합니다.

입력을 입력하고 확인 표시를 클릭하여 입력 구성을 완료합니다.


구성된 Azure 저장소 Blob Blob 업로드 동작에는 입력 매개 변수와 출력 매개 변수가 둘 다 표시되어 있습니다.

#### 이전 동작의 출력을 FTP 동작에 대한 입력으로 사용
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

<!--HONumber=49-->