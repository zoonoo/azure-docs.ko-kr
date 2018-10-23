---
title: Azure Digital Twins 배포 | Microsoft Docs
description: 이 자습서의 단계에 따라 Azure Digital Twins 인스턴스를 배포하고 공간 리소스를 구성하는 방법을 알아봅니다.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 9ca8a9de9a286d4b14dae4a822f3e9baf4747c60
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363336"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>자습서: Azure Digital Twins를 배포하고 공간 그래프 구성

Azure Digital Twins를 사용하여 사람, 장소 및 장치를 일관적인 공간 시스템에 결합할 수 있습니다. 이 자습서 시리즈는 Azure Digital Twins를 사용하여 온도 및 공기질이 최적 상태인 방 점유를 검색하는 방법을 보여줍니다. 이러한 자습서에서는 .NET 콘솔 응용 프로그램을 통해 여러 층이 있고 각 층에 여러 개의 방이 있는 사무실 건물 시나리오를 작성하는 방법을 안내합니다. 방에는 동작, 주변 온도 및 공기질을 감지하는 센서가 부착된 장비가 있습니다. Azure Digital Twins를 사용하여 건물의 물리적 영역 및 엔터티를 디지털 개체로 복제하는 방법을 배울 것입니다. 또 다른 콘솔 응용 프로그램을 사용하여 장치 이벤트를 시뮬레이션할 것입니다. 그런 다음, 이러한 물리적 영역과 엔터티에서 들어오는 이벤트를 거의 실시간으로 모니터링하는 방법을 알아볼 것입니다. 사무실 관리자는 이 정보를 사용하여 이 건물에서 근무하는 직원이 최적 상태의 회의실을 예약하도록 도와줄 수 있습니다. 사무실 시설 관리자는 설정을 사용하여 회의실 사용 추세를 살펴보고 유지 관리 목적으로 작업 조건을 모니터링할 수 있습니다.

이 시리즈의 첫 번째 자습서에서는 다음 작업을 수행하는 방법을 알아봅니다.

> [!div class="checklist"]
> * Deploy Digital Twins
> * 앱에 권한 부여
> * Digital Twins 샘플 앱 수정
> * 건물 프로비전


이러한 자습서에서는 개념을 보다 구체적으로 설명하기 위해 [빠른 시작: 사용 가능한 회의실 찾기](quickstart-view-occupancy-dotnet.md)에 사용되는 것과 동일한 샘플을 수정하여 사용합니다.


## <a name="prerequisites"></a>필수 조건

- Azure 구독. Azure 계정이 없으면 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 이러한 자습서에 사용되는 Digital Twins 샘플은 C#으로 작성되었습니다. 샘플을 빌드하고 실행할 수 있도록 개발 머신에 [.NET Core SDK 버전 2.1.403 이상](https://www.microsoft.com/net/download)을 설치합니다. 명령 창에서 `dotnet --version` 명령을 실행하여 머신에 올바른 버전이 설치되어 있는지 확인합니다.

- 샘플 코드를 탐색할 [Visual Studio Code](https://code.visualstudio.com/). 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Deploy Digital Twins

이 섹션의 단계에 따라 Digital Twins 서비스의 새 인스턴스를 만듭니다. 구독당 하나의 인스턴스만 만들 수 있습니다. 이미 인스턴스가 실행 중이면 다음 섹션으로 건너뜁니다. 

[!INCLUDE [create-digital-twins-portal](../../includes/create-digital-twins-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>앱에 권한 부여

Digital Twins는 [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)를 사용하여 서비스에 대한 [읽기/쓰기 액세스](../active-directory/develop/v1-permissions-and-consent.md)를 제어합니다. Digital Twins 인스턴스와 연결해야 하는 모든 응용 프로그램은 Azure Active Directory에 등록되어야 합니다. 이 섹션의 단계에서는 샘플 앱을 등록하는 방법을 보여줍니다.

이미 *앱 등록*이 있는 경우 해당 앱 등록을 샘플에 다시 사용할 수 있습니다. 하지만 이 섹션을 끝까지 읽고 앱 등록이 올바르게 구성되었는지 확인해야 합니다.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-digital-twins-sample"></a>Digital Twins 샘플 구성

이 섹션에서는 [Digital Twins REST API](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index)와 통신하는 Digital Twins 응용 프로그램을 살펴봅니다. 

### <a name="download-the-sample"></a>샘플 다운로드
[빠른 시작: 사용 가능한 회의실 찾기](quickstart-view-occupancy-dotnet.md)에 필요한 샘플을 이미 다운로드한 경우 이 단계를 건너뛸 수 있습니다.

1. [Digital Twins .Net 샘플](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)을 다운로드합니다. 
2. 머신에 ZIP 폴더의 콘텐츠를 추출합니다. 

### <a name="explore-the-sample"></a>샘플 탐색
추출된 샘플 폴더의 **_digital-twins-samples-csharp\digital-twins-samples.code-workspace_** 파일을 Visual Studio Code에서 엽니다. 이 파일에는 다음 두 프로젝트가 포함되어 있습니다. 

1. 프로비전 샘플 **_occupancy-quickstart_** 를 사용하면 물리적 공간과 그 안에 있는 리소스를 디지털화한 이미지인 [공간 인텔리전스 그래프](concepts-objectmodel-spatialgraph.md#graph)를 구성하고 프로비전할 수 있습니다. 이 샘플은 스마트 건물에 대한 개체를 정의하는 [개체 모델](concepts-objectmodel-spatialgraph.md#model)을 사용합니다. Digital Twins 개체 및 REST API 전체 목록을 보려면 [인스턴스](#deploy)에 대해 작성된 [이 REST API 설명서](https://docs.westcentralus.azuresmartspaces.net/management/swagger) 또는 **관리 API** URL를 방문하세요.

   샘플이 Digital Twins 인스턴스와 통신하는 방법을 알아보려면 **_src\actions_** 폴더를 시작하세요. 이 폴더의 파일은 이 자습서에서 사용할 명령을 구현합니다.
    - *provisionSample.cs* 파일은 공간 그래프를 프로비전하는 방법을 보여줍니다.
    - *getSpaces.cs* 파일은 프로비전된 공간에 대한 정보를 가져옵니다.
    - *getAvailableAndFreshSpaces.cs* 파일은 사용자 정의 함수라고 하는 사용자 지정 함수의 결과를 가져옵니다.
    - *createEndpoints.cs* 파일은 다른 서비스와 상호 작용하는 엔드포인트를 만듭니다.

1. 시뮬레이션 샘플 **_device-connectivity_** 는 센서 데이터를 시뮬레이션하여 Digital Twin 인스턴스에 대해 프로비전된 IoT 허브로 보냅니다. 이 샘플은 [공간 그래프를 프로비전한 후 그 다음 자습서](tutorial-facilities-udf.md#simulate)에서 사용합니다. 이 샘플을 구성하는 데 사용된 센서 및 장치 식별자는 그래프를 프로비전하는 데 사용할 식별자와 동일해야 합니다.

### <a name="configure-the-provisioning-sample"></a>프로비전 샘플 구성
1. 명령 창을 열고, 다운로드한 샘플로 이동합니다. 다음 명령 실행:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. 이 명령을 실행하여 샘플 프로젝트에 대한 종속성을 복원합니다.

    ```cmd/sh
    dotnet restore
    ```

1. Visual Studio Code에서, **occupancy-quickstart** 프로젝트에 속하는 **_appSettings.json_** 파일을 열고, 다음 값을 업데이트합니다.
    1. *ClientId*: [앱 권한 설정](#permissions) 섹션에서 본 AAD 앱 등록의 **응용 프로그램 ID**를 입력합니다.
    1. *테넌트*: 마찬가지로 [앱 권한 설정](#permissions) 섹션에서 본 [AAD 테넌트](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant)의 **디렉터리 ID**를 입력합니다.
    1. *BaseUrl*: Digital Twins 인스턴스의 URL을 입력합니다. 이 URL을 가져오려면 이 URL의 자리 표시자를 인스턴스에 대한 값 **https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/** 로 바꿉니다. [배포 섹션](#deploy)에서 **관리 API** URL을 수정하고 **swagger/** 를 **api/v1.0/** 로 바꿔서 이 URL을 가져올 수도 있습니다.

1. 샘플을 사용하여 탐색할 수 있는 Digital Twins 기능 목록을 보려면 다음 명령을 실행합니다.

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-provisioning-process"></a>프로비전 프로세스의 이해
이 섹션에서는 샘플이 건물의 공간 그래프를 프로비전하는 방법을 보여줍니다. 

Visual Studio Code에서 **_occupancy-quickstart\src\actions_** 폴더로 이동하여 *provisionSample.cs* 파일을 엽니다. 다음 함수를 봅니다.

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

이 함수는 같은 폴더에 있는 *provisionSample.yaml* 파일을 사용합니다. 이 파일을 열고, 사무실 건물의 계층 구조인 *Venue*, *Floor*, *Area* 및 *Rooms*를 봅니다. 이러한 물리적 공간은 *장치* 및 *센서*를 포함할 수 있습니다. 각 항목에는 미리 정의된 `type`이 있으며 *Floor*, *Room*을 예로 들 수 있습니다. 

샘플 *yaml* 파일은 `Default` Digital Twins 개체 모델을 사용하여 공간 그래프를 보여줍니다. 이 모델은 대부분의 유형(예: SensorDataType에 대한 온도, SpaceBlobType에 대한 맵) 및 공간 유형(예: 하위 유형이 FocusRoom, ConferenceRoom 등인 방)에 대한 일반 이름을 제공합니다. 공장 같은 다른 유형의 장소에 대한 공간 그래프를 만들어야 하는 경우 다른 개체 모델이 필요합니다. 프로비전 샘플에 대한 명령줄에서 `dotnet run GetOntologies` 명령을 실행하여 사용 가능한 모델을 확인할 수 있습니다. 공간 그래프 및 개체 모델에 대한 자세한 내용은 [Digital Twins 개체 모델 및 공간 인텔리전스 그래프의 이해](concepts-objectmodel-spatialgraph.md)를 참조하세요. 

### <a name="modify-sample-spatial-graph"></a>샘플 공간 그래프 수정
*provisionSample.yaml*은 다음 노드를 포함합니다.

- **리소스**: `resources` 노드는 설치된 장치와 통신하는 IoT Hub 리소스를 만듭니다. 그래프의 루트 노드에 있는 IoT 허브는 그래프의 모든 장치 및 센서와 통신할 수 있습니다.  

- **공간**: Digital Twins 개체 모델에서 `spaces`는 물리적 위치를 나타냅니다. 각 공간에는 *Region*, *Venue*, *Customer*, `Name` 등의 `Type`이 있습니다. 공간은 계층적 구조를 만드는 다른 공간에 속할 수 있습니다. *provisionSample.yaml* 파일에는 가상 건물의 공간 그래프가 있습니다. `Venue`, 층의 `Area`, 영역의 `Room` 노드 내부에 있고 유형이 `Floor`인 공간의 논리 중첩 형식을 살펴보세요. 

- **장치**: 공간에는 `devices`가 포함될 수 있으며, 장치는 센서 수를 관리하는 물리적 장치 또는 가상 엔터티입니다. 예를 들어 사용자의 휴대폰, Raspberry Pi 센서 Pod, 게이트웨이 등을 장치로 사용할 수 있습니다. 샘플의 가상 건물에서 *Focus Room*이라는 이름의 방에 *Raspberry Pi 3 A1* 장치가 어떻게 포함되는지 살펴보세요. 각 장치 노드는 고유한 `hardwareId`를 통해 식별되며, 이 샘플에서는 이러한 ID가 하드코딩되었습니다. 실제 프로덕션 환경에 사용할 수 있도록 이 샘플을 구성하려면 이러한 ID를 해당 설정의 값으로 바꿔야 합니다.  

- **센서**: 한 장치가 여러 `sensors`를 포함할 수 있으며, 센서는 온도, 동작, 배터리 잔량 등의 물리적 변경 내용을 검색하여 기록할 수 있습니다. 각 센서 노드는 `hardwareId`를 통해 식별되며, 여기서는 이 ID가 하드코딩되었습니다. 실제 응용 프로그램의 경우 이러한 ID를 해당 설정에 사용되는 센서의 고유 식별자로 바꿔야 합니다. *provisionSample.yaml* 파일에는 *동작* 및 *이산화탄소*를 기록하는 두 개의 센서가 있습니다. 이산화탄소 센서에 대한 줄 아래에 다음 줄을 추가하여 *온도*를 기록할 또 다른 센서를 추가합니다. 이러한 줄은 *provisionSample.yaml*에 주석으로 처리되며, 각 줄 앞의 '#' 문자를 제거하여 간단하게 주석 처리를 제거할 수 있습니다. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > 위 코드 조각의 명령문을 사용하여 `dataType` 및 `hardwareId` 키를 정렬합니다. 또한 편집기가 공간을 탭으로 바꾸지 않도록 주의해야 합니다. 

*provisionSample.yaml* 파일을 저장하고 닫습니다. 그 다음 자습서에서는 이 파일에 정보를 추가하고, Azure Digital Twins 샘플 건물을 프로비전하겠습니다.


## <a name="clean-up-resources"></a>리소스 정리

앞으로 Azure Digital Twins 탐색을 중지하려는 경우 이 자습서에서 만든 리소스를 자유롭게 삭제하면 됩니다.

1. [Azure Portal](http://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, Digital Twins 리소스 그룹을 선택하여 **삭제**합니다.
2. 필요한 경우 작업 머신에서도 샘플 응용 프로그램을 삭제할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

시리즈의 그 다음 자습서로 넘어가서 샘플 건물의 조건을 모니터링하는 사용자 지정 논리를 구현하는 방법을 알아봅니다. 
> [!div class="nextstepaction"]
> [자습서: 건물을 프로비전하고 작업 조건 모니터링](tutorial-facilities-udf.md)

