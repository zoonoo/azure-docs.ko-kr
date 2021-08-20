---
title: 모델 변환 오류 코드
description: Azure Object Anchors 서비스에 대한 모델 변환 오류 코드입니다.
author: jastenze
manager: vriveras
ms.author: jastenze
ms.date: 04/20/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: e47e4e9f784e51c646da22063ee954d9b60a4e11
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202674"
---
# <a name="model-conversion-error-codes"></a>모델 변환 오류 코드

모델 변환 실패의 일반적인 모드의 경우 `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionOperation`의 `Value` 필드에서 가져온 `Azure.MixedReality.ObjectAnchors.Conversion.AssetConversionProperties` 개체에는 `ConversionErrorCode` 유형의 ErrorCode 필드가 포함됩니다. 이 유형은 이러한 일반적인 실패 모드를 열거합니다. 여기에는 오류 메시지 지역화, 오류 복구 및 오류를 수정할 수 있는 방법에 대한 팁이 있습니다.

| 오류 코드                    | Description                       |  완화 방법                       |
| ---                      | ---                               | ---                               |
| INVALID_ASSET_URI | 변환 작업을 시작할 때 제공된 URI의 자산을 찾을 수 없습니다. | 자산 변환 작업을 트리거할 때 변환할 자산이 업로드된 서비스에서 가져온 업로드 URI를 제공합니다. |
| INVALID_JOB_ID | 생성할 자산 변환 작업에 대해 제공된 ID가 기본적으로 모두 0인 GUID로 설정되었습니다. | 자산 변환 작업을 만들 때 GUID를 지정하는 경우 GUID가 기본적으로 모두 0인 GUID가 아닌지 확인합니다. |
| INVALID_GRAVITY | 자산 변환 작업을 만들 때 제공된 중력 벡터는 완전히 0이 되는 벡터였습니다. | 자산 변환을 시작할 때 업로드된 자산에 해당하는 중력 벡터를 제공합니다. |
| INVALID_SCALE | 제공된 배율이 0이 아닌 양의 값이 아닙니다. | 자산 변환을 시작할 때 업로드된 자산의 측정 단위 배율(미터)에 해당하는 스칼라 값을 제공합니다. |
| ASSET_SIZE_TOO_LARGE | 자산 또는 해당 직렬화된 자산에서 생성된 중간 .PLY 파일이 너무 큽니다. | 변환을 위해 자산을 제출하기 전에 자산 크기 지침을 참조하여 적합성을 확인합니다. aka.ms/aoa/faq |
| ASSET_DIMENSIONS_OUT_OF_BOUNDS | 자산의 크기가 실제 차원 제한을 초과했습니다. 이는 작업을 만들 때 자산에 대해 부적절하게 설정된 규모의 표시일 수 있습니다. | 변환을 위해 자산을 제출하기 전에 자산 크기 지침을 참조하여 적합성을 확인하고 제공된 규모가 업로드된 자산과 일치하는지 확인합니다. aka.ms/aoa/faq |
| ZERO_FACES | 자산에서 생성된 중간 .PLY 파일은 얼굴이 없는 것으로 확인되어 변환에 유효하지 않습니다. | 자산이 유효한 메시지인지 확인합니다. |
| INVALID_FACE_VERTICES | 자산에서 생성된 중간 .PLY 파일에 존재하지 않는 꼭짓점을 참조하는 얼굴이 포함되어 있습니다. | 자산 파일이 올바르게 생성되었는지 확인합니다. |
| ZERO_TRAJECTORIES_GENERATED | 업로드된 자산에서 생성된 카메라 궤도가 비어 있습니다. | 변환을 위해 자산을 제출하기 전에 자산 크기 지침을 참조하여 적합성을 확인합니다. aka.ms/aoa/faq |
| TOO_MANY_RIG_POSES | 중간 .PLY 파일의 Rig 포즈 수가 서비스 제한을 초과했습니다. | 변환을 위해 자산을 제출하기 전에 자산 크기 지침을 참조하여 적합성을 확인합니다. aka.ms/aoa/faq |
| SERVICE_ERROR | 알 수 없는 서비스 오류가 발생했습니다. | 문제가 지속되면 Object Anchors 서비스 팀 구성원에게 문의합니다. https://github.com/Azure/azure-object-anchors/issues |
| ASSET_CANNOT_BE_CONVERTED | 제공된 자산이 손상되었거나, 형식이 잘못되었거나, 제공된 형식으로 변환할 수 없습니다. | 자산이 지정된 유형의 유효하게 생성된 파일인지 확인하고 변환을 위해 자산을 제출하기 전에 자산 크기 지침을 참조하여 적합성을 확인합니다. aka.ms/aoa/faq |

실제 자산 변환 작업 외부에서 발생하는 모든 오류는 예외로 throw됩니다. 특히, `Azure.RequestFailedException`은 실패한(4xx 또는 5xx) 또는 예기치 않은 HTTP 응답 코드를 수신하는 서비스 호출에 대해 throw될 수 있습니다. 이러한 예외에 대한 자세한 내용은 예외의 `Status`, `ErrorCode` 또는 `Message` 필드를 검사합니다.

| 예외                  | 원인                       |
| ---                      | ---                               |
| ArgumentException |  <ul><li>잘못 생성되었거나 모두 0개의 계정 ID를 사용하여 ObjectAnchorsConversionClient로 요청을 생성할 때 발생합니다.</li><li>잘못된 공백 계정 도메인을 사용하여 ObjectAnchorsConversionClient를 초기화하려고 할 때 발생합니다.</li><li>지원되지 않는 서비스 버전이 ObjectAnchorsConversionClientOptions를 통해 ObjectAnchorsConversionClient에 제공될 때 발생합니다.</li></ul> |
| ArgumentNullException | <ul><li>잘못된 null 계정 도메인을 사용하여 ObjectAnchorsConversionClient를 초기화하려고 할 때 발생합니다.</li><li>잘못된 null 자격 증명을 사용하여 ObjectAnchorsConversionClient를 초기화하려고 할 때 발생합니다.</li></ul> |
| RequestFailedException | <ul><li>계정을 찾을 수 없는 경우, 프런트 엔드 서비스 오류로 인해 잘못된 업로드 URI가 검색되는 등 (실행됨/실행 중인/실행할 작업의 상태와 관련이 없는) 잘못된 HTTP 상태 코드로 인해 나타나는 그 밖의 모든 문제에 대해 발생합니다.</li></ul> |
| UnsupportedAssetFileTypeException | <ul><li>Azure Object Anchors Conversion 서비스에서 지원되지 않는 확장명 또는 지정된 파일 형식의 자산이 있는 작업을 제출하려고 할 때 발생합니다.</li></ul> |