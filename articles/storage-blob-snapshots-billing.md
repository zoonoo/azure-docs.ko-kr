<properties 
	pageTitle="스냅숏 요금 청구 방법 이해" 
	description="Azure 저장소 Blob 스냅숏 요금 청구 방법을 소개하는 가이드" 
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

#스냅숏 요금 청구 방법 이해

Blob의 읽기 전용 복사본인 스냅숏을 만들면 계정에 데이터 저장소 요금이 추가로 부과될 수 있습니다. 응용 프로그램을 디자인할 때는 불필요한 비용을 최소화할 수 있도록 이러한 요금 발생 방식을 파악하는 것이 중요합니다.

##청구 관련 중요 고려 사항

아래 목록에는 스냅숏을 만들 때 고려할 주요 사항이 나와 있습니다.

- Blob, 스냅숏 등 위치에 관계없이 고유 블록이나 페이지에는 요금이 청구됩니다. 스냅숏의 기준이 되는 Blob을 업데이트할 때까지는 해당 Blob와 연결된 스냅숏에 대해 계정에 추가 요금이 부과되지 않습니다. 기본 Blob은 업데이트하면 해당 스냅숏과 달라지므로 각 Blob 또는 스냅숏의 고유 블록이나 페이지에 대해 요금이 부과됩니다.

- 블록 Blob 내의 블록을 바꾸고 나면 해당 블록은 이후부터 고유 블록으로 요금이 청구됩니다. 블록의 ID와 데이터가 스냅숏에서와 같더라도 마찬가지입니다. 블록을 다시 커밋하면 스냅숏의 해당 항목과 달라지므로 해당 데이터에 대해 요금이 부과됩니다. 같은 데이터로 업데이트하는 페이지 Blob 내 페이지의 경우에도 마찬가지입니다.

- UploadFile, UploadText, UploadStream 또는 UploadByteArray 메서드를 호출하여 블록 Blob을 바꾸면 해당 Blob의 모든 블록이 바뀝니다. 해당 Blob에 스냅숏이 연결되어 있으면 스냅숏과 기본 Blob의 모든 블록이 달라지므로 두 Blob의 모든 블록에 대해 요금이 부과됩니다. 이는 기본 Blob와 스냅숏의 데이터가 동일하게 유지되더라도 마찬가지입니다.

- Azure Blob 서비스에서는 두 블록이 같은 데이터를 포함하는지를 확인할 수 없습니다. 업로드/커밋되는 각 블록은 데이터와 블록 ID가 같더라도 고유한 블록으로 처리됩니다. 고유한 블록에 대해서는 비용이 부과되므로 스냅숏이 포함된 Blob을 업데이트하면 고유한 블록이 추가로 생성되어 추가 비용이 발생함을 고려해야 합니다.

> [AZURE.NOTE] 모범 사례에 따르면 추가 비용을 방지하기 위해 스냅숏을 철저하게 관리해야 합니다. 즉, 다음과 같은 방식으로 스냅숏을 관리하는 것이 좋습니다.

> - 응용 프로그램 디자인상 스냅숏을 유지해야 하는 경우가 아니면, Blob을 업데이트할 때마다 같은 데이터로 업데이트하더라도 해당 Blob에 연결된 스냅숏을 삭제한 후에 다시 만듭니다. Blob의 스냅숏을 삭제한 후에 다시 만들면 Blob와 스냅숏이 달라지지 않습니다.

> - Blob의 스냅숏을 유지하는 경우에는 UploadFile, UploadText, UploadStream 또는 UploadByteArray를 호출하여 Blob을 업데이트하지 않습니다. 이러한 메서드는 Blob의 모든 블록을 바꾸기 때문입니다. 대신 PutBlock 및 PutBlockList 메서드를 사용하여 가능한 최소 블록 수만 업데이트합니다.


##스냅숏 청구 시나리오


다음 시나리오에서는 블록 Blob와 해당 스냅숏에 대해 비용이 청구되는 방법을 보여 줍니다. 

시나리오 1에서는 스냅숏을 생성한 이후 기본 Blob을 업데이트하지 않았으므로 고유 블록 1, 2, 3에 대해서만 비용이 청구됩니다.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-1.png)

시나리오 2에서는 기본 Blob은 업데이트되었지만 스냅숏은 업데이트되지 않았습니다. 블록 3이 업데이트되고 동일한 데이터와 동일한 ID를 가지고 있지만 스냅숏의 블록 3과 같지는 않습니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-2.png)

시나리오 3에서는 기본 Blob은 업데이트되었지만 스냅숏은 업데이트되지 않았습니다. 기본 Blob에서 블록 3이 블록 4로 바뀌었지만 스냅숏은 계속 블록 3을 반영합니다. 따라서 4개 블록에 대한 요금이 계정에 청구됩니다.
 
![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-3.png)

시나리오 4에서는 기본 Blob이 완전히 업데이트되었으며 원래 블록은 하나도 포함되어 있지 않습니다. 따라서 8개 고유 블록 모두에 대한 요금이 계정에 청구됩니다. UploadFile, UploadText, UploadFromStream, UploadByteArray 등의 업데이트 메서드를 사용하는 경우가 이러한 시나리오에 해당합니다. 이러한 메서드는 Blob의 모든 콘텐츠를 바꾸기 때문입니다.

![Azure Storage Resources](./media/storage-blob-snapshots-billing/storage-blob-snapshots-billing-scenario-4.png)
<!--HONumber=47-->
