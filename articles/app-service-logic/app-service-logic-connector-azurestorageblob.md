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
   ms.date="08/19/2015"
   ms.author="rajram"/>
   
# Azure Storage Blob Connector
Azure Storage Blob에 연결하여 Blob 컨테이너의 Blob을 업로드, 다운로드 및 삭제할 수 있습니다. 커넥터는 논리 앱에서 "워크플로"의 일부로 사용될 수 있습니다.

## 트리거 및 작업
*트리거*는 발생하는 이벤트입니다. 예를 들어 주문이 업데이트되거나 새 고객이 추가되는 것이 트리거입니다. *작업*은 트리거의 결과입니다. 예를 들어 주문이 업데이트되면 영업 직원에게 경고를 보내는 것이 작업입니다. 또는 새 고객이 추가되면 새 고객에게 환영 전자 메일을 보냅니다.

저장소 Blob 커넥터는 논리 앱에서 동작으로 사용할 수 있으며 JSON 및 XML 형식의 데이터를 지원합니다. 현재 저장소 Blob 커넥터에 대한 트리거는 없습니다.

저장소 Blob 커넥터에서 사용할 수 있는 트리거와 동작은 다음과 같습니다.

트리거 | actions
--- | ---
없음 | <ul><li>Get Blob(Blob 가져오기) - 컨테이너에서 특정 Blob을 가져옵니다.</li><li>Upload Blob(Blob 업로드) - 새 Blob을 업로드하거나 기존 Blob을 업데이트합니다.</li><li>Delete Blob(Blob 삭제) - 컨테이너에서 특정 Blob을 삭제합니다.</li><li>List Blobs(Blob 나열) - 디렉터리의 모든 Blob을 나열합니다.</li><li>Snapshot Blob(Blob 스냅숏 만들기) - 특정 Blob의 읽기 전용 스냅숏을 만듭니다.</li><li>Copy Blob(Blob 복사) - 다른 Blob에서 복사하여 새 Blob을 만듭니다. 원본 Blob은 동일한 계정 또는 다른 계정에 있을 수 있습니다.</li></ul>


## Azure 저장소 Blob 커넥터 만들기

커넥터는 논리 앱 내에서 만들거나 Azure 마켓플레이스에서 직접 만들 수 있습니다. 마켓플레이스에서 커넥터를 만들려면

1. Azure 시작 보드에서 **마켓플레이스**를 선택합니다.
2. “Blob”를 검색합니다. ![Azure 저장소 Blob 커넥터 선택][2]

3. Blob를 선택하고 **만들기**를 선택합니다.
4. 이름, 앱 서비스 계획 및 기타 속성을 입력합니다.
5. 다음 패키지 설정을 입력합니다.

	이름 | 필수 | 설명
--- | --- | ---
컨테이너/SAS URI | 예 | Blob 컨테이너의 URI를 입력합니다. URI는 SAS 토큰을 포함할 수도 있습니다. 예를 들어 http://*storageaccountname*.blob.core.windows.net/containername 또는 http://*storageaccountname*.blob.core.windows.net/containername?sr=c&si=mypolicy&sig=signatureblah를 입력합니다.
액세스 키 | 아니요 | 유효한 기본 또는 보조 저장소 계정 액세스 키를 입력합니다. 인증에 SAS 토큰을 사용하는 경우 이 필드를 비워 둡니다.

	![Azure 저장소 Blob 커넥터 만들기][3]

6. **만들기**를 클릭합니다.

## 논리 앱에 Azure Storage Blob Connector 사용
Azure 저장소 Blob 커넥터를 만들었으면 이제 워크플로에 추가할 수 있습니다.

1. 새로 만들기 -> 웹 + 모바일 -> 논리 앱을 통해 새 논리 앱을 만듭니다. 논리 앱에 대한 속성을 입력합니다. ![논리 앱 만들기][4]

2. **트리거 및 동작**을 클릭합니다. 워크플로 디자이너가 열립니다. ![논리 앱 빈 흐름 디자이너][5]

3. 오른쪽 창에서 Azure 저장소 Blob 커넥터를 선택합니다. 커넥터에 사용할 수 있는 동작이 표시됩니다. ![Azure 저장소 Blob 동작 목록][10]

4. 이 시나리오에서는 **Blob 업로드** 동작을 사용해 보겠습니다. ![Blob 업로드 동작의 입력][11]

5. 입력 값을 입력하고 해당 확인 표시를 선택하여 구성을 완료합니다.

	입력 | 설명
--- | ---
Blob 경로 | 업로드할 Blob의 경로를 결정합니다. 경로는 구성된 컨테이너 경로에 대해 상대적으로 해석됩니다.
Blob 쓰기 콘텐츠 | 업로드할 Blob의 콘텐츠 및 속성을 입력합니다.
Content Transfer Encoding(콘텐츠 전송 인코딩) | 없음 또는 Base64를 입력합니다.
덮어쓰기 | true로 설정되면 기존 Blob이 덮어쓰여집니다. False로 설정된 경우 동일한 경로에 Blob이 이미 있는 경우 오류가 반환됩니다.

구성된 Azure 저장소 Blob Blob 업로드 동작에는 입력 매개 변수와 출력 매개 변수가 둘 다 표시되어 있습니다.

#### 이전 동작의 출력을 Azure 저장소 Blob 동작에 대한 입력으로 사용
이전 스크린샷에서**콘텐츠** 값은 식일 수 있습니다.

	@triggers().outputs.body.Content

Content를 원하는 값으로 설정할 수 있습니다. 식은 논리 앱 트리거의 출력을 업로드할 파일의 내용으로 사용합니다. 예를 들어 변환의 출력을 사용할 수 있습니다. 이 시나리오에서 식은 다음과 같습니다.

	@actions('transformservice').outputs.body.OutputXML

## 커넥터의 추가 기능
이제 커넥터를 만들었으므로 논리 앱을 사용하여 비즈니스 워크플로에 추가할 수 있습니다. [논리 앱 정의](app-service-logic-what-are-logic-apps.md)를 참조하세요.

[커넥터 및 API 앱 참조](http://go.microsoft.com/fwlink/p/?LinkId=529766)의 Swagger REST API 참조를 봅니다.

커넥터의 성능 통계를 검토하고 보안을 제어할 수 있습니다. [기본 제공 API 앱 및 커넥터 관리 및 모니터링](app-service-logic-monitor-your-connectors.md)을 참조하세요.

<!-- Image reference -->
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
 

<!---HONumber=August15_HO8-->