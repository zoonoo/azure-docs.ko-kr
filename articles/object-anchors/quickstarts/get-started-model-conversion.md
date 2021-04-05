---
title: '빠른 시작: 앱에서 사용할 Object Anchors 모델 만들기'
description: 이 빠른 시작에서는 3D 모델에서 Object Anchors 모델을 만드는 방법을 알아봅니다.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607903"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>빠른 시작: 3D 모델에서 Object Anchors 모델 만들기

Azure Object Anchors는 3D 모델을 HoloLens에 대한 개체 인식 혼합 현실 환경을 지원하는 AI 모델로 변환하는 관리형 클라우드 서비스입니다. 이 빠른 시작에서는 C#/.NET Core SDK를 사용하여 3D 모델에서 Object Anchors 모델을 만드는 방법에 대해 설명합니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Object Anchors 계정 만들기
> * 3D 모델을 변환하여 Object Anchors 모델 만들기

## <a name="prerequisites"></a>사전 요구 사항

이 빠른 시작을 완료하려면 다음 항목이 있어야 합니다.

* <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>가 설치된 Windows 머신.
* <a href="https://git-scm.com" target="_blank">Windows용 Git</a>.
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Object Anchors 계정 만들기

먼저 Object Anchors 서비스를 사용하여 계정을 만들어야 합니다.

1. [Azure Portal](https://portal.azure.com/)로 이동하여 **리소스 만들기** 를 선택합니다.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="새 리소스 만들기":::

2. **Object Anchors** 리소스를 검색합니다.

   "Object Anchors"를 검색합니다.

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Object Anchors 리소스 선택":::

   검색 결과의 **Object Anchors** 리소스에서 **만들기-> Object Anchors** 를 선택합니다.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Object Anchors 리소스 만들기":::

3. **Object Anchors 계정** 대화 상자에서:
    * 고유한 리소스 이름을 입력합니다.
    * 리소스를 연결할 구독을 선택합니다.
    * 기존 리소스 그룹을 만들거나 사용합니다.
    * 리소스를 배치할 지역을 선택합니다.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Object Anchors 리소스 계정 세부 정보 입력":::

    리소스 만들기를 시작하려면 **만들기** 를 선택합니다.

4. 리소스를 만들었으면 **리소스로 이동** 을 선택합니다.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="리소스로 이동":::

5. 개요 페이지에서:

   **계정 도메인** 을 기록해 둡니다. 나중에 필요합니다.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Object Anchors 리소스에 대한 계정 도메인 복사":::

   **계정 ID** 를 기록해 둡니다. 나중에 필요합니다.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Object Anchors 리소스에 대한 계정 ID 복사":::

   **액세스 키** 페이지로 이동하여 **기본 키** 를 기록해 둡니다. 나중에 필요합니다.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Object Anchors 리소스에 대한 계정 키 복사":::

## <a name="get-the-sample-project"></a>샘플 프로젝트 가져오기

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>3D 모델 변환

이제 3D 모델을 변환할 수 있습니다.

1. Visual Studio에서 `quickstarts/conversion/Conversion.sln`을 엽니다. 이 솔루션에는 C# 콘솔 프로젝트가 포함되어 있습니다.

2. 프로젝트의 루트에 있는 `Configuration.cs` 파일을 열고 다음 필드에서`set-me` 값을 바꿉니다.

   | 필드         | Description                                                         |
   |---------------|---------------------------------------------------------------------|
   | AccountDomain | 위에서 만든 Object Anchors 계정의 **계정 도메인** 입니다. |
   | AccountId     | 위에서 만든 Object Anchors 계정의 **계정 ID** 입니다.     |
   | AccountKey    | 위에서 만든 Object Anchors 계정의 **기본 키** 입니다.     |

   확인해야 하는 4개의 추가 필드가 있습니다.

    | 필드                    | Description                       |
    | ---                      | ---                               |
    | InputAssetPath           | 로컬 머신의 3D 모델에 대한 절대 경로입니다. 지원되는 파일 형식은 `fbx`, `ply`, `obj`, `glb`, `gltf`입니다. |
    | AssetDimensionUnit       | 3D 모델의 측정 단위입니다. 지원되는 모든 측정 단위는 `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` 열거를 사용하여 액세스할 수 있습니다. |
    | Gravity                  | 3D 모델의 중력 벡터 방향입니다. 이 3D 벡터는 모델의 좌표계에서 아래쪽 방향을 제공합니다. 예를 들어 음수 `y`가 모델의 3D 공간에서 아래쪽 방향을 나타내는 경우 이 값은 `Vector3(0.0f, -1.0f, 0.0f)`입니다. |

3. 프로젝트를 빌드하고 실행하여 3D 모델을 업로드하고 서비스에 새 변환 작업을 등록하고 완료될 때까지 기다립니다. 작업이 완료되면 `InputAssetPath`에 지정된 파일 옆에 Object Anchors 모델이 다운로드됩니다. 다음 콘솔 출력과 비슷하게 표시됩니다.

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   나중에 참조할 수 있도록 **작업 ID** 를 기록해 둡니다. 디버깅 또는 문제 해결에 유용할 수 있습니다.

4. 작업이 성공적으로 완료되면 지정된 출력 위치에 `<Model-Filename-Without-Extension>_<JobID>.ou` 형식의 파일이 표시되어야 합니다. 예를 들어 3D 모델 파일 이름이 `chair.ply`이고 작업 ID가 `00000000-0000-0000-0000-000000000000`인 경우 서비스 출력 파일 이름은 `chair_00000000-0000-0000-0000-000000000000.ou`입니다.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Object Anchors 계정을 만들고 3D 모델을 변환하여 Object Anchors 모델을 만들었습니다. 혼합 현실 앱에서 Object Anchors SDK와 모델을 통합하는 방법에 대해 알아보려면 다음 문서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [MRTK를 사용한 Unity HoloLens](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
