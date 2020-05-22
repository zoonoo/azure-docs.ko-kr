---
title: Azure SQL Edge에서 IoT Edge 모듈 설정
description: 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 Azure SQL Edge 자습서 중 2부에서는 IoT Edge 모듈 및 연결을 설정합니다.
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: bbbbe09aac30165a2f9b7bbe54f58e0c09a6cf09
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593502"
---
# <a name="set-up-iot-edge-modules-and-connections"></a>IoT Edge 모듈 및 연결 설정

Azure SQL Edge에서 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 자습서 중 2부에서는 다음과 같은 IoT Edge 모듈을 설정합니다.

- Azure SQL Edge
- 데이터 생성기 IoT Edge 모듈

## <a name="create-azure-stream-analytics-module"></a>Azure Stream Analytics 모듈 만들기

이 자습서에서 사용되는 Azure Stream Analytics 모듈을 만듭니다. SQL Edge에서 스트리밍 작업을 사용하는 방법에 대한 자세한 내용은 [SQL Database Edge에서 스트리밍 작업 사용](https://docs.microsoft.com/azure/sql-database-edge/stream-analytics#using-streaming-jobs-with-sql-database-edge)을 참조하세요.

호스팅 환경을 Edge로 설정하여 Azure Stream Analytics 작업을 만든 후에는 자습서에 대한 입력 및 출력을 설정합니다.

1. **입력**을 만들려면 **+스트림 입력 추가**를 클릭합니다. 다음 정보를 사용하여 세부 정보 섹션에 정보를 입력합니다.

   필드|값
   -----|-----
   이벤트 직렬화 형식|JSON
   Encoding|UTF-8
   이벤트 압축 유형|None

2. **출력**을 만들려면 **+추가**를 클릭하고 SQL Database를 선택합니다. 다음 정보를 사용하여 세부 정보 섹션을 채웁니다.

   > [!NOTE]
   > 이 섹션에 지정된 암호는 **“Azure SQL Edge 모듈 배포”** 섹션에서 SQL Edge 모듈을 배포할 때 SQL SA 암호를 지정하는 데 필요합니다.

   필드|값
   -----|-----
   데이터베이스|IronOreSilicaPrediction
   서버 이름|tcp:.,1433
   사용자 이름|sa
   암호|강력한 암호를 지정
   테이블|IronOreMeasurements1

3. **쿼리** 섹션으로 이동하고 다음과 같이 쿼리를 설정합니다.

   `SELECT * INTO <name_of_your_output_stream> FROM <name_of_your_input_stream>`
   
4. **구성**에서 **게시**를 선택한 다음, **게시** 단추를 선택합니다. SQL Database Edge 모듈에 사용할 SAS URI를 저장합니다.

## <a name="specify-container-registry-credentials"></a>컨테이너 레지스트리 자격 증명 지정

모듈 이미지를 호스팅하는 컨테이너 레지스트리에 대한 자격 증명을 지정해야 합니다. 이러한 자격 증명은 리소스 그룹에서 만든 컨테이너 레지스트리에서 찾을 수 있습니다. **액세스 키** 섹션으로 이동합니다. 다음 필드를 확인합니다.

- 레지스트리 이름
- 로그인 서버
- 사용자 이름
- 암호

이제 IoT Edge 모듈에서 컨테이너 자격 증명을 지정합니다.

1. 리소스 그룹에서 만든 IoT 허브로 이동합니다.

2. **자동 디바이스 관리**에 있는 **IoT Edge** 섹션에서 **디바이스 ID**를 클릭합니다. 이 자습서에서는 ID가 `IronOrePredictionDevice`입니다.

3. **모듈 설정** 섹션을 선택합니다.

4. **컨테이너 레지스트리 자격 증명**에서 다음 값을 입력합니다.

   _필드_|_값_
   -------|-------
   속성|레지스트리 이름
   주소|로그인 서버
   사용자 이름|사용자 이름
   암호|암호
  
## <a name="deploy-the-data-generator-module"></a>데이터 생성기 모듈 배포

1. **IoT Edge 모듈** 섹션에서 **+추가**를 클릭하고 **IoT Edge 모듈**을 선택합니다.

2. IoT Edge 모듈 이름과 이미지 URI를 제공합니다.
   이미지 URI는 리소스 그룹의 컨테이너 레지스트리에서 찾을 수 있습니다. **서비스**에서 **리포지토리** 섹션을 선택합니다. 이 자습서에서는 `silicaprediction`이라는 리포지토리를 선택합니다. 적절한 태그를 선택합니다. 이미지 URI는 다음과 같은 형식입니다.

   *컨테이너 레지스트리의 로그인 서버*/*리포지토리 이름*:*태그 이름*

   다음은 그 예입니다.

   ```
   ASEdemocontregistry.azurecr.io/silicaprediction:amd64
   ```

3. **추가**를 클릭합니다.

## <a name="deploy-the-azure-sql-edge-module"></a>Azure SQL Edge 모듈 배포

1. [Azure SQL Database Edge 배포 미리 보기](https://docs.microsoft.com/azure/sql-database-edge/deploy-portal#deploy-sql-database-edge)에 나열된 단계를 수행하여 Azure SQL Edge 모듈을 배포합니다.

2. **모듈 설정** 페이지의 **경로 지정**에서 다음과 같이 모듈의 경로를 IoT Edge 허브 통신으로 지정합니다. 

   ```
   FROM /messages/modules/<your_data_generator_module>/outputs/<your_output_stream_name> INTO
   BrokeredEndpoint("/modules/<your_azure_sql_edge_module>/inputs/<your_input_stream_name>")
   ```

   다음은 그 예입니다.

   ```
   FROM /messages/modules/ASEDataGenerator/outputs/IronOreMeasures INTO BrokeredEndpoint("/modules/AzureSQLEdge/inputs/Input1")
   ```

3. **모듈 쌍** 설정에서 자습서의 앞부분에서 저장한 관련 SAS URL을 사용하여 SQLPackage 및 ASAJonInfo를 업데이트해야 합니다.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

## <a name="next-steps"></a>다음 단계

- [ONNX를 사용하여 Azure SQL Edge에 ML 모델 배포](tutorial-run-ml-model-on-sql-edge.md)
