---
title: ONNX를 사용하여 Azure SQL Edge에 ML 모델 배포
description: 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 Azure SQL Edge 자습서 중 3부에서는 SQL Edge에서 ONNX 기계 학습 모델을 실행합니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 9e5bb037b88b7c370e31d05c2d20fc6f558a8b39
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422198"
---
# <a name="deploy-ml-model-on-azure-sql-edge-using-onnx"></a>ONNX를 사용하여 Azure SQL Edge에 ML 모델 배포 

Azure SQL Edge에서 철광석 불순물 예측을 위해 세 부분으로 구성된 이번 자습서 중 3부에서는 다음을 수행합니다.

1. Azure Data Studio를 사용하여 Azure SQL Edge 인스턴스의 SQL Database에 연결합니다.
2. Azure SQL Edge에서 ONNX로 철광석 불순물을 예측합니다.

## <a name="key-components"></a>주요 구성 요소

1. 이 솔루션은 Edge Hub로 전송되는 각 메시지 사이에 기본 500밀리초를 사용합니다. 이는 **Program.cs** 파일에서 변경할 수 있습니다. 
   ```json
   TimeSpan messageDelay = configuration.GetValue("MessageDelay", TimeSpan.FromMilliseconds(500));
   ```
2. 솔루션은 다음과 같은 특성을 가진 메시지를 생성했습니다. 요구 사항에 따라 특성을 추가하거나 제거합니다. 
```json
{
    timestamp 
    cur_Iron_Feed
    cur_Silica_Feed 
    cur_Starch_Flow 
    cur_Amina_Flow 
    cur_Ore_Pulp_pH
    cur_Flotation_Column_01_Air_Flow
    cur_Flotation_Column_02_Air_Flow
    cur_Flotation_Column_03_Air_Flow
    cur_Flotation_Column_04_Air_Flow
    cur_Flotation_Column_01_Level
    cur_Flotation_Column_02_Level
    cur_Flotation_Column_03_Level
    cur_Flotation_Column_04_Level
    cur_Iron_Concentrate
}
```

## <a name="connect-to-the-sql-database-in-the-azure-sql-edge-instance-to-train-deploy-and-test-the-ml-model"></a>Azure SQL Edge 인스턴스의 SQL Database에 연결하여 ML 모델 학습, 배포 및 테스트

1. Azure Data Studio를 엽니다.

2. **시작** 탭에서 다음 정보를 사용하여 새 연결을 시작합니다.

   |_필드_|_값_|
   |-------|-------|
   |연결 형식| Microsoft SQL Server|
   |서버|이 데모에 대해 생성된 VM에서 언급된 공용 IP 주소|
   |사용자 이름|sa|
   |암호|Azure SQL Edge 인스턴스를 만드는 동안 사용된 강력한 암호입니다.|
   |데이터베이스|기본값|
   |서버 그룹|기본값|
   |이름(선택 사항)|필요에 따라 이름을 입력합니다.|

3. **연결**

4. **파일** 섹션에서 머신의 프로젝트 파일을 복제한 폴더에서 **/DeploymentScripts/MiningProcess_ONNX.jpynb** 를 엽니다.

5. 커널을 Python 3으로 설정합니다.


## <a name="next-steps"></a>다음 단계

Azure SQL Edge에서의 ONNX 모델 사용에 대한 자세한 내용은 [SQL Edge에서 ONNX를 통한 기계 학습 및 AI](onnx-overview.md)를 참조하세요.