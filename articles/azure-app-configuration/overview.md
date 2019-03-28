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
ms.openlocfilehash: 8c2c31b4d87ac2db123c0cae63679e3773734b4f
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226286"
---
# <a name="what-is-azure-app-configuration"></a>Azure App Configuration이란?

Azure App Configuration은 애플리케이션 설정을 중앙 집중식으로 관리하는 서비스를 제공합니다. 일반적으로 최신 프로그램, 특히 클라우드에서 실행되는 프로그램에는 사실상 많은 구성 요소가 분산되어 있습니다. 이러한 구성 요소에 구성 설정이 분산되어 있으면 애플리케이션을 배포하는 동안 오류를 쉽게 해결할 수 없습니다. App Configuration을 사용하여 애플리케이션에 대한 모든 설정을 한 곳에서 저장하고 액세스를 보호합니다.

App Configuration은 미리 보기 기간 동안 체험 서비스로 사용할 수 있습니다. 사용하려는 경우 미리 보기를 [등록](https://aka.ms/azconfig/register)합니다.

## <a name="why-use-app-configuration"></a>App Configuration을 사용하는 이유

클라우드 기반 애플리케이션은 여러 지역의 여러 가상 머신이나 컨테이너에서 실행되며 다수의 외부 서비스를 사용합니다. 강력하고 확장성 있는 분산 애플리케이션을 만드는 작업은 매우 어렵습니다. 

개발자는 다양한 프로그래밍 방법을 통해 점점 더 복잡해지는 애플리케이션 구축을 처리할 수 있습니다. 예를 들어 12개 요소 앱에서는 충분한 테스트를 거친 다양한 아키텍처 패턴과 클라우드 애플리케이션 사용에 대한 모범 사례를 자세히 설명하고 있습니다. 이 가이드의 주요 권장 사항은 구성을 코드에서 분리하는 것입니다. 이 경우 애플리케이션의 구성 설정은 실행 파일 외부에 유지하고 런타임 환경이나 외부 원본에서 읽어야 합니다.

모든 애플리케이션에서 App Configuration을 사용할 수 있지만, 애플리케이션 사용의 이점을 누릴 수 있는 애플리케이션 유형의 예는 다음과 같습니다.

* 하나 이상의 지역에 배포된 Azure Kubernetes Service, Azure Service Fabric 또는 컨테이너화된 다른 앱을 기반으로 하는 마이크로서비스
* Azure Functions 또는 다른 이벤트 구동 상태 비저장 컴퓨팅 앱을 포함한 서버리스 앱
* 지속적인 배포 파이프라인

App Configuration을 사용하면 다음과 같은 이점이 있습니다.

* 몇 분 안에 설정할 수 있는 완전 관리형 서비스
* 유연한 키 표현 및 매핑
* 레이블로 태그 지정
* 특정 시점에 설정 재생
* 사용자 지정 차원에서 두 구성 집합 비교
* Azure 관리 ID를 통해 보안 향상
* 저장 시 또는 전송 중 완벽한 데이터 암호화
* 인기 있는 프레임워크와 네이티브 통합

App Configuration은 애플리케이션 비밀을 저장하는 데 사용되는 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)를 보완합니다. App Configuration을 사용하여 더 쉽게 구현할 수 있는 시나리오는 다음과 같습니다.

* 서로 다른 환경 및 지역에 대한 계층 구조 구성 데이터를 중앙 집중식으로 관리하고 배포합니다.
* 애플리케이션을 다시 배포하거나 다시 시작할 필요 없이 동적 구성을 변경합니다.
* 기능 관리

## <a name="use-app-configuration"></a>App Configuration 사용

애플리케이션에 App Configuration 저장소를 추가하는 가장 쉬운 방법은 Microsoft가 제공하는 클라이언트 라이브러리를 활용하는 것입니다. 프로그래밍 언어와 프레임워크에 따라 사용할 수 있는 최상의 방법은 다음과 같습니다.

| 프로그래밍 언어 및 프레임워크 | 연결 방법 |
|---|---|
| .NET Core 및 ASP.NET Core | .NET Core용 App Configuration 공급자 |
| .NET 및 ASP.NET | .NET용 App Configuration 작성기 |
| Java Spring | Spring Cloud용 App Configuration 클라이언트 |
| 기타 | App Configuration REST API |

## <a name="next-steps"></a>다음 단계

* [빠른 시작: ASP.NET 웹앱 만들기](quickstart-aspnet-core-app.md) 
