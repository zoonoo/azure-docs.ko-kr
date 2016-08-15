<properties
   pageTitle="Azure Functions 크기 조정 방법 | Microsoft Azure"
   description="Azure Functions에서 이벤트 기반 워크로드의 수요를 충족하도록 크기를 조정하는 방식을 이해합니다."
   services="functions"
   documentationCenter="na"
   authors="dariagrigoriu"
   manager="erikre"
   editor=""
   tags=""
   keywords="Azure 함수, 함수, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처"/>

<tags
   ms.service="functions"
   ms.devlang="multiple"
   ms.topic="reference"
   ms.tgt_pltfrm="multiple"
   ms.workload="na"
   ms.date="08/03/2016"
   ms.author="dariagrigoriu"/>

# Azure Functions 크기 조정 방법

## 소개

Azure Functions의 장점은 필요할 때 계산 리소스에서만 사용한다는 점입니다. 즉, 유휴 상태의 VM에 대한 비용을 지불하거나 필요할 때를 대비해서 용량을 남겨 둘 필요가 없다는 점입니다. 그 대신, 플랫폼에서 코드가 실행될 경우 계산 역량을 할당하고 로드를 처리하는 데 필요하면 크기를 확장하며 코드가 실행되지 않을 경우 축소합니다.

이런 새로운 기능의 메커니즘은 동적 서비스 계획입니다.

이 문서는 동적 서비스 계획의 작동 원리 및 플랫폼이 코드를 실행하는 수요에 따라 크기를 조정하는 방식에 대한 개요를 제공합니다.

Azure Functions에 익숙하지 않다면 해당 기능을 더 잘 이해할 수 있도록 [Azure Functions 개요](functions-overview.md) 문서를 확인하도록 합니다.

## Azure Functions 구성

크기 조정과 관련된 두 가지 주요 설정이 있습니다.

* [Azure 앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 또는 동적 서비스 계획
* 실행 환경을 위한 메모리 크기

함수의 비용은 선택하는 서비스 계획에 따라 달라집니다. 동적 서비스 계획에서 비용은 실행 시간, 메모리 크기, 실행 수의 함수입니다. 요금은 코드가 실제로 실행 중인 경우에만 누적됩니다.

앱 서비스 계획은 기존 VM에 함수를 호스팅하며 다른 코드를 실행하는 데 사용될 수도 있습니다. 매달 이 VM에 대한 비용을 지불하면 이 곳에서 함수를 실행하는 데 대한 추가 요금이 발생하지 않습니다.

## 서비스 계획 선택

함수를 만들 경우 동적 서비스 계획에서 실행할지 또는 [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서 실행할지를 선택할 수 있습니다. 현재 기본, 표준 또는 프리미엄 SKU에서 웹앱이 작동하는 것처럼 앱 서비스 계획에서 사용자의 함수는 전용 VM에서 실행됩니다. 전용 VM은 사용자의 앱 및 함수에 할당되며 코드가 활발히 실행되고 있는지와 상관 없이 언제든 사용할 수 있습니다. 이미 다른 코드를 실행하고 있는 기존의 활용도가 낮은 VM이 있거나 함수를 계속해서 또는 거의 끊임없이 실행할 예정인 경우에 유용한 옵션입니다. VM은 런타임 및 메모리에서 비용을 분리합니다. 결과적으로 많은 장기 실행 함수의 비용을 해당 함수가 실행되는 하나 이상의 VM의 비용으로 제한할 수 있습니다.

[AZURE.INCLUDE [동적 서비스 계획](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0803_2016-->