---
title: Azure App Configuration이란? | Microsoft Docs
description: Azure App Configuration 서비스에 대한 개요입니다.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: 11dd91039bb352e86800982d0a294f82622a56fe
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885022"
---
# <a name="what-is-azure-app-configuration"></a>Azure App Configuration이란?

Azure App Configuration은 중앙 집중식으로 애플리케이션 설정을 관리하는 서비스를 제공합니다. 최신 프로그램, 특히 클라우드에서 실행되는 프로그램은 일반적으로 많은 구성 요소가 사실상 분산되어 있습니다. 이러한 구성 요소에 구성 설정이 분산되어 있으면 애플리케이션을 배포하는 동안 오류를 쉽게 해결할 수 없습니다. App Configuration을 사용하면 한 곳에서 애플리케이션에 대한 모든 설정을 저장하고 액세스를 보호할 수 있습니다.

## <a name="why-use-app-configuration"></a>App Configuration을 사용하는 이유

클라우드 기반 애플리케이션은 여러 지역의 여러 가상 머신이나 컨테이너에서 실행되며 다수의 외부 서비스를 사용합니다. 강력하고 확장성이 뛰어난 분산 애플리케이션을 만드는 작업은 매우 어렵습니다. 증가하는 애플리케이션 구축의 복잡성을 처리하는 개발자를 돕기 위해 다양한 프로그래밍 방법론이 등장하고 있습니다. 예를 들어 12-요소 앱에는 충분한 테스트를 거친 다수의 아키텍처 패턴과 클라우드 애플리케이션 사용에 대한 모범 사례가 자세히 설명되어 있습니다. 이 가이드의 주요 권장 사항은 구성을 코드에서 분리하는 것입니다. 다시 말해, 설정과 같은 애플리케이션의 구성은 실행 파일 외부에 유지하고 런타임 환경이나 외부 소스에서 읽어 들여야 합니다.

모든 애플리케이션에 이러한 방법을 활용할 수 있지만, 다음은 App Configuration을 사용해야 하는 애플리케이션 유형의 좋은 예입니다.

* 한 곳 이상의 지역에 배포된 AKS, Service Fabric 또는 기타 컨테이너화된 앱을 기반으로 하는 마이크로 서비스
* Azure Functions 또는 기타 이벤트 구동 상태 비저장 컴퓨팅 앱을 비롯한 서버리스 앱
* 지속적인 배포 파이프라인

App Configuration을 사용하면 다음과 같은 이점이 있습니다.

* 몇 분 안에 설정할 수 있는 완전 관리형 서비스
* 유연한 키 표현 및 매핑
* 레이블을 사용한 태그 지정
* 지정 시간에 설정 재생
* 사용자 지정 방식으로 정의된 차원에 대한 두 가지 구성 집합 비교
* Azure 관리형 ID를 통해 향상된 보안
* 미사용 시 또는 전송 중 완벽한 데이터 암호화
* 인기있는 프레임워크와 네이티브 통합

## <a name="how-to-use-app-configuration"></a>App Configuration을 사용하는 방법

애플리케이션에 App Configuration 저장소를 추가하는 가장 쉬운 방법은 Microsoft가 제공하는 클라이언트 라이브러리를 활용하는 것입니다. 프로그래밍 언어 및 프레임워크에 따라 다음과 같은 유용한 방법을 사용할 수 있습니다.

| 프로그래밍 언어 및 프레임워크 | 연결 방법 |
|---|---|
| **.NET Core** 및 **ASP.NET Core** | .NET Core에 대한 App Configuration 구성 공급자 |
| **.NET** 및 **ASP.NET** | .NET에 대한 App Configuration 구성 작성기 |
| **Java Spring** | Spring Cloud에 대한 App Configuration 구성 클라이언트 |
| 기타 | App Configuration REST API |

## <a name="next-steps"></a>다음 단계

* [빠른 시작: ASP.NET 웹앱 만들기](quickstart-aspnet-core-app.md)  
