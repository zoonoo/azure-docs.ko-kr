<properties
   pageTitle="Azure Functions 크기 조정 방법 | Microsoft Azure"
   description="Azure Functions에서 이벤트 기반 워크로드의 수요를 충족하도록 크기를 조정하는 방식을 이해합니다."
   services="functions"
   documentationCenter="na"
   authors="eduardolaureano"
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
   ms.date="03/09/2016"
   ms.author="edlaure"/>
  
# Azure Functions 크기 조정 방법
     
## 소개

Azure Functions의 장점 중 하나는 실행되는 코드에 필요할 때만 리소스가 사용된다는 것입니다. 즉, 유휴 상태의 VM에 대한 비용을 지불하거나 필요할 때를 대비해서 용량을 남겨 둘 필요가 없다는 점입니다. 그 대신, 플랫폼에서 코드가 실행될 때 계산 역량을 할당하고, 로드를 처리하는 데 필요하면 크기를 확장하고, 코드가 실행되지 않을 때는 다시 축소합니다.

이런 새로운 기능의 메커니즘은 동적 서비스 계획입니다. 새로운 서비스 계획은 코드에 대하여, 코드가 사용하는 메모리의 양과 기가바이트 초 단위로 측정되는 실행에 소요되는 시간에 대해서만 비용이 부과되는, 수요에 따라 확장되는 동적 컨테이너를 제공합니다.

이 문서는 동적 서비스 계획의 작동 원리 및 플랫폼이 코드를 실행하기 위해서 수요에 따라 크기를 조정하는 방식에 대한 개요를 제공합니다.

Azure Functions에 익숙하지 않다면, 해당 기능을 더 잘 이해할 수 있도록 [Azure Functions 개요](functions-overview.md)를 확인하는 것이 좋습니다.

## 함수 앱 구성

크기 조정과 관련된 두 가지 주요 설정이 있습니다.

* [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) 또는 동적 서비스 계획 
* 실행 환경을 위한 메모리 크기 

함수의 비용은 사용자가 선택하는 서비스 계획 유형에 따라 달라집니다. 동적 서비스 계획에서 비용은 실행 시간, 메모리 크기, 실행 수의 함수입니다. 실제로 코드를 실행할 때만 비용이 발생합니다.

일반 서비스 계획에서는 기존 VM에 다른 코드를 실행하는 데 사용될 수 있는 함수를 호스트할 수 있습니다. 매달 이 VM에 대한 비용을 지불하면 이 곳에서 함수를 실행하는 데 대한 추가 요금이 발생하지 않습니다.

## 서비스 계획 선택

함수 앱을 만들 때, 동적 서비스 계획(신규!)에서 실행하지 일반 [앱 서비스 계획](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)에서 실행할지를 선택할 수 있습니다. 앱 서비스 계획 내에서, 사용자의 함수는 현재(기본, 표준 또는 프리미엄 SKU에서) 웹앱이 작동하는 것처럼, 전용 VM에서 실행됩니다. 전용 VM은 사용자의 앱 및/또는 함수에 할당되며, 활발히 실행되고 있는 코드와 상관없이 사용할 수 있습니다. 이 옵션은 기존 VM에서 이미 다른 코드를 실행하고 있지만 완전히 활용되고 있지 못한 경우 또는 함수를 계속해서 또는 거의 끊임없이 실행할 예정인 경우에 유용합니다. 하나의 VM을 사용하면, 오래 실행되는 많은 수의 함수에 대한 비용을 함수가 실행되는 하나 이상의 VM에 대한 비용으로 제한할 수 있기 때문에, 실행 시간 및 메모리 크기로부터 발생하는 비용을 완화시킬 수 있습니다.

[AZURE.INCLUDE [동적 서비스 계획](../../includes/functions-dynamic-service-plan.md)]

<!---HONumber=AcomDC_0518_2016-->