---
title: Azure Digital Twins로 사용 가능한 방 찾기(C#) | Microsoft Docs
description: 이 빠른 시작에서는 시뮬레이션된 동작 및 이산화탄소 원격 분석 데이터를 Azure Digital Twins의 공간으로 보내는 두 개의 .NET Core 샘플 응용 프로그램을 실행합니다. 클라우드에서 계산 처리 후 관리 API에서 공기가 깨끗한 사용 가능한 회의실을 찾는 것이 목적입니다.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/7/2018
ms.author: alinast
ms.openlocfilehash: e11ae90655e1d4ce7a2475f0eacefb25491416da
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582397"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>빠른 시작: Azure Digital Twins로 사용 가능한 회의실 찾기

Azure Digital Twins 서비스를 사용하여 물리적 환경의 디지털 이미지를 다시 만들 수 있습니다. 그 후 환경 내 이벤트를 통해 알림을 받고 그에 대한 응답을 사용자 지정할 수 있습니다. 

이 빠른 시작에서는 [.NET 샘플 쌍](https://github.com/Azure-Samples/digital-twins-samples-csharp)을 사용하여 가상의 사무실 건물을 디지털화합니다. 해당 건물에서 사용할 수 있는 공간을 찾는 방법을 보여줍니다. Digital Twins를 사용하면 환경에 여러 센서를 연결할 수 있습니다. 이산화탄소를 측정하는 시뮬레이션된 센서를 통해 사용 가능한 회의실의 공기질이 최적 상태인지 확인할 수 있습니다. 샘플 애플리케이션 중 하나는 이 시나리오를 시각화하는 데 도움이 되는 임의의 센서 데이터를 생성합니다.

다음 비디오는 빠른 시작 설정을 요약하여 보여줍니다.

> [!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>필수 조건

1. Azure 계정이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

1. 이 빠른 시작에서 실행하는 두 개의 콘솔 애플리케이션은 C#으로 작성되었습니다. 개발 머신에 [.NET Core SDK 버전 2.1.403 이상](https://www.microsoft.com/net/download)을 설치합니다. .NET Core SDK가 설치된 경우 개발 머신에 설치된 C#의 현재 버전을 확인합니다. 명령 프롬프트에서 `dotnet --version` 명령을 실행합니다.

1. [샘플 C# 프로젝트](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip)를 다운로드합니다. digital-twins-samples-csharp-master.zip 보관 파일을 추출합니다. 


## <a name="create-a-digital-twins-instance"></a>Digital Twins 인스턴스 만들기

이 섹션의 단계에 따라 [포털](https://portal.azure.com)에서 Digital Twins의 새 인스턴스를 만듭니다.

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>앱에 대한 권한 설정

이 섹션에서는 샘플 애플리케이션이 Digital Twins 인스턴스에 액세스할 수 있도록 샘플 애플리케이션을 Azure AD(Azure Active Directory)에 등록합니다. 이미 Azure AD 앱 등록이 완료된 경우 해당 앱 등록을 샘플에 다시 사용할 수 있습니다. 이 섹션에 설명된 대로 구성되어 있는지 확인합니다. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>응용 프로그램 빌드

다음 단계에 따라 점유 애플리케이션을 빌드합니다.

1. 명령 프롬프트를 엽니다. digital-twins-samples-csharp-master.zip 파일이 추출된 폴더로 이동합니다.
1. `cd occupancy-quickstart/src`을 실행합니다.
1. `dotnet restore`을 실행합니다.
1. **appSettings.json**을 편집하여 다음 변수를 업데이트합니다.
    - **ClientId**: 이전 섹션에서 본 Azure AD 앱 등록의 애플리케이션 ID를 입력합니다.
    - **테넌트**: 마찬가지로 이전 섹션에서 본 Azure AD 테넌트의 디렉터리 ID를 입력합니다.
    - **BaseUrl**: Digital Twins 인스턴스의 관리 API URL은 `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/` 형식입니다. 이 URL의 자리 표시자를 이전 섹션의 인스턴스 값으로 바꿉니다.

## <a name="provision-graph"></a>그래프 프로비전

이 단계에서는 Digital Twins 공간 그래프에 다음을 프로비전합니다.
 
- 여러 공간
- 디바이스 1개
- 센서 2개 
- 사용자 지정 함수 
- 역할 할당 1개
 
공간 그래프는 [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml) 파일을 사용하여 프로비전됩니다.

1. `dotnet run ProvisionSample`을 실행합니다.
    >[!NOTE]
    >디바이스 로그인 Azure CLI 도구는 사용자를 Azure AD에 인증하는 데 사용됩니다. 사용자는 [Microsoft 로그인](https://microsoft.com/devicelogin) 페이지에서 제공된 코드를 입력하여 인증해야 합니다. 코드를 입력한 후 인증 단계를 따릅니다. 도구가 실행 중일 때 사용자는 인증해야 합니다.
    
    >[!TIP]
    > 이 단계를 실행할 때 `EXIT: Unexpected error: The input is not a valid Base-64 string ...` 오류 메시지가 표시되면 변수가 올바르게 복사되었는지 확인합니다.


1. 프로비전 단계는 몇 분 정도 걸릴 수 있습니다. Digital Twins 인스턴스 내에 IoT Hub도 프로비전합니다. IoT Hub가 Status=`Running`으로 표시될 때까지 반복됩니다.

    ![샘플 프로비전][4]

1. 실행이 끝나면 디바이스 시뮬레이터 샘플에 사용할 수 있도록 디바이스의 `ConnectionString`을 복사합니다. 이 이미지에 설명된 문자열만 복사하세요.

    ![샘플 프로비전][1]

## <a name="send-sensor-data"></a>센서 데이터 보내기

다음 단계에 따라 센서 시뮬레이터 애플리케이션을 빌드하고 실행합니다.

1. 새 명령 프롬프트를 엽니다. digital-twins-samples-csharp-master 폴더에 다운로드한 프로젝트로 이동합니다.
1. `cd device-connectivity`을 실행합니다.
1. `dotnet restore`을 실행합니다.
1. **appsettings.json**을 편집하여 **DeviceConnectionString**을 위의 `ConnectionString`으로 업데이트합니다.
1. `dotnet run` 명령을 실행하여 센서 데이터 전송을 시작합니다. 다음 이미지처럼 센서 데이터가 Digital Twins로 전송되는 것이 보입니다.

     ![디바이스 연결][2]

1. 다음 단계 작업을 통해 결과를 나란히 놓고 볼 수 있도록 이 시뮬레이터를 실행합니다. 이 창은 Digital Twins로 전성된 시뮬레이션된 센서 데이터를 보여줍니다. 다음 단계에서는 공기가 깨끗한 가용 회의실을 실시간으로 쿼리합니다.

    >[!TIP]
    > 이 단계를 실행할 때 `EXIT: Unexpected error: The input is not a valid Base-64 string ...` 오류 메시지가 표시되면 `DeviceConnectionString`이 올바르게 복사되었는지 확인합니다.

## <a name="find-available-spaces-with-fresh-air"></a>공기가 깨끗한 가용 회의실 찾기

센서 샘플은 두 센서의 임의 데이터 값을 시뮬레이션합니다. 두 센서는 동작 및 이산화탄소 센서입니다. 이 샘플에서 말하는 공기가 깨끗한 가용 회의실이란 아무도 없고 이산화탄소 농도가 1,000ppm 미만인 회의실이라고 정의되어 있습니다. 조건이 충족되지 않으면 공간을 사용할 수 없거나 공기질이 좋지 않은 것입니다.

1. 이전 프로비전 단계를 실행할 때 사용한 명령 프롬프트를 엽니다.
1. `dotnet run GetAvailableAndFreshSpaces`을 실행합니다.
1. 이 명령 프롬프트와 센서 데이터 명령 프롬프트를 나란히 놓고 살펴봅니다. 

    한 명령 프롬프트가 5초마다 시뮬레이션된 동작 및 이산화탄소 데이터를 Digital Twins로 보냅니다. 다른 명령 프롬프트는 실시간으로 그래프를 읽고 임의의 시뮬레이션된 데이터를 기반으로 공기가 깨끗한 가용 회의실을 찾습니다. 마지막으로 전송된 센서 데이터를 기준으로 다음 조건 중 하나를 거의 실시간으로 표시합니다.
    - 공기가 깨끗한 가용 회의실.
    - 다른 사람이 사용 중이거나 공기질이 좋지 않음.

     ![공기가 깨끗한 가용 회의실 가져오기][3]

이 빠른 시작에서 어떤 일이 발생했고 어떤 API가 호출되었는지 이해하려면 digital-twins-samples-csharp에서 찾은 코드 작업 영역 프로젝트를 사용하여 [Visual Studio Code](https://code.visualstudio.com/Download)를 엽니다. 다음 명령을 사용합니다.

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

이 자습서에서는 코드를 심층적으로 살펴봅니다. 구성 데이터를 수정하는 방법 및 어떤 API가 호출되는지 살펴봅니다. 관리 API에 대한 자세한 내용을 보려면 Digital Twins Swagger 페이지로 이동하세요.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| 이름 | 다음 항목으로 교체 |
| --- | --- |
| YOUR_INSTANCE_NAME | Digital Twins 인스턴스의 이름 |
| YOUR_LOCATION | 인스턴스를 호스팅하는 서버 지역 |

또는 간편하게 [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger)로 이동합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서는 다음 작업 방법을 자세히 설명합니다. 

- 시설 관리자가 시설 사용자 생산성을 높일 수 있는 애플리케이션을 빌드합니다. 
- 건물을 보다 효율적으로 운영합니다.

자습서를 계속 진행하려면 이 빠른 시작에서 만든 리소스를 지우지 마세요. 자습서를 더 이상 진행할 계획이 없으면 이 빠른 시작에서 만든 모든 리소스를 삭제하세요.

1. 샘플 리포지토리를 다운로드할 때 생성된 폴더를 삭제하세요.
1. [Azure Portal](http://portal.azure.com)의 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 그런 다음, Digital Twins 리소스를 선택합니다. **모든 리소스** 창의 위쪽에서 **삭제**를 선택합니다.
   
    > [!TIP]
    > Digital Twins 인스턴스를 삭제하는 데 문제가 있을 경우 픽스가 포함된 서비스 업데이트가 배포된 것입니다. 인스턴스 삭제를 다시 시도해 보세요.


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 간단한 시나리오를 사용하여 작업 조건이 양호한 회의실을 찾을 방법을 보여주었습니다. 이 시나리오에 대한 자세한 분석 내용은 다음 자습서를 참조하세요.

> [!div class="nextstepaction"]
> [자습서: Azure Digital Twins를 배포하고 공간 그래프 구성](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
