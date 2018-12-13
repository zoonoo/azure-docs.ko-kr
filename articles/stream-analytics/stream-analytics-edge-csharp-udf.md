---
title: Visual Studio(Preview)에서 Azure Stream Analytics Edge 작업용 C# 사용자 정의 함수 작성
description: Visual Studio에서 Stream Analytics Edge 작업용으로 C# 사용자 정의 함수를 작성하는 방법을 알아봅니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: d593930705c7455d03e20af2affd2de3c418d4a5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389071"
---
# <a name="tutorial-write-a-c-user-defined-function-for-an-azure-stream-analytics-edge-job-in-visual-studio-preview"></a>자습서: Visual Studio(Preview)에서 Azure Stream Analytics Edge 작업용 C# 사용자 정의 함수 작성

Visual Studio에서 만든 C# UDF(사용자 정의 함수)를 사용하면 원하는 함수로 Azure Stream Analytics 쿼리 언어를 확장할 수 있습니다. C#에서는 DLL을 비롯한 기존 코드를 다시 사용할 수 있으며 수학 논리나 복합 논리도 사용할 수 있습니다. UDF는 세 가지 방식으로 구현합니다. Stream Analytics 프로젝트의 CodeBehind 파일로 구현하거나, 로컬 C# 프로젝트의 UDF로 구현하거나, 저장소 계정의 기존 패키지에 포함된 UDF로 구현할 수 있습니다. 이 자습서에서는 CodeBehind 방법을 사용하여 기본적인 C# 함수를 구현합니다. Stream Analytics Edge 작업용 UDF 함수는 현재 미리 보기로 제공되며 프로덕션 워크로드에는 사용해서는 안 됩니다.

이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * CodeBehind를 사용하여 C# 사용자 정의 함수 작성
> * 로컬에서 Stream Analytics Edge 작업 테스트
> * Azure에 Edge 작업 게시

## <a name="prerequisites"></a>필수 조건

시작하기 전에 다음 필수 조건을 모두 갖추었는지 확인합니다.

* Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Visual Studio용 Stream Analytics 도구](stream-analytics-tools-for-visual-studio-install.md) 및 **Azure 개발** 또는 **데이터 저장소 및 처리** 워크로드를 설치합니다.
* 기존 [Stream Analytics Edge 개발 가이드](stream-analytics-tools-for-visual-studio-edge-jobs.md)를 살펴봅니다.

## <a name="create-a-container-in-your-azure-storage-account"></a>Azure 저장소 계정에 컨테이너 만들기

여기서 만드는 컨테이너는 컴파일된 C# 패키지를 저장하고 IoT Edge 디바이스에 해당 패키지를 배포하는 데 사용됩니다. 각 Stream Analytics 작업 전용 컨테이너를 사용하세요. 여러 Stream Analytics Edge 작업에 같은 컨테이너를 다시 사용할 수는 없습니다. 기존 컨테이너가 포함된 저장소 계정이 이미 있는 경우에는 해당 컨테이너를 사용하면 됩니다. 그렇지 않은 경우에는 [새 컨테이너를 만드세요](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). 

## <a name="create-a-stream-analytics-edge-project-in-visual-studio"></a>Visual Studio에서 Stream Analytics Edge 프로젝트 만들기

1. Visual Studio를 시작합니다.

2. **파일 > 새로 만들기 > 프로젝트**를 선택합니다.

3. 왼쪽의 템플릿 목록에서 **Stream Analytics**를 선택한 다음 **Azure Stream Analytics Edge 응용 프로그램**을 선택합니다.

4.  프로젝트 **이름**, **위치** 및 **솔루션 이름**을 입력하고, **확인**을 선택합니다.

    ![Visual Studio에서 Azure Stream Analytics Edge 프로젝트 만들기](./media/stream-analytics-edge-csharp-udf/stream-analytics-create-edge-app.png)

## <a name="configure-assembly-package-path"></a>어셈블리 패키지 경로 구성

1. Visual Studio를 열고 **솔루션 탐색기**로 이동합니다.

2. 작업 구성 파일 `EdgeJobConfig.json`을 두 번 클릭합니다.

3. **사용자 정의 코드 구성** 섹션을 확장하고 다음 제안 값으로 구성을 입력합니다.

    |**설정**  |**제안 값**  |
    |---------|---------|
    |어셈블리 원본  |  로컬 프로젝트 참조 또는 CodeBehind   |
    |리소스  |  현재 계정의 데이터 선택   |
    |구독  |  구독을 선택합니다.   |
    |Storage 계정  |  저장소 계정 선택   |
    |컨테이너  |  저장소 계정에서 만든 컨테이너 선택   |

    ![Visual Studio의 Azure Stream Analytics Edge 작업 구성](./media/stream-analytics-edge-csharp-udf/stream-analytics-edge-job-config.png)


## <a name="write-a-c-udf-with-codebehind"></a>CodeBehind를 사용하여 C# UDF 작성
CodeBehind 파일은 ASA Edge 쿼리 스크립트 하나와 연결된 C# 파일입니다. 제출하는 CodeBehind 파일은 Visual Studio에서 자동 압축되어 Azure 저장소 계정에 업로드됩니다. 모든 클래스는 *public*으로, 모든 개체는 *static public*으로 정의해야 합니다.

1. **솔루션 탐색기**에서 **Script.asql**를 확장하여 **Script.asaql.cs** CodeBehind 파일을 찾습니다.

2. 코드를 다음 샘플로 바꿉니다.

    ```csharp
        using System; 
        using System.Collections.Generic; 
        using System.IO; 
        using System.Linq; 
        using System.Text; 
    
        namespace ASAEdgeUDFDemo 
        { 
            public class Class1 
            { 
                // Public static function 
                public static Int64 SquareFunction(Int64 a) 
                { 
                    return a * a; 
                } 
            } 
        } 
    ```

## <a name="implement-the-udf"></a>UDF 구현

1. **솔루션 탐색기**에서 **Script.asaql** 파일을 엽니다.

2. 기존 쿼리를 다음으로 바꿉니다.

    ```sql
        SELECT machine.temperature, udf.ASAEdgeUDFDemo_Class1_SquareFunction(try_cast(machine.temperature as bigint))
        INTO Output
        FROM Input 
    ```

## <a name="local-testing"></a>로컬 테스트

1. Edge [온도 시뮬레이터 샘플 데이터 파일](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Sample%20Data/TemperatureSampleData.json)을 다운로드합니다.

2. **솔루션 탐색기**에서 **입력**을 확장하고 **Input.json**을 마우스 오른쪽 단추로 클릭한 후에 **로컬 입력 추가**를 선택합니다.

   ![Visual Studio에서 Azure Stream Analytics 작업에 로컬 입력 추가](./media/stream-analytics-edge-csharp-udf/stream-analytics-add-local-input.png)

3. 다운로드한 샘플 데이터의 로컬 입력 파일 경로를 지정하고 파일을 **저장**합니다.

    ![Visual Studio의 Azure Stream Analytics 작업용 로컬 입력 구성](./media/stream-analytics-edge-csharp-udf/stream-analytics-local-input-config.png)

4. 스크립트 편집기에서 **로컬에서 실행**을 클릭합니다. 로컬 실행에서 출력 결과가 정상적으로 저장되면 아무 키나 눌러 표 형식으로 결과를 확인합니다. 

    ![Visual Studio를 사용하여 로컬에서 Azure Stream Analytics 작업 실행](./media/stream-analytics-edge-csharp-udf/stream-analytics-run-locally.png)

5. **결과 폴더 열기**를 선택하여 JSON 및 CSV 형식의 원시 파일을 확인할 수도 있습니다.

    ![Visual Studio를 사용하여 로컬 Azure Stream Analytics 작업의 결과 확인](./media/stream-analytics-edge-csharp-udf/stream-analytics-view-local-results.png)

## <a name="debug-a-udf"></a>UDF 디버그
표준 C# 코드를 디버그하는 것과 같은 방식으로 C# UDF를 로컬로 디버그할 수 있습니다. 

1. C# 함수에 중단점을 추가합니다.

    ![Visual Studio에서 Edge 작업용 Azure Stream Analytics 사용자 정의 함수에 중단점 추가](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-breakpoints.png)

2. **F5** 키를 눌러 디버깅을 시작합니다. 프로그램이 중단점에서 정상적으로 중지됩니다.

    ![Visual Studio 디버깅 결과에서 Edge 작업용 Azure Stream Analytics 사용자 정의 함수 확인](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-debug.png)

## <a name="publish-your-job-to-azure"></a>Azure에 작업 게시
쿼리를 로컬로 테스트한 다음 스크립트 편집기에서 **Azure에 제출**을 선택하여 작업을 Azure에 게시합니다.

![Visual Studio에서 Azure에 Stream Analytics Edge 작업 제출](./media/stream-analytics-edge-csharp-udf/stream-analytics-udf-submit-job.png)

## <a name="deploy-to-iot-edge-devices"></a>IoT Edge 디바이스에 배포
이제 Stream Analytics 작업을 IoT Edge 모듈로 배포할 준비가 되었습니다. [IoT Edge 빠른 시작](https://docs.microsoft.com/azure/iot-edge/quickstart)의 설명에 따라 IoT Hub를 만들고, IoT Edge 장치를 등록하고, 장치에서 IoT Edge 런타임을 설치 및 시작합니다. 그런 다음 [작업 배포](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics#deploy-the-job) 자습서의 설명에 따라 Stream Analytics 작업을 IoT Edge 모듈로 배포합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 CodeBehind를 사용하여 간단한 C# 사용자 정의 함수를 만들고, Azure에 작업을 게시하고, IoT Hub 포털을 사용하여 IoT Edge 디바이스에 작업을 배포했습니다. 

Stream Analytics Edge 작업에 C# 사용자 정의 함수를 사용하는 여러 방법에 대해 자세히 알아보려면 다음 문서를 계속 확인하세요.

> [!div class="nextstepaction"]
> [Azure Stream Analytics용 C# 사용자 정의 함수 작성](stream-analytics-edge-csharp-udf-methods.md)
