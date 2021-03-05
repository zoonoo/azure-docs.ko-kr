---
title: Azure Functions 시작
description: Azure Functions를 사용하기 위한 첫 번째 단계를 수행합니다.
author: craigshoemaker
ms.topic: overview
ms.date: 11/19/2020
ms.author: cshoe
zone_pivot_groups: programming-languages-set-functions-lang-workers
ms.openlocfilehash: 77d370b895c777278d3136c7d2c511e7f9e23b36
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179258"
---
# <a name="getting-started-with-azure-functions"></a>Azure Functions 시작

## <a name="introduction"></a>소개

[Azure Functions](./functions-overview.md)를 사용하면 시스템의 논리를 쉽게 사용할 수 있는 코드 블록으로 구현할 수 있습니다. 이러한 코드 블록을 "함수"라고 합니다.

시작하려면 다음 리소스를 사용합니다.

::: zone pivot="programming-language-csharp"

| 작업 | 리소스 |
| --- | --- |
| **첫 번째 기능 만들기** | 다음 도구 중 하나를 사용합니다.<br><br><li>[Visual Studio](./functions-create-your-first-function-visual-studio.md)<li>[Visual Studio Code](./create-first-function-vs-code-csharp.md)<li>[명령줄](./create-first-function-cli-csharp.md) |
| **실행 중인 함수 보기** | <li>[Azure 샘플 브라우저](/samples/browse/?expanded=azure&languages=csharp&products=azure-functions)<li>[Azure 커뮤니티 라이브러리](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=C%23) |
| **대화형 자습서 살펴보기**| <li>[Azure 서버리스 기술 검토 및 비즈니스 시나리오에 적합한 서비스 선택](/learn/modules/serverless-fundamentals/)<li>[잘 설계된 프레임워크 - 성능 효율성](/learn/modules/azure-well-architected-performance-efficiency/)<li>[트리거를 사용하여 Azure 함수 실행](/learn/modules/execute-azure-function-with-triggers/) <br><br>[대화형 자습서의 전체 목록](/learn/browse/?expanded=azure&products=azure-functions)은 Microsoft Learn을 참조하세요.|
| **모범 사례 검토** |<li>[성능 및 안정성](./functions-best-practices.md)<li>[연결 관리](./manage-connections.md)<li>[오류 처리 및 함수 재시도](./functions-bindings-error-pages.md?tabs=csharp)<li>[보안](./security-concepts.md)|
| **자세히 알아보기** | <li>함수가 수요와 일치하도록 인스턴스를 [자동으로 늘리거나 줄이는](./functions-scale.md) 방법 알아보기<li>사용 가능한 다양한 [배포 방법](./functions-deployment-technologies.md) 탐색<li>기본 제공 [모니터링 도구](./functions-monitoring.md)를 사용하여 함수 분석 지원<li>[C# 언어 참조](./functions-dotnet-class-library.md) 읽기|

::: zone-end

::: zone pivot="programming-language-java"
| 작업 | 리소스 |
| --- | --- |
| **첫 번째 기능 만들기** | 다음 도구 중 하나를 사용합니다.<br><br><li>[Visual Studio Code](./create-first-function-vs-code-java.md)<li>[터미널/명령 프롬프트를 사용하는 Java/Maven 함수](./create-first-function-cli-java.md)<li>[Gradle](./functions-create-first-java-gradle.md)<li>[Eclipse](./functions-create-maven-eclipse.md)<li>[IntelliJ IDEA](./functions-create-maven-intellij.md) |
| **실행 중인 함수 보기** | <li>[Azure 샘플 브라우저](/samples/browse/?expanded=azure&languages=java&products=azure-functions)<li>[Azure 커뮤니티 라이브러리](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Java) |
| **대화형 자습서 살펴보기**| <li>[Azure 서버리스 기술 검토 및 비즈니스 시나리오에 적합한 서비스 선택](/learn/modules/serverless-fundamentals/)<li>[잘 설계된 프레임워크 - 성능 효율성](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions용 Maven 플러그인을 사용하여 앱 개발](/learn/modules/develop-azure-functions-app-with-maven-plugin/) <br><br>[대화형 자습서의 전체 목록](/learn/browse/?expanded=azure&products=azure-functions)은 Microsoft Learn을 참조하세요.|
| **모범 사례 검토** |<li>[성능 및 안정성](./functions-best-practices.md)<li>[연결 관리](./manage-connections.md)<li>[오류 처리 및 함수 재시도](./functions-bindings-error-pages.md?tabs=java)<li>[보안](./security-concepts.md)|
| **자세히 알아보기** | <li>함수가 수요와 일치하도록 인스턴스를 [자동으로 늘리거나 줄이는](./functions-scale.md) 방법 알아보기<li>사용 가능한 다양한 [배포 방법](./functions-deployment-technologies.md) 탐색<li>기본 제공 [모니터링 도구](./functions-monitoring.md)를 사용하여 함수 분석 지원<li>[Java 언어 참조](./functions-reference-java.md) 읽기|
::: zone-end

::: zone pivot="programming-language-javascript"
| 작업 | 리소스 |
| --- | --- |
| **첫 번째 기능 만들기** | 다음 도구 중 하나를 사용합니다.<br><br><li>[Visual Studio Code](./create-first-function-vs-code-node.md)<li>[Node.js 터미널/명령 프롬프트](./create-first-function-cli-node.md) |
| **실행 중인 함수 보기** | <li>[Azure 샘플 브라우저](/samples/browse/?expanded=azure&languages=javascript%2ctypescript&products=azure-functions)<li>[Azure 커뮤니티 라이브러리](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=JavaScript%2CTypeScript) |
| **대화형 자습서 살펴보기** | <li>[Azure 서버리스 기술 검토 및 비즈니스 시나리오에 적합한 서비스 선택](/learn/modules/serverless-fundamentals/)<li>[잘 설계된 프레임워크 - 성능 효율성](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions를 사용하여 서버리스 API 빌드](/learn/modules/build-api-azure-functions/)<li>[Azure Functions를 사용하여 서버리스 논리 만들기](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[Azure Functions를 사용하여 Node.js 및 Express API를 서버리스 API로 리팩터링](/learn/modules/shift-nodejs-express-apis-serverless/) <br><br>[대화형 자습서의 전체 목록](/learn/browse/?expanded=azure&products=azure-functions)은 Microsoft Learn을 참조하세요.|
| **모범 사례 검토** |<li>[성능 및 안정성](./functions-best-practices.md)<li>[연결 관리](./manage-connections.md)<li>[오류 처리 및 함수 재시도](./functions-bindings-error-pages.md?tabs=javascript)<li>[보안](./security-concepts.md)|
| **자세히 알아보기** | <li>함수가 수요와 일치하도록 인스턴스를 [자동으로 늘리거나 줄이는](./functions-scale.md) 방법 알아보기<li>사용 가능한 다양한 [배포 방법](./functions-deployment-technologies.md) 탐색<li>기본 제공 [모니터링 도구](./functions-monitoring.md)를 사용하여 함수 분석 지원<li>[JavaScript](./functions-reference-node.md) 또는 [TypeScript](./functions-reference-node.md#typescript) 언어 참조 읽기|
::: zone-end

::: zone pivot="programming-language-powershell"
| 작업 | 리소스 |
| --- | --- |
| **첫 번째 기능 만들기** | <li>[Visual Studio Code](./create-first-function-vs-code-powershell.md) 사용 |
| **실행 중인 함수 보기** | <li>[Azure 샘플 브라우저](/samples/browse/?expanded=azure&languages=powershell&products=azure-functions)<li>[Azure 커뮤니티 라이브러리](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=PowerShell) |
| **대화형 자습서 살펴보기** | <li>[Azure 서버리스 기술 검토 및 비즈니스 시나리오에 적합한 서비스 선택](/learn/modules/serverless-fundamentals/)<li>[잘 설계된 프레임워크 - 성능 효율성](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions를 사용하여 서버리스 API 빌드](/learn/modules/build-api-azure-functions/)<li>[Azure Functions를 사용하여 서버리스 논리 만들기](/learn/modules/create-serverless-logic-with-azure-functions/)<li>[트리거를 사용하여 Azure 함수 실행](/learn/modules/execute-azure-function-with-triggers/) <br><br>[대화형 자습서의 전체 목록](/learn/browse/?expanded=azure&products=azure-functions)은 Microsoft Learn을 참조하세요.|
| **모범 사례 검토** |<li>[성능 및 안정성](./functions-best-practices.md)<li>[연결 관리](./manage-connections.md)<li>[오류 처리 및 함수 재시도](./functions-bindings-error-pages.md?tabs=powershell)<li>[보안](./security-concepts.md)|
| **자세히 알아보기** | <li>함수가 수요와 일치하도록 인스턴스를 [자동으로 늘리거나 줄이는](./functions-scale.md) 방법 알아보기<li>사용 가능한 다양한 [배포 방법](./functions-deployment-technologies.md) 탐색<li>기본 제공 [모니터링 도구](./functions-monitoring.md)를 사용하여 함수 분석 지원<li>[PowerShell 언어 참조](./functions-reference-powershell.md) 읽기)|
::: zone-end

::: zone pivot="programming-language-python"
| 작업 | 리소스 |
| --- | --- |
| **첫 번째 기능 만들기** | 다음 도구 중 하나를 사용합니다.<br><br><li>[Visual Studio Code](./create-first-function-vs-code-csharp.md?pivots=programming-language-python)<li>[터미널/명령 프롬프트](./create-first-function-cli-csharp.md?pivots=programming-language-python) |
| **실행 중인 함수 보기** | <li>[Azure 샘플 브라우저](/samples/browse/?expanded=azure&languages=python&products=azure-functions)<li>[Azure 커뮤니티 라이브러리](https://www.serverlesslibrary.net/?technology=Functions%202.x&language=Python) |
| **대화형 자습서 살펴보기** | <li>[Azure 서버리스 기술 검토 및 비즈니스 시나리오에 적합한 서비스 선택](/learn/modules/serverless-fundamentals/)<li>[잘 설계된 프레임워크 - 성능 효율성](/learn/modules/azure-well-architected-performance-efficiency/)<li>[Azure Functions를 사용하여 서버리스 API 빌드](/learn/modules/build-api-azure-functions/)<li>[Azure Functions를 사용하여 서버리스 논리 만들기](/learn/modules/create-serverless-logic-with-azure-functions/) <br><br>[대화형 자습서의 전체 목록](/learn/browse/?expanded=azure&products=azure-functions)은 Microsoft Learn을 참조하세요.|
| **모범 사례 검토** |<li>[성능 및 안정성](./functions-best-practices.md)<li>[연결 관리](./manage-connections.md)<li>[오류 처리 및 함수 재시도](./functions-bindings-error-pages.md?tabs=python)<li>[보안](./security-concepts.md)<li>[처리량 성능 향상](./python-scale-performance-reference.md)|
| **자세히 알아보기** | <li>함수가 수요와 일치하도록 인스턴스를 [자동으로 늘리거나 줄이는](./functions-scale.md) 방법 알아보기<li>사용 가능한 다양한 [배포 방법](./functions-deployment-technologies.md) 탐색<li>기본 제공 [모니터링 도구](./functions-monitoring.md)를 사용하여 함수 분석 지원<li>[Python 언어 참조](./functions-reference-python.md) 읽기|
::: zone-end

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 애플리케이션의 구조에 대해 알아보기](./functions-reference.md)
