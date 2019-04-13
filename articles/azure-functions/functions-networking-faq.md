---
title: Azure Functions의 네트워킹에 대 한 질문과 대답
description: 몇 가지 가장 일반적인 질문 및 Azure Functions를 사용 하 여 네트워킹에 대 한 시나리오에 대 한 답변입니다.
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: troubleshooting
ms.date: 4/11/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 126b9ccefedee1f5cefdac8a8666a58e7a4a1fef
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59548646"
---
# <a name="frequently-asked-questions-about-networking-in-azure-functions"></a>Azure Functions의 네트워킹에 대 한 질문과 대답

다음은 네트워킹 자주 묻는 질문의 목록입니다. 보다 포괄적인 개요를 알아보려면는 [네트워킹 옵션 문서 함수](functions-networking-options.md)

## <a name="how-do-i-set-a-static-ip-in-functions"></a>함수에서 고정 IP를 설정 하는 방법

함수에 대 한 인바운드 및 아웃 바운드 IP가 정적 있어야 하는 유일한 방법은 중인 함수 앱 서비스 환경 (ASE)에서 배포 합니다. ASE를 사용 하 여 자세한 내용은 문서를 사용 하 여 시작 합니다. [만들기 및 ILB ASE를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.

## <a name="how-do-i-restrict-internet-access-to-my-function"></a>내 함수에 인터넷 액세스를 어떻게 제한할 수 있습니까?

다양 한 방법으로, 아래에서 인터넷 액세스를 제한할 수 있습니다.

* [IP 제한을](../app-service/app-service-ip-restrictions.md): IP 범위에서 함수 앱에 인바운드 트래픽을 제한 합니다.
* 모든 HTTP 트리거를 제거 합니다. 일부 응용 프로그램에 대 한 것으로 충분 하 HTTP 트리거를 방지 하 고 다른 이벤트 소스를 사용 하 여 함수를 트리거합니다.

이렇게 하면 가장 중요 한 고려를 염두에 Azure portal 편집기 사용 하기 위해 실행 중인 함수에 직접 액세스 해야 하는 방법은입니다. Azure portal 통해 코드 변경에는 해당 IP 허용 목록에 추가 하도록 포털을 찾아볼 하는 데 사용할 장치를 해야 합니다. 계속 사용할 수 있습니다, 있지만 플랫폼 기능 탭 아래에 있는 모든 네트워크 제한을 합니다.

## <a name="how-do-i-restrict-my-function-app-to-a-vnet"></a>VNET에 함수 앱 내 제한 하는 어떻게 하나요?

모든 트래픽을 VNET 통과 되도록 함수를 전체적으로 제한 하는 유일한 방법은 사용 하는 것을 내부적으로 부하 분산 (ILB) 앱 서비스 환경 (ASE). 이 옵션은 VNET 내에서 전용된 인프라에서 사이트를 배포 하 고 모든 트리거 및 VNET 통한 트래픽을 보냅니다. ASE를 사용 하 여 자세한 내용은 문서를 사용 하 여 시작 합니다. [만들기 및 ILB ASE를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.

## <a name="how-can-i-access-resources-in-a-vnet-from-a-function-app"></a>함수 앱에서 VNET의 리소스를 액세스 어떻게 하나요?

VNET 통합을 사용 하 여 실행 중인 함수에서 VNET의 리소스에 액세스할 수 있습니다. 자세한 내용은 참조 하세요. [VNET 통합](functions-networking-options.md#vnet-integration)

## <a name="how-do-i-access-resources-protected-by-service-endpoints"></a>서비스 끝점에 의해 보호 되는 리소스에 액세스 하려면 어떻게 해야 합니까?

새 VNET 통합을 사용 하 여 (현재 미리 보기로 제공)를 실행 중인 함수에서 리소스를 보호 하는 서비스 끝점에 액세스할 수 있습니다. 자세한 내용은 [VNET 통합 미리 보기](functions-networking-options.md#preview-vnet-integration)합니다.

## <a name="how-can-i-trigger-a-function-from-a-resource-in-a-vnet"></a>VNET에서 리소스의 함수를 트리거할 수는 방법

만 함수 앱 App Service Environment에 배포 하 여 VNET에서 리소스의 함수를 트리거할 수 있습니다. ASE를 사용 하 여 세부 정보를 참조 하세요 [만들기 및 ILB ASE를 사용 하 여](../app-service/environment/create-ilb-ase.md)입니다.


## <a name="how-can-i-deploy-my-function-app-in-a-vnet"></a>VNET에서 함수 앱을 배포 하려면 어떻게 해야 하나요?

전체적으로 ILB ASE를 사용 하 여 VNET에 세부 정보 내에 있는 함수 앱 만들기, 문서를 시작 하는 유일한 방법은 App Service Environment에 배포 합니다. [만들기 및 ILB ASE를 사용 하 여](https://docs.microsoft.com/azure/app-service/environment/create-ilb-ase)입니다.

만 해야 하는 포괄적인 네트워크 격리 이하의 VNET 리소스에 대 한 단방향 액세스 시나리오에 대 한 참조를 [Functions 네트워킹 개요](functions-networking-options.md)합니다.

## <a name="next-steps"></a>다음 단계

네트워킹에 자세히 알아보려면 및 함수: 

* [시작 VNET 통합 자습서를 수행 합니다.](./functions-create-vnet.md)
* [여기에서 함수에서 네트워킹 옵션에 자세히 알아보기](./functions-networking-options.md)
* [App Service와 VNET 통합/함수에 대해 여기서 자세히 알아보기](../app-service/web-sites-integrate-with-vnet.md)
* [Azure의 VNET에 대해 자세히 알아보기](../virtual-network/virtual-networks-overview.md)
* [추가 네트워킹 기능 및 App Service Environment를 사용 하 여 컨트롤을 사용 하도록 설정](../app-service/environment/intro.md)
