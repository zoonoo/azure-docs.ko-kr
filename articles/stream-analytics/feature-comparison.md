---
title: Azure Stream Analytics 기능 비교
description: 이 문서에서는 Azure Stream Analytics 클라우드 및 Azure portal, Visual Studio 및 Visual Studio Code에 IoT Edge 작업에 대 한 지원 기능을 비교 합니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509782"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics 기능 비교

Azure Stream Analytics를 사용 하 여 만들 수 있습니다 스트리밍 솔루션을 사용 하 여 IoT Edge 및 클라우드에서 [Azure portal](stream-analytics-quick-create-portal.md)를 [Visual Studio](stream-analytics-quick-create-vs.md), 및 [Visual Studio Code](quick-create-vs-code.md)합니다. 이 문서의 표에 두 작업 유형에 대 한 각 플랫폼에서 지원 되는 기능입니다.

## <a name="cloud-job-features"></a>클라우드 작업 기능


|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|플랫폼 간     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|스크립트 작성     |예         |예         |예         |
|스크립트 Intellisense     |구문 강조 표시         |구문 강조 표시</br>코드 완성</br>오류 표식         |구문 강조 표시</br>코드 완성</br>오류 표식         |
|입력, 출력 및 작업 구성 정의     |예         |예         |예         |
|Blob 출력 분     |예         |예         |예         |
|출력으로 power BI     |예         |예         |아닙니다.         |
|SQL database 참조 데이터     |예         |예         |예         |
|사용자 지정 메시지 속성     |예         |아니오         |아닙니다.         |
|여러 쿼리 입력 및 출력 공유합니다     |아닙니다.         |예         |예         |
|JavaScript UDF와 UDA     |예         |예         |Windows만         |
|Machine Learning 설명선     |예. 하지만 쿼리를 테스트할 수 없습니다.        |예, 하지만 로컬로 테스트할 수 없습니다.         |아닙니다.         |
|호환성 수준     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|기본 제공 기계 학습 기반 변칙 검색 함수     |예         |예         |예         |
|기본 지리 공간적 함수     |예         |예         |예         |
|쿼리 샘플 파일을 사용 하 여 테스트     |예         |예         |예         |
|라이브 데이터는 로컬 테스트     |아닙니다.         |사용자 계정 컨트롤         |아닙니다.         |
|작업 엔터티를 살펴보고 작업 나열     |예         |예         |예         |
|로컬 프로젝트 작업을 내보내려면     |아닙니다.         |예         |예         |
|제출, 시작 및 중지 작업     |예         |예         |예         |
|소스 제어     |아닙니다.         |예         |예         |
|CI/CD 지원도     |부분         |예         |예         |
|작업 메트릭 보기 및 다이어그램     |예         |예         |포털에서 열기         |
|작업 런타임 오류 보기     |예         |예         |아닙니다.         |
|진단 로그     |예         |아니오         |아닙니다.         |


## <a name="iot-edge-job-features"></a>IoT Edge 작업 기능

|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|제작 작업     |예         |예         |아닙니다.         |
|소스 제어     |아닙니다.         |사용자 계정 컨트롤         |아닙니다.         |
|로컬 프로젝트 작업을 내보내려면     |아닙니다.         |사용자 계정 컨트롤         |아닙니다.         |
|쿼리 샘플 파일을 사용 하 여 테스트     |예         |예         |아닙니다.         |
|여러 쿼리 입력 및 출력 공유합니다     |아닙니다.         |사용자 계정 컨트롤         |아닙니다.         |
|C# UDF     |아닙니다.         |사용자 계정 컨트롤         |아닙니다.         |
|제출, 시작 및 중지 작업     |예         |예         |아닙니다.         |
|작업 엔터티를 살펴보고 작업 나열     |예         |예         |아닙니다.         |
|작업 메트릭 보기 및 다이어그램     |예         |부분         |아닙니다.         |
|작업 런타임 오류 보기     |예         |부분         |아닙니다.         |
|CI/CD 지원도     |아닙니다.         |아니요         |아닙니다.         |


## <a name="next-steps"></a>다음 단계

* [IoT Edge의 Azure Stream Analytics](stream-analytics-edge.md)
* [자습서: 작성 된 C# Azure Stream Analytics IoT Edge 작업 (미리 보기)에 대 한 사용자 정의 함수](stream-analytics-edge-csharp-udf.md)
* [Visual Studio 도구를 사용 하 여 Stream Analytics IoT Edge 작업 개발](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [Visual Studio Code (미리 보기)를 사용 하 여 Azure Stream Analytics 살펴보기](vscode-explore-jobs.md)


