<properties 
	pageTitle="Blob의 스냅숏 만들기" 
	description="Azure 저장소 Blob 스냅숏 만들기 방법 가이드" 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

#Blob의 스냅숏 만들기

Blob의 스냅숏을 만들 수 있습니다. 스냅숏은 특정 시점에 생성된 Blob의 읽기 전용 버전입니다. 스냅숏이 생성된 후에는 읽거나 복사하거나 삭제할 수 있지만 수정할 수는 없습니다. 스냅숏을 사용하면 특정 시점에서 표시된 대로 Blob을 백업할 수 있습니다.

Blob 스냅숏의 이름은 스냅숏을 생성한 기본 Blob의 이름과 같으며 스냅숏을 생성한 시간을 나타내기 위해 DateTime 값이 추가됩니다. 예를 들어 페이지 Blob URI가 http://storagesample.core.blob.windows.net/mydrives/myvhd이면 스냅숏 URI는 http://storagesample.core.blob.windows.net/mydrives/myvhd?snapshot=2011-03-09T01:42:34.9360000Z와 같습니다. 추후 작업 시 이 값을 사용하여 스냅숏을 참조할 수 있습니다. Blob의 스냅숏은 URI를 공유하며 이 DateTime 값을 통해서만 구분 가능합니다. 클라이언트 라이브러리 코드에서 Blob 스냅숏 속성은 기본 Blob을 기준으로 스냅숏을 고유하게 식별하는 DateTime 값을 반환합니다. 이 값을 사용하여 스냅숏에 대해 추가 작업을 수행할 수 있습니다.

Blob 하나에 여러 스냅숏이 있을 수 있습니다. 스냅숏은 명시적으로 삭제될 때까지 보존되지만 원본 Blob보다 오랫동안 보존할 수는 없습니다. Blob와 연결된 스냅숏을 열거하여 현재 스냅숏을 추적할 수 있습니다.

##속성 상속

Blob의 스냅숏을 만들면 시스템 속성이 같은 값으로 스냅숏에 복사됩니다. 아래 목록에는 .NET 저장소 클라이언트 라이브러리에 대해 복사되는 시스템 속성이 나와 있습니다.

- ContentType 

- ContentEncoding 

- ContentLanguage

- Length

- CacheControl

- ContentMd5 


기본 Blob와 연결된 임대는 스냅숏에 복사되지 않습니다. 스냅숏은 임대할 수 없습니다.

##스냅숏 복사 

Blob 및 스냅숏 관련 복사 작업에는 다음 규칙이 적용됩니다.

- 기본 Blob에 대해 스냅숏을 복사할 수 있습니다. 스냅숏의 수준을 기본 Blob 위치로 올리면 이전 Blob 버전을 복원할 수 있습니다. 이 경우 스냅숏은 유지되지만 읽고 쓰기가 가능한 복사본으로 해당 원본을 덮어쓰게 됩니다.

- 스냅숏을 다른 이름으로 대상 Blob에 복사할 수 있습니다. 그러면 생성되는 대상 Blob은 스냅숏이 아닌 쓰기 가능한 Blob입니다.

- 원본 Blob을 복사해도 해당 원본 Blob의 스냅숏은 대상에 복사되지 않습니다. 대상 Blob을 복사본으로 덮어써도 대상 Blob와 연결된 스냅숏은 해당 이름으로 유지됩니다. 

- 블록 Blob의 스냅숏을 만들면 블록의 커밋된 블록 목록도 스냅숏에 복사됩니다. 커밋되지 않은 블록은 복사되지 않습니다.

##액세스 조건 지정 

조건이 충족되어야 스냅숏이 작성되도록 액세스 조건을 지정할 수 있습니다. 액세스 조건을 지정하려면 AccessCondition 속성을 사용합니다. 지정한 조건이 충족되지 않으면 스냅숏은 작성되지 않으며 Blob 서비스는 상태 코드 HTTPStatusCode.PreconditionFailed를 반환합니다.

##스냅숏 삭제 

스냅숏도 삭제하지 않으면 스냅숏이 포함된 Blob을 삭제할 수 없습니다. 스냅숏은 개별적으로 삭제할 수도 있고 원본 Blob을 삭제할 때 모든 스냅숏을 삭제하도록 저장소 서비스에 명령할 수도 있습니다. 아직 스냅숏이 있는 Blob을 삭제하려고 하면 호출에서 오류가 반환됩니다.

##프리미엄 저장소를 사용하는 스냅숏
프리미엄 저장소에서 스냅숏을 사용할 때는 다음 규칙이 적용됩니다.

- 프리미엄 저장소 계정의 페이지 Blob당 스냅숏 수는 100개로 제한됩니다. 해당 제한을 초과하면 스냅숏 Blob 작업에서 오류 코드 409(SnapshotCountExceeded)가 반환됩니다.

- 프리미엄 저장소 계정의 페이지 Blob 스냅숏은 10분마다 생성할 수 있습니다. 해당 속도를 초과하면 스냅숏 Blob 작업에서 오류 코드 409(SnaphotOperationRateExceeded)가 반환됩니다.

- Blob 가져오기를 통해 프리미엄 저장소 계정의 페이지 Blob 스냅숏을 읽을 수는 없습니다. 프리미엄 저장소 계정에서 스냅숏에 대해 Blob 가져오기를 호출하면 오류 코드 400(잘못된 작업)이 반환됩니다. 그러나 스냅숏에 대해 Blob 속성 가져오기 및 Blob 메타데이터 가져오기를 호출할 수는 있습니다.

- 스냅숏을 읽으려는 경우 Blob 복사 작업을 사용하여 계정의 다른 페이지 Blob에 스냅숏을 복사할 수 있습니다. 이때 복사 작업의 대상 Blob에는 기존 스냅숏이 없어야 합니다. 대상 Blob에 스냅숏이 있으면 Blob 복사 작업에서 오류 코드 409(SnapshotsPresent)가 반환됩니다.

##스냅숏에 대한 절대 URI 생성 

다음 코드 예제는 기본 Blob 개체에서부터 스냅숏의 절대 URI를 구성합니다.

C#

	var snapshot = blob.CreateSnapshot();
	var uri = Microsoft.WindowsAzure.StorageClient.Protocol.BlobRequest.Get
    (snapshot.Uri, 
    0, 
    snapshot.SnapshotTime.Value, 
    null).Address.AbsoluteUri;

<!--HONumber=47-->
