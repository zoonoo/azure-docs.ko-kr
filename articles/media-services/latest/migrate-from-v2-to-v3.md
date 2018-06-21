---
title: Azure Media Services v2에서 v3으로 마이그레이션 | Microsoft Docs
description: 이 문서에서는 Azure Media Services v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다.
services: media-services
documentationcenter: na
author: Juliako
manager: cfowler
editor: ''
tags: ''
keywords: Azure Media Services, 스트림, 브로드캐스트, 라이브, 오프라인
ms.service: media-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 05/22/2018
ms.author: juliako
ms.openlocfilehash: 4e644db12a74d6ef132a0c8d64ef517a0c2253cc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660096"
---
# <a name="migrate-from-media-services-v2-to-v3"></a>Media Services v2에서 v3으로 마이그레이션

> [!NOTE]
> Azure Media Services의 최신 버전은 미리보기에 있으며 v3라고도 합니다.

이 문서에서는 AMS(Azure Media Services) v3에서 도입된 변경 내용을 설명하고 두 버전 간의 차이점을 보여 줍니다.

## <a name="why-should-you-move-to-v3"></a>v3으로 이동해야 하는 이유는 무엇인가요?

### <a name="api-is-more-approachable"></a>더 접근하기 쉬운 API

*  v3은 Azure Resource Manager에서 빌드된 관리 및 운영 기능을 모두 제공하는 통합된 API 화면을 기반으로 합니다. Azure Resource Manager 템플릿을 사용하여 변환, 스트리밍 엔드포인트, LiveEvent(라이브 이벤트) 등을 만들고 배포할 수 있습니다.
* API(Swagger라고도 함) 사양 문서를 엽니다.
* .NET, .Net Core, Node.js, Python, Java, Ruby용 SDK를 사용할 수 있습니다.
* Azure CLI를 통합합니다.

### <a name="new-features"></a>새로운 기능

* 인코딩은 이제 HTTPS 수집(URL 기반 입력)을 지원합니다.
* Transform이 v3에서 새로 추가되었습니다. Transform은 구성을 공유하고, Azure Resource Manager 템플릿을 만들고, 특정 고객 또는 테넌트에 대한 인코딩 설정을 격리하는 데 사용됩니다. 
* 동적 패키징 및 동적 암호화 설정이 서로 다른 여러 개의 StreamingLocator가 자산에 있을 수 있습니다.
* 콘텐츠 보호에서 다중 키 기능을 지원합니다. 
* LiveEvent 미리 보기는 동적 패키징 및 동적 암호화를 지원합니다. 이렇게 하면 DASH 및 HLS 패키징뿐만 아니라 미리 보기에서도 콘텐츠 보호가 가능합니다.
* LiveOuput은 이전 Program 엔터티보다 더 간단하게 사용할 수 있습니다. 
* 엔터티에 대한 RBAC 지원이 추가되었습니다.

## <a name="changes-from-v2"></a>v2에서 변경된 내용

* Media Services SDK가 Storage SDK에서 분리되어 사용되는 Storage SDK를 더 효율적으로 제어하고 버전 문제를 방지합니다. 
* v3에서 모든 인코딩 비트 전송률은 비트/초입니다. 이는 REST v2 Media Encoder Standard 미리 설정과 다릅니다. 예를 들어 v2의 비트 전송률은 128로 지정되지만 v3에서는 128000이 됩니다. 
* AssetFiles, AccessPolicies, IngestManifests는 v3에 없습니다.
* ContentKeys는 더 이상 엔터티가 아니며 StreamingLocator의 속성입니다.
* Event Grid 지원은 NotificationEndpoints를 대체합니다.
* 일부 엔터티의 이름이 바뀌었습니다.

  * JobOutput은 Task를 대체하여, 이제는 Job의 일부입니다.
  * LiveEvent는 Channel을 대체합니다.
  * LiveOutput은 Program을 대체합니다.
  * StreamingLocator는 Locator를 대체합니다.

## <a name="code-changes"></a>코드 변경 내용

### <a name="create-an-asset-and-upload-a-file"></a>자산 만들기 및 파일 업로드 

#### <a name="v2"></a>v2

```csharp
IAsset asset = context.Assets.Create(assetName, storageAccountName, options);

IAssetFile assetFile = asset.AssetFiles.Create(assetFileName);

assetFile.Upload(filePath);
```

#### <a name="v3"></a>v3

```csharp
Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, permissions: AssetContainerPermission.ReadWrite, expiryTime: DateTime.Now.AddHours(1));

var sasUri = new Uri(response.AssetContainerSasUrls.First());
CloudBlobContainer container = new CloudBlobContainer(sasUri);

var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
blob.UploadFromFile(fileToUpload);
```

### <a name="submit-a-job"></a>작업 제출

#### <a name="v2"></a>v2

```csharp
IMediaProcessor processor = context.MediaProcessors.GetLatestMediaProcessorByName(mediaProcessorName);

IJob job = jobs.Create($"Job for {inputAsset.Name}");

ITask task = job.Tasks.AddNew($"Task for {inputAsset.Name}", processor, taskConfiguration);

task.InputAssets.Add(inputAsset);

task.OutputAssets.AddNew(outputAssetName, outputAssetStorageAccountName, outputAssetOptions);

job.Submit();
```

#### <a name="v3"></a>v3

```csharp
client.Assets.CreateOrUpdate(resourceGroupName, accountName, outputAssetName, new Asset());

JobOutput[] jobOutputs = { new JobOutputAsset(outputAssetName)};

JobInput jobInput = JobInputAsset(assetName: assetName);

Job job = client.Jobs.Create(resourceGroupName,
accountName, transformName, jobName,
new Job {Input = jobInput, Outputs = jobOutputs});
```

### <a name="publish-an-asset-with-aes-encryption"></a>AES 암호화를 사용하여 자산 게시 

#### <a name="v2"></a>v2

1. ContentKeyAuthorizationPolicyOption 만들기
2. ContentKeyAuthorizationPolicy 만들기
3. AssetDeliveryPolicy 만들기
4. Asset 만들기/콘텐츠 업로드 또는 작업 제출/출력 자산 사용
5. Asset에 AssetDeliveryPolicy 연결
6. ContentKey 만들기
7. Asset에 ContentKey 연결
8. AccessPolicy 만들기
9. Locator 만들기

#### <a name="v3"></a>v3

1. 콘텐츠 키 정책 만들기
2. Asset 만들기
3. 콘텐츠 업로드 또는 Asset을 JobOutput으로 사용
4. Locator 만들기

## <a name="next-steps"></a>다음 단계

비디오 파일 인코딩 및 스트리밍을 시작하는 것이 얼마나 쉬운 지 보려면 [파일 스트리밍](stream-files-dotnet-quickstart.md)을 확인합니다. 

