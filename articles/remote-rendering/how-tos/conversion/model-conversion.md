---
title: 모델 변환
description: 렌더링을 위해 모델을 변환하는 프로세스를 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "91318061"
---
# <a name="convert-models"></a>모델 변환

Azure Remote Rendering을 사용하면 매우 복잡한 모델을 렌더링할 수 있습니다. 성능을 최대화하려면 데이터를 최적의 형식으로 전처리해야 합니다. 데이터의 양에 따라 이 단계는 시간이 다소 걸릴 수 있습니다. 모델을 로드하는 동안 이 시간이 소요되는 경우에는 실용적이지 않습니다. 또한 여러 세션에 대해 이 프로세스를 반복하는 것은 불필요합니다. 이러한 이유로 ARR 서비스는 미리 실행할 수 있는 전용 *변환 서비스* 를 제공합니다.
변환이 완료되면 Azure Storage 계정에서 모델을 로드할 수 있습니다.

## <a name="supported-source-formats"></a>지원되는 원본 형식

변환 서비스는 다음 형식을 지원합니다.

- **FBX**(버전 2011 ~ 2020)
- **GLTF**/**GLB**(버전 2.x)

[모델 형식에 대한 재질 매핑](../../reference/material-mapping.md) 챕터에 나열된 것처럼 재질 속성 변환과 관련된 형식 간에는 약간의 차이가 있습니다.

## <a name="the-conversion-process"></a>변환 프로세스

1. [두 개의 Azure Blob Storage 컨테이너를 준비](blob-storage.md)합니다. 하나는 입력용이고 하나는 출력용입니다.
1. 모델을 입력 컨테이너에 업로드합니다(하위 경로 아래에 있으며 선택 사항임).
1. [모델 변환 REST API](conversion-rest-api.md)를 통해 변환 프로세스를 트리거합니다.
1. 변환 진행률에 대한 서비스를 폴링합니다.
1. 완료되면 연결된 스토리지 계정에서
    - 모델을 로드합니다(스토리지 계정을 연결하려면 [계정 만들기](../create-an-account.md#link-storage-accounts)의 "스토리지 계정 연결" 단계 참조).
    - 또는 *SAS(공유 액세스 서명)* 를 제공하여 모델을 로드합니다.

모든 모델 데이터(입력 및 출력)는 사용자가 제공한 Azure Blob 스토리지에 저장됩니다. Azure Remote Rendering은 자산 관리를 완벽하게 제어할 수 있도록 합니다.

## <a name="pricing"></a>가격 책정

변환 가격 책정에 대한 자세한 내용은 [Remote Rendering 가격 책정](https://azure.microsoft.com/pricing/details/remote-rendering) 페이지를 참조하세요.


## <a name="conversion-parameters"></a>변환 매개 변수

다양한 변환 옵션은 [이 챕터](configure-model-conversion.md)를 참조하세요.

## <a name="examples"></a>예

- [빠른 시작: 렌더링을 위해 모델 변환](../../quickstarts/convert-model.md)에서는 모델을 변환하는 방법에 대해 단계별로 소개합니다.
- 변환 서비스 사용을 보여 주는 [PowerShell 스크립트 예](../../samples/powershell-example-scripts.md)는 *Scripts* 폴더의 [ARR 샘플 리포지토리](https://github.com/Azure/azure-remote-rendering)에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환에 Azure Blob Storage 사용](blob-storage.md)
- [모델 변환 REST API](conversion-rest-api.md)
- [모델 변환 구성](configure-model-conversion.md)
- [변환용 파일 레이아웃](layout-files-for-conversion.md)
- [모델 형식에 대한 재질 매핑](../../reference/material-mapping.md)
