---
title: 모델 변환
description: 렌더링을 위해 모델을 변환하는 프로세스에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681494"
---
# <a name="convert-models"></a>모델 변환

Azure 원격 렌더링을 사용하면 매우 복잡한 모델을 렌더링할 수 있습니다. 최대 성능을 얻으려면 데이터를 최적의 형식으로 사전 처리해야 합니다. 데이터 양에 따라 이 단계는 시간이 걸릴 수 있습니다. 이 시간이 모델 로드 중에 소비되었다면 비실용적입니다. 또한 여러 세션에 대해 이 프로세스를 반복하는 것은 낭비입니다. 이러한 이유로 ARR 서비스는 미리 실행할 수 있는 전용 *변환 서비스를*제공합니다.
변환되면 Azure 저장소 계정에서 모델을 로드할 수 있습니다.

## <a name="supported-source-formats"></a>지원되는 소스 형식

변환 서비스는 다음 형식을 지원합니다.

- **FBX** (버전 2011 이상)
- **GLTF** (버전 2.x)
- **GLB** (버전 2.x)

모델 형식에 대한 장 [재질 매핑에](../../reference/material-mapping.md)나열된 대로 재료 속성 변환과 관련된 형식 간에는 약간의 차이가 있습니다.

## <a name="the-conversion-process"></a>변환 프로세스

1. [두 개의 Azure Blob 저장소 컨테이너 준비:](blob-storage.md)입력용 컨테이너, 출력용 컨테이너 하나
1. 모델을 입력 컨테이너에 업로드(선택적으로 하위 경로 아래).
1. 모델 변환 REST API를 통해 [변환 프로세스트리거](conversion-rest-api.md)
1. 변환 진행률에 대한 서비스 폴링
1. 완료되면 모델을 로드합니다.
    - 연결된 저장소 [계정에서(계정 만들기의](../create-an-account.md#link-storage-accounts) "저장소 계정 링크" 단계를 참조하여 저장소 계정을 연결합니다.)
    - 또는 공유 *액세스 서명(SAS)을*제공하여

모든 모델 데이터(입력 및 출력)는 Azure Blob 저장소에서 제공된 사용자에 저장됩니다. Azure 원격 렌더링을 사용하면 자산 관리를 완전히 제어할 수 있습니다.

## <a name="conversion-parameters"></a>변환 매개 변수

다양한 변환 옵션은 [이 장을](configure-model-conversion.md)참조하십시오.

## <a name="examples"></a>예

- [빠른 시작: 렌더링을 위한 모델 변환은](../../quickstarts/convert-model.md) 모델을 변환하는 방법을 단계별로 소개하는 것입니다.
- 변환 서비스의 사용을 보여 주는 [PowerShell 스크립트](../../samples/powershell-example-scripts.md)예제는 *스크립트* 폴더의 [ARR 샘플 리포지토리에서](https://github.com/Azure/azure-remote-rendering) 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [모델 변환에 Azure Blob 저장소 사용](blob-storage.md)
- [모델 변환 REST API](conversion-rest-api.md)
- [모델 변환 구성](configure-model-conversion.md)
- [모델 형식에 대한 재질 매핑](../../reference/material-mapping.md)
