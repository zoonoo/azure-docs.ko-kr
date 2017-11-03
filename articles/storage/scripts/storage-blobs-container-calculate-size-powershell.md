---
title: "Azure PowerShell 스크립트 샘플 - Blob 컨테이너 크기 계산 | Microsoft Docs"
description: "컨테이너에 있는 각 Blob의 크기를 합계해서 Azure Blob Storage의 컨테이너 크기를 계산합니다."
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 10/23/2017
ms.author: fryu
ms.openlocfilehash: 46f3eac0129da41062caba2da090f9e532505d67
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Blob Storage 컨테이너 크기 계산

이 스크립트는 컨테이너에 있는 Blob의 크기를 합계해서 Azure Blob Storage의 컨테이너 크기를 계산합니다.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="understand-the-size-of-blob-storage-container"></a>Blob Storage 컨테이너 크기 이해

Blob Storage 컨테이너의 전체 크기에는 컨테이너 자체의 크기와 컨테이너 아래 모든 Blob의 크기가 포함됩니다.

다음은 Blob 컨테이너 및 Blob에 대한 저장소 용량을 계산하는 방법을 설명합니다. 아래에서 Len(X)는 문자열의 문자 수를 의미합니다.

### <a name="blob-containers"></a>Blob 컨테이너

다음은 Blob 컨테이너당 소비되는 저장소 양을 계산하는 방법입니다.

`
48 bytes + Len(ContainerName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
For-Each Signed Identifier[512 bytes]
`

다음은 분석 정보입니다.
* 각 컨테이너의 48바이트 오버헤드에는 마지막으로 수정한 시간, 권한, 공용 설정 및 일부 시스템 메타데이터가 포함됩니다.
* 컨테이너 이름은 유니코드로 저장되므로 문자 수에 2를 곱합니다.
* 저장된 각 Blob 컨테이너 메타데이터에 대해 이름의 길이(ASCII로 저장됨)와 문자열 값의 길이를 저장합니다.
* 서명된 식별자당 512바이트에는 서명된 식별자 이름, 시작 시간, 만료 시간 및 권한아 포함됩니다.

### <a name="blobs"></a>Blob

다음은 Blob당 소비되는 저장소 양을 계산하는 방법입니다.

* 블록 Blob(기본 Blob 또는 스냅숏)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
8 bytes + number of committed and uncommitted blocks * Block ID Size in bytes +
SizeInBytes(data in unique committed data blocks stored) +
SizeInBytes(data in uncommitted data blocks)
`

* 페이지 Blob(기본 Blob 또는 스냅숏)

`
124 bytes + Len(BlobName) * 2 bytes +
For-Each Metadata[3 bytes + Len(MetadataName) + Len(Value)] +
number of nonconsecutive page ranges with data * 12 bytes +
SizeInBytes(data in unique pages stored)
`

다음은 분석 정보입니다.

* 마지막으로 수정한 시간, 크기, 캐시 제어, 콘텐츠 형식, 콘텐츠 언어, 콘텐츠 인코딩, 콘텐츠 MD5, 권한, 스냅숏 정보, 임대 및 일부 시스템 메타데이터가 포함된 Blob에 대한 124바이트 오버헤드.
* Blob 이름은 유니코드로 저장되므로 문자 수에 2를 곱합니다.
* 저장된 각 메타데이터에 대해 이름의 길이(ASCII로 저장됨)와 문자열 값의 길이를 저장합니다.
* 블록 Blob의 경우
    * 블록 목록에 대한 8바이트
    * 블록 수 곱하기 블록 ID 크기(바이트)
    * 더하기 커밋된 블록과 커밋되지 않은 블록에 있는 데이터의 크기. 스냅숏이 사용되면 이 크기에 기본 또는 스냅숏 Blob에 대한 고유 데이터만 포함됩니다. 커밋되지 않은 블록이 일주일 후에 사용되지 않으면 해당 블록은 가비지 수집되고 이후로 더 이상 청구에 포함되지 않습니다.
* 페이지 Blob의 경
    * 데이터 시간이 12바이트인 비연속적인 페이지 범위의 수. GetPageRanges API를 호출할 때 표시되는 고유한 페이지 범위의 수입니다.
    * 더하기 저장된 모든 페이지의 데이터 크기(바이트). 스냅숏이 사용되면 이 크기에 기본 Blob 또는 스냅숏 Blob이 카운트되는 고유 페이지만 포함됩니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size-ex.ps1 "Calculate container size")]

## <a name="next-steps"></a>다음 단계

Azure Storage 청구에 대한 자세한 내용은 [Windows Azure Storage 청구 이해](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/)를 참조하세요.

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/overview)를 참조하세요.

추가 저장소 PowerShell 스크립트 샘플은 [Azure Storage에 대한 PowerShell 샘플](../blobs/storage-samples-blobs-powershell.md)에서 찾을 수 있습니다.
