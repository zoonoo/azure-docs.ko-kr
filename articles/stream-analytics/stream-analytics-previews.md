---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 637afab45e04c68777f8d1b42817c912cdc09941
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53133750"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="public-previews"></a>공개 미리 보기

다음 기능은 공개 미리 보기에 있습니다. 현재 이러한 기능을 활용할 수 있지만 프로덕션 환경에서는 사용하지 마세요.

### <a name="integration-with-azure-machine-learning"></a>Azure Machine Learning과 통합

ML(Machine Learning) 함수로 Stream Analytics 작업을 확장할 수 있습니다. Stream Analytics 작업에서 ML 함수를 사용하는 방법에 대한 자세한 내용은 [Azure Machine Learning 함수를 사용하여 Stream Analytics 작업의 크기 조정](stream-analytics-scale-with-machine-learning-functions.md)을 참조하세요. [Azure Stream Analytics 및 Azure Machine Learning을 사용한 감정 분석 수행](stream-analytics-machine-learning-integration-tutorial.md)을 통해 실제 시나리오를 확인하세요.

### <a name="session-windows"></a>세션 창

Stream Analytics에는 개발자가 최소한의 노력으로 복잡한 스트림 처리 작업을 작성할 수 있도록 창 함수에 대한 네이티브 지원이 있습니다. [세션 창](https://msdn.microsoft.com/azure/stream-analytics/reference/session-window-azure-stream-analytics)은 데이터가 없는 기간을 필터링하여 비슷한 시간에 도착하는 이벤트를 그룹화합니다. 창 함수에 대한 자세한 내용은 [Stream Analytics 창 함수 소개](stream-analytics-window-functions.md)를 참조하세요.

### <a name="blob-output-partitioning-by-custom-time"></a>사용자 지정 시간별 Blob 출력 분할

Azure Stream Analytics는 사용자 지정 시간 특성을 기반으로 Blob Storage로 출력할 수 있습니다. 자세한 내용은 [Azure Stream Analytics Blob Storage 출력에 대한 사용자 지정 날짜/시간 경로 패턴](stream-analytics-custom-path-patterns-blob-storage-output.md)을 참조하세요.

### <a name="javascript-user-defined-aggregate"></a>JavaScript 사용자 정의 집계

Azure Stream Analytics는 JavaScript로 작성된 UDA(사용자 정의 집계)를 지원하고 이를 통해 복잡한 상태 저장 비즈니스 논리를 구현할 수 있습니다. UDA를 사용하는 방법은 [Azure Stream Analytics JavaScript 사용자 정의 집계](stream-analytics-javascript-user-defined-aggregates.md) 설명서를 참조하세요. 

### <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.

### <a name="net-user-defined-functions-on-iot-edge"></a>IoT Edge의 .NET 사용자 정의 함수

.NET Standard 사용자 정의 함수를 사용하면 스트리밍 파이프라인의 일부로 .NET Standard 코드를 실행할 수 있습니다. 단순 C# 클래스를 작성하거나 전체 프로젝트 및 라이브러리를 가져올 수 있습니다. 전체 작성 및 디버깅 환경은 Visual Studio에서 지원됩니다. 자세한 내용은 [Azure Stream Analytics Edge 작업에 대한 .NET Standard 사용자 정의 함수 개발](stream-analytics-edge-csharp-udf-methods.md)을 참조하세요.

## <a name="private-previews"></a>비공개 미리 보기

다음 기능은 비공개 미리 보기에 있습니다. 이 미리 보기에 액세스하려면 Azure Stream Analytics 비공개 미리 보기 [로그인](https://aka.ms/ASApreview1) 페이지를 방문하세요.

### <a name="anomaly-detection"></a>이상 감지

Azure Stream Analytics에는 양방향, 느린 긍정 및 느린 부정 추세 검색 외에도 *spike* 및 *dips* 검색을 지원하는 새로운 기계 학습 모델이 도입되었습니다.

### <a name="c-custom-deserializer-for-azure-stream-analytics-on-iot-edge"></a>IoT Edge의 Azure Stream Analytics용 C# 사용자 지정 역직렬 변환기

개발자는 이제 Azure Stream Analytics에서 수신한 이벤트를 역직렬화하기 위해 C#에서 사용자 지정 역직렬 변환기를 구현할 수 있습니다. 역직렬화할 수 있는 형식의 예로는 Parquet, Protobuf, XML 또는 임의의 이진 형식이 있습니다.

### <a name="blob-output-partitioning-by-custom-attribute"></a>사용자 지정 특성으로 Blob 출력 분할

이제 쿼리의 열을 기반으로 Azure Stream Analytics 출력을 Blob Storage로 분할할 수 있습니다.

### <a name="managed-identities-for-azure-resources-authentication-to-azure-data-lake-storage"></a>Azure Data Lake Storage에 대한 Azure 리소스 인증을 위한 관리 ID

Azure Data Lake Storage Gen1에 쓰는 동안 Azure 리소스 기반 인증을 위해 관리 ID와 함께 실시간 파이프라인을 작동할 수 있게 하므로 프로그래밍 방식으로 작업을 만들 수 있습니다. 자세한 내용은 [Use Managed identities for Azure resources to Authenticate Azure Stream Analytics Jobs to Azure Data Lake Storage Gen1 Output](stream-analytics-managed-identities-adls.md)(Azure 리소스의 관리 ID를 사용하여 Azure Data Lake Storage Gen1 출력에 대한 Azure Stream Analytics 작업 인증)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Eight new features in Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/)(Azure Stream Analytics의 8가지 새 기능)

* [4 new features now available in Azure Stream Analytics](https://azure.microsoft.com/blog/4-new-features-now-available-in-azure-stream-analytics/)(Azure Stream Analytics에서 이제 사용 가능한 4가지 새 기능)
