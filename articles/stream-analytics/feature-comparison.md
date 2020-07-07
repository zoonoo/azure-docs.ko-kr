---
title: Azure Stream Analytics 기능 비교
description: 이 문서에서는 Azure Portal, Visual Studio 및 Visual Studio Code에서 Azure Stream Analytics 클라우드 및 IoT Edge 작업에 대해 지원 되는 기능을 비교 합니다.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: b169a067f05de422b714e18a4d7ebb9c9e7f0428
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82133493"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics 기능 비교

Azure Stream Analytics를 사용 하 여 클라우드 및 IoT Edge [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md)및 [Visual Studio Code](quick-create-vs-code.md)를 사용 하 여 스트리밍 솔루션을 만들 수 있습니다. 이 문서의 표에서는 두 작업 유형에 대해 각 플랫폼에서 지원 되는 기능을 보여 줍니다.

> [!NOTE]
> Visual Studio 및 Visual Studio Code 도구는 중국 동부, 중국 북부, 독일 중부 및 독일 북동부 지역의 작업을 지원하지 않습니다.

## <a name="cloud-job-features"></a>클라우드 작업 기능


|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|플랫폼 간     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|스크립트 작성     |예         |예         |예         |
|Intellisense 스크립팅     |구문 강조         |구문 강조</br>코드 완성</br>오류 표식         |구문 강조</br>코드 완성</br>오류 표식         |
|모든 종류의 입력, 출력 및 작업 구성 정의     |예         |예         |예         |
|원본 제어     |예         |예         |예         |
|CI/CD 지원     |Partial         |예         |예         |
|여러 쿼리에서 입력 및 출력 공유     |예         |예         |예         |
|샘플 파일을 사용 하 여 쿼리 테스트     |예         |예        |예         |
|라이브 데이터 로컬 테스트     |예         |예       |예      |
|작업 나열 및 작업 엔터티 보기     |예         |예        |예         |
|로컬 프로젝트로 작업 내보내기     |예         |예         |예         |
|작업 제출, 시작 및 중지     |예         |예         |예         |
|작업 메트릭 및 다이어그램 보기     |예         |예         |포털에서 열기         |
|작업 런타임 오류 보기     |예         |예         |아니요         |
|리소스 로그     |예         |아니요         |아니요         |
|사용자 지정 메시지 속성     |예         |예         |아니요       |
|C # 사용자 지정 코드 함수 및 역직렬 변환기|읽기 전용 모드|예|아니요|
|JavaScript UDF 및 UDA     |예         |예         |Windows만 해당         |
|Machine Learning Service     |예        |예         |아니요         |
|Machine Learning Studio     |예. 하지만 쿼리를 테스트할 수 없습니다.        |예 |아니요         |
|호환성 수준     |1.0</br>1.1</br>1.2 (기본값)         |1.0</br>1.1</br>1.2 (기본값)           |1.0</br>1.1</br>1.2 (기본값)           |
|기본 제공 ML 기반 변칙 검색 함수     |예         |예         |예         |
|기본 제공 지리 공간적 함수     |예         |예         |예         |



## <a name="iot-edge-job-features"></a>IoT Edge 작업 기능

|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|작업 작성     |예         |예         |아니요         |
|원본 제어     |아니요         |예         |아니요         |
|로컬 프로젝트로 작업 내보내기     |아니요         |예         |아니요         |
|샘플 파일을 사용 하 여 쿼리 테스트     |예         |예         |아니요         |
|여러 쿼리에서 입력 및 출력 공유     |아니요         |예         |아니요         |
|C# UDF     |아니요         |예         |아니요         |
|작업 제출     |예         |예         |아니요         |
|작업 나열 및 작업 엔터티 보기     |예         |예         |아니요         |
|작업 메트릭 및 다이어그램 보기     |예         |부분         |아니요         |
|작업 런타임 오류 보기     |예         |부분         |아니요         |
|CI/CD 지원     |아니요         |아니요         |아니요         |


## <a name="next-steps"></a>다음 단계

* [IoT Edge의 Azure Stream Analytics](stream-analytics-edge.md)
* [자습서: Azure Stream Analytics IoT Edge 작업 (미리 보기)에 대 한 c # 사용자 정의 함수 작성](stream-analytics-edge-csharp-udf.md)
* [Visual Studio 도구를 사용 하 여 Stream Analytics IoT Edge 작업 개발](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [Visual Studio Code를 사용 하 여 Azure Stream Analytics 탐색 (미리 보기)](visual-studio-code-explore-jobs.md)


