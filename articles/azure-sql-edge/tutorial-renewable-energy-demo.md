---
title: Contoso 풍력 발전소의 터빈에 Azure SQL Edge 배포
description: 이 자습서에서는 Contoso 풍력 발전소의 터빈에서 wake-detection을 위해 Azure SQL Edge를 사용합니다.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 12/18/2020
ms.openlocfilehash: e966d756744210dc8f6739b96501dd91f0d8d1b5
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97723476"
---
# <a name="using-azure-sql-edge-to-build-smarter-renewable-resources"></a>Azure SQL Edge를 사용하여 더 효율적으로 갱신 가능한 리소스 빌드

이 Azure SQL Edge 데모는 생성기를 등록하는 데이터 처리를 위해 SQL DB 에지를 사용하는 풍력 터빈 팜인 Contoso 갱신 가능한 에너지를 기반으로 합니다. 

이 데모에서는 디바이스에서 감지되는 풍량 때문에 발생하는 경고를 해결하는 방법을 안내합니다. 모델을 학습하고, 감지된 바람 절전 모드를 수정하고, 궁극적으로 전원 출력을 최적화하는 SQL DB Edge에 배포합니다.

Azure SQL Edge - 채널 9의 갱신 가능한 에너지 데모 비디오:
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Azure-SQL-Edge-Demo-Renewable-Energy/player]

## <a name="setting-up-the-demo-on-your-local-computer"></a>로컬 컴퓨터에서 데모 설정
Git은 데모의 모든 파일을 로컬 컴퓨터로 복사하는 데 사용됩니다. 

1. [여기](https://git-scm.com/download)에서 git을 설치합니다.
2. 명령 프롬프트를 열고 리포지토리를 다운로드해야 하는 폴더로 이동합니다. 
3. 명령 https://github.com/microsoft/sql-server-samples.git를 실행합니다.
4. 리포지토리가 복제된 위치에서 **'sql-server-samples\samples\demos\azure-sql-edge-demos\Wind Turbine Demo'** 로 이동합니다.
5. README.md의 지침에 따라 데모 환경을 설정하고 데모를 실행합니다.