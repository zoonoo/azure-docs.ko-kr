---
title: 사용자 고유의 ML을 Azure 센티널로 가져오기 | Microsoft Docs
description: 이 문서에서는 Azure 센티널에서 데이터 분석을 위해 고유한 기계 학습 알고리즘을 만들고 사용 하는 방법을 설명 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 17c0ba7306ab4cc51fe8bbe3709d5b6bc85fa487
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91347508"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>사용자 고유의 Machine Learning (ML)를 Azure 센티널로 가져오기

ML (Machine Learning)은 Azure 센티널의 주요 기초가 되 중 하나 이며, 별도로 설정 하는 기본 특성 중 하나입니다. Azure 센티널은 기본 제공 되는 [Fusion](fusion.md) 상관 엔진 및 Jupyter 노트북, 새로 제공 되는 BYO ML (ml) 플랫폼 등 여러 가지 환경에서 ML을 제공 합니다. 

ML 검색 모델은 개별 환경 및 사용자 동작 변경에 맞게 조정 하 여 가양성을 줄이고 일반적인 방법으로 찾을 수 없는 위협을 식별할 수 있습니다. 많은 보안 조직에서는 보안을 위해 ML의 가치를 이해 하 고 있으며,이 중 상당수는 보안과 ML에 대 한 전문 지식을 가진 전문가의 luxury을가지고 있지 않습니다. 여기에서 제공 하는 프레임 워크는 보안 조직과 전문가 들이 ML 경험에 맞게 성장 하도록 설계 되었습니다. ML에 대 한 새로운 조직이 나 필요한 전문 지식이 없는 조직은 Azure 센티널의 기본 제공 ML 기능에서 중요 한 보호 가치를 얻을 수 있습니다.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="기계 학습 프레임 워크":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>사용자 고유의 Machine Learning (BYO) 플랫폼은 무엇 인가요?

ML 리소스가 있고 고유한 비즈니스 요구 사항에 맞게 사용자 지정 된 ML 모델을 빌드하려고 하는 조직의 경우 **BYO 플랫폼**을 제공 합니다. 플랫폼은 [Azure Databricks](https://docs.microsoft.com/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) 환경 및 jupyter 노트북을 사용 하 여 ML 환경을 만듭니다. 다음 구성 요소를 제공 합니다.

- 데이터에 액세스 하 고 결과를 BYO (LA) Log Analytics로 푸시하는 데 도움이 되는 라이브러리를 포함 하는 ML 패키지는 검색, 조사 및 검색에 결과를 통합할 수 있습니다. 

- 조직의 특정 보안 문제에 맞게 사용자 지정할 수 있는 ML 알고리즘 템플릿입니다. 

- 모델을 학습 하 고 모델 점수 매기기를 예약 하는 샘플 노트북. 

이 외에도 고유한 ML 모델 및/또는 고유한 Spark 환경을 사용 하 여 Azure 센티널과 통합할 수 있습니다.

BYO 플랫폼을 사용 하 여 고유한 ML 모델 빌드에 대 한 빠른 시작을 받을 수 있습니다. 

- 샘플 데이터가 포함 된 노트북은 프로덕션 데이터를 처리 하는 것에 대해 걱정 하지 않고 실습 환경을 완벽 하 게 활용할 수 있도록 도와줍니다.

- Spark 환경에 통합 된 패키지를 사용 하면 인프라를 관리 하는 데 따른 문제와 마찰 줄어듭니다.

- 라이브러리는 데이터 이동을 지원 합니다. 노트북 교육 및 점수 매기기는 종단 간 환경을 보여 주고 사용자 환경에 맞게 변경할 수 있는 템플릿 역할을 합니다.

### <a name="use-cases"></a>사용 사례
 
BYO 플랫폼 및 패키지는 고유한 ML 검색을 작성 하는 데 필요한 시간과 노력을 크게 줄여 Azure 센티널의 특정 보안 문제를 해결 하는 기능을 발휘할 합니다. 플랫폼은 다음과 같은 사용 사례를 지원 합니다.

**ML 알고리즘을 학습 하 여 사용자 지정 모델을 가져옵니다.** 기존 ML 알고리즘 (Microsoft 또는 사용자 커뮤니티에서 공유)을 사용 하 여 데이터 및 환경에 더 적합 한 사용자 지정 ML 모델을 얻기 위해 자신의 데이터를 쉽게 학습할 수 있습니다.

**사용자 지정 모델을 가져오도록 ML 알고리즘 템플릿을 수정 합니다.** ML 알고리즘 템플릿 (Microsoft 또는 사용자 커뮤니티에서 공유)을 수정 하 고 사용자 고유의 데이터에 수정 된 알고리즘을 학습 하 여 특정 문제에 맞게 사용자 지정 된 모델을 파생 시킬 수 있습니다.

**사용자 고유의 모델을 만듭니다.** Azure 센티널의 BYO 플랫폼과 유틸리티를 사용 하 여 처음부터 직접 모델을 만듭니다.

**Databricks/Spark 환경을 통합 합니다.** 기존 Databricks/Spark 환경을 Azure 센티널에 통합 하 고 BYO 라이브러리 및 템플릿을 사용 하 여 고유한 상황에 맞는 ML 모델을 빌드합니다.

**사용자 고유의 ML 모델을 가져옵니다.** 사용자 고유의 ML 모델을 가져오고 BYO 플랫폼과 유틸리티를 사용 하 여 Azure 센티널과 통합할 수 있습니다.

**ML 알고리즘 공유:** 커뮤니티에서 도입 하 고 적응 하는 ML 알고리즘을 공유 합니다.

**ML을 사용 하 여 SecOps의 역량** 을 강화 합니다. 사용자 지정 ml 모델 및 검색, 검색, 조사 및 응답에 대 한 결과를 사용 합니다.

이 문서에서는 BYO 플랫폼의 구성 요소와 플랫폼 및 비정상 리소스 액세스 알고리즘을 활용 하 여 Azure 센티널로 사용자 지정 된 ML 검색을 제공 하는 방법을 보여 줍니다.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark 환경

[Apache Spark™](http://spark.apache.org/) 는 데이터 파이프라인을 빌드하기 위한 통합 프레임 워크를 제공 하 여 빅 데이터를 단순화 합니다. Azure Databricks는 Spark를 중심으로 구축 된 제로 관리 클라우드 플랫폼을 제공 하 여이를 추가로 수행 합니다. BYO 플랫폼에 Databricks를 사용 하는 것이 좋습니다. 따라서 데이터 파이프라인 및 플랫폼 문제를 주요 당면 않고 비즈니스에 즉시 영향을 주는 대답을 찾는 데 집중할 수 있습니다.
Databricks 또는 다른 Spark 환경이 이미 있는 경우 기존 설치 프로그램을 사용 하는 것을 선호 하는 경우 BYO 패키지도 제대로 작동 합니다. 

## <a name="byo-ml-package"></a>BYO-ML 패키지

BYO ML 패키지에는 보안을 위해 ML의 프런트 엔드에 있는 Microsoft의 모범 사례 및 연구를 포함 합니다. 이 패키지에서는 보안 문제에 대 한 다음 유틸리티, 노트북 및 알고리즘 템플릿 목록을 제공 합니다.

| 파일 이름 | Description |
| --------- | ----------- |
| azure_sentinel_utilities. | Azure에서 blob을 읽고 Log Analytics에 쓰는 유틸리티를 포함 합니다. |
| AnomalousRASampleData | 노트북에서는 생성 된 학습 및 테스트 샘플 데이터와 함께 센티널에서 비정상적인 리소스 액세스 모델을 사용 하는 방법을 보여 줍니다. |
| AnomalousRATraining | 모델을 학습 하 고 모델을 작성 하 고 저장 하기 위한 노트북. |
| AnomalousRAScoring | 모델 실행을 예약 하 고 결과를 시각화 하 고 점수를 다시 Azure 센티널로 쓰는 노트북입니다. |
|

제공 하는 첫 번째 ML 알고리즘 템플릿은 [비정상적인 리소스 액세스 검색](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)을 위한 것입니다. 공동 작업 필터링 알고리즘을 기반으로 하며 Windows 파일 공유 액세스 로그 (이벤트 ID가 5140 인 보안 이벤트)를 사용 하 여 학습 합니다. 로그에서이 모델에 필요한 주요 정보는 액세스 한 사용자와 리소스의 쌍입니다. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>예제 연습: 비정상적인 파일 공유 액세스 검색 

이제 BYO 플랫폼의 주요 구성 요소에 대해 잘 알고 있으므로 플랫폼 및 구성 요소를 사용 하 여 사용자 지정 ML 검색을 제공 하는 방법을 보여 주는 예제는 다음과 같습니다.

### <a name="setup-the-databricksspark-environment"></a>Databricks/Spark 환경 설정

사용자 고유의 Databricks 환경을 아직 설치 하지 않은 경우 설치 해야 합니다. 지침은 [Databricks 빠른](https://docs.microsoft.com/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) 시작 문서를 참조 하세요.

### <a name="auto-export-instruction"></a>자동 내보내기 명령

센티널의 사용자 고유의 데이터를 기반으로 사용자 지정 ML 모델을 작성 하려면 ML 모델이 Databricks에서 액세스할 수 있도록 데이터를 Log Analytics에서 Blob 저장소 또는 이벤트 허브 리소스로 내보내야 합니다. [Azure 센티널로 데이터를 수집](connect-data-sources.md)하는 방법을 알아봅니다.

이 예에서는 Azure blob storage에서 파일 공유 액세스 로그에 대 한 학습 데이터가 필요 합니다. 데이터 형식은 노트북 및 라이브러리에 설명 되어 있습니다.

[AZURE CLI (명령줄 인터페이스)](https://docs.microsoft.com/cli/azure/monitor/log-analytics)를 사용 하 여 Log Analytics에서 데이터를 자동으로 내보낼 수 있습니다. 

명령을 실행 하기 위해 Log Analytics 작업 영역, 저장소 계정 및 EventHub 리소스에서 **참가자** 역할을 할당 받아야 합니다. 

자동 내보내기를 설정 하는 명령의 샘플 집합은 다음과 같습니다.

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>사용자 지정 데이터 내보내기

자동 내보내기 Log Analytics에서 지원 하지 않는 사용자 지정 데이터의 경우 논리 앱 또는 다른 솔루션을 사용 하 여 데이터를 이동할 수 있습니다. [Blob 저장소로 Log Analytics 데이터 내보내기](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) 블로그 및 스크립트를 참조할 수 있습니다.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Azure 센티널 외부의 데이터와 상호 연결

Azure 센티널 외부에서 blob 저장소 또는 이벤트 허브로 데이터를 가져와 센티널 데이터와 상호 연결 하 여 ML 모델을 빌드할 수도 있습니다. 
 
### <a name="copy-and-install-the-related-packages"></a>관련 패키지 복사 및 설치

위에서 언급 한 Azure 센티널 GitHub 리포지토리에서 BYO 패키지를 Databricks 환경에 복사 합니다. 그런 다음 노트북을 열고 노트북 내의 지침에 따라 클러스터에 필요한 라이브러리를 설치 합니다.

### <a name="model-training-and-scoring"></a>모델 학습 및 점수 매기기

두 노트북의 지침에 따라 사용자 환경 및 리소스에 따라 구성을 변경 하 고, 모델을 학습 하 고 작성 하는 단계를 따르고, 들어오는 파일 공유 액세스 로그의 점수를 매기는 모델을 예약 합니다.

### <a name="write-results-to-log-analytics"></a>Log Analytics에 결과를 씁니다.

예약 된 점수 매기기를 받은 후 점수 매기기 노트북에서 모듈을 사용 하 여 Azure 센티널 인스턴스와 연결 된 Log Analytics 작업 영역에 점수 매기기 결과를 쓸 수 있습니다.

### <a name="check-results-in-azure-sentinel"></a>Azure 센티널에서 결과 확인

점수가 매겨진 결과와 관련 로그 세부 정보를 보려면 Azure 센티널 포털로 다시 이동 합니다. 사용자 지정 로그 > **로그** 에 **AnomalousResourceAccessResult_CL** 테이블 또는 사용자 지정 테이블 이름에 결과가 표시 됩니다. 이러한 결과를 사용 하 여 조사 및 구하기 환경을 개선할 수 있습니다.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="비정상적인 리소스 액세스 로그":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>ML 결과를 사용 하 여 사용자 지정 분석 규칙 빌드

ML 결과가 사용자 지정 로그 테이블에 있는지 확인 하 고 점수가 정확도에 만족 하는 경우 결과에 따라 검색을 만들 수 있습니다. Azure 센티널 포털에서 **분석** 으로 이동 하 여 [새 검색 규칙을 만듭니다](tutorial-detect-threats-custom.md). 다음은 검색을 만드는 데 사용 되는 쿼리를 보여 주는 예제입니다.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="B Y O M L 검색에 대 한 사용자 지정 분석 규칙 만들기":::

### <a name="view-and-respond-to-incidents"></a>인시던트 보기 및 대응
ML 결과에 따라 분석 규칙을 설정 하 고 나면 쿼리에서 설정한 임계값을 초과 하는 결과가 생성 되 면 인시던트가 생성 되어 Azure 센티널의 **인시던트** 페이지에 표시 됩니다. 

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure 센티널의 BYO 플랫폼을 사용 하 여 데이터를 분석 하 고 위협을 감지 하는 고유한 기계 학습 알고리즘을 만들거나 가져오는 방법을 배웠습니다.

- [Azure 센티널 블로그의](https://aka.ms/azuresentinelblog)machine learning 및 기타 관련 항목에 대 한 게시물을 참조 하세요.
