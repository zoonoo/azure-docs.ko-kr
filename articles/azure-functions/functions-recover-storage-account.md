---
title: Azure Functions 런타임에 연결할 수 없는 문제를 해결하는 방법을 설명합니다.
description: 잘못된 스토리지 계정 문제를 해결하는 방법에 대해 알아봅니다.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 910b582cb40b9f8aff6a553621b4677d6b019826
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963889"
---
# <a name="how-to-troubleshoot-functions-runtime-is-unreachable"></a>"Functions 런타임에 연결할 수 없음" 문제를 해결하는 방법

이 문서는 Azure Portal에 표시 될 때 "함수 런타임에 접근할 수 없습니다" 오류 메시지가 표시 되는 문제를 해결 하기 위한 것입니다. 이 오류가 발생 하면 포털에 다음과 같은 오류 문자열이 표시 됩니다.

`Error: Azure Functions Runtime is unreachable. Click here for details on storage configuration`

이는 Azure Functions 런타임를 시작할 수 없을 때 발생 합니다. 이 오류가 발생하는 가장 일반적인 원인은 함수 앱이 스토리지 계정에 액세스할 수 없기 때문입니다. 자세히 알아보려면 [Storage 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조 하세요.

이 문서의 나머지 부분에서는 각 사례를 식별 하 고 해결 하는 방법을 비롯 하 여이 오류의 다음 원인을 해결 하는 데 도움이 됩니다.

+ [스토리지 계정이 삭제됨](#storage-account-deleted)
+ [스토리지 계정 애플리케이션 설정이 삭제됨](#storage-account-application-settings-deleted)
+ [스토리지 계정 자격 증명이 잘못됨](#storage-account-credentials-invalid)
+ [스토리지 계정에 액세스할 수 없음](#storage-account-inaccessible)
+ [일일 실행 할당량 초과](#daily-execution-quota-full)
+ [앱이 방화벽 뒤에 있습니다.](#app-is-behind-a-firewall)


## <a name="storage-account-deleted"></a>스토리지 계정이 삭제됨

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수가 작동하지 않습니다.

### <a name="how-to-find-your-storage-account"></a>스토리지 계정을 찾는 방법

먼저 애플리케이션 설정에서 스토리지 계정 이름을 조회합니다. `AzureWebJobsStorage` 또는 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 중 하나에 스토리지 계정 이름이 연결 문자열에 래핑된 상태로 포함되어 있습니다. [응용 프로그램 설정 참조에 대](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)한 자세한 내용은 여기를 참조 하세요.

Azure Portal에서 스토리지 계정을 검색하여 계정이 아직 있는지 확인합니다. 계정이 삭제된 경우에는 스토리지 계정을 다시 만들고 스토리지 연결 문자열을 바꿔야 합니다. 그러면 함수 코드가 손실되므로 다시 배포해야 합니다.

## <a name="storage-account-application-settings-deleted"></a>스토리지 계정 애플리케이션 설정이 삭제됨

이전 단계에서 저장소 계정 연결 문자열이 없는 경우 삭제 되거나 덮어쓴 것일 수 있습니다. 일반적으로는 배포 슬롯 또는 Azure Resource Manager 스크립트를 사용하여 애플리케이션 설정을 지정할 때 앱 설정을 삭제합니다.

### <a name="required-application-settings"></a>필수 애플리케이션 설정

* 필수
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 사용 계획 함수의 경우 필수
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

[여기에서 이러한 응용 프로그램 설정에 대해 읽어](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)보세요.

### <a name="guidance"></a>지침

* 이러한 설정에 대해 "슬롯 설정"을 선택 하지 마세요. 배포 슬롯을 교환 하면 함수 앱이 중단 됩니다.
* 이러한 설정은 자동 배포의 일부로 수정 하지 마십시오.
* 이러한 설정은 생성 시에 제공되어야 하며 유효한 상태여야 합니다. 이러한 설정을 포함 하지 않는 자동화 된 배포는 나중에 설정이 추가 된 경우에도 실행 되지 않는 함수 앱을 생성 합니다.

## <a name="storage-account-credentials-invalid"></a>스토리지 계정 자격 증명이 잘못됨

스토리지 키를 다시 생성하는 경우에는 위의 스토리지 계정 연결 문자열을 업데이트해야 합니다. [저장소 키 관리에 대 한 자세한 내용은 여기를 참조](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)하세요.

## <a name="storage-account-inaccessible"></a>스토리지 계정에 액세스할 수 없음

함수 앱은 저장소 계정에 액세스할 수 있어야 합니다. Functions의 스토리지 계정 액세스를 차단하는 일반적인 문제는 다음과 같습니다.

+ 저장소 계정에 대 한 트래픽을 허용 하는 올바른 네트워크 규칙 없이 ASE (App Service 환경)에 배포 된 함수 앱입니다.

+ 저장소 계정 방화벽이 사용 하도록 설정 되어 있고 기능에서 들어오고 나가는 트래픽을 허용 하도록 구성 되어 있지 않습니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md)을 참조하세요.

## <a name="daily-execution-quota-full"></a>매일 실행 할당량이 꽉 참

매일 실행 할당량이 구성 된 경우 함수 앱을 일시적으로 사용 하지 않도록 설정 하 여 많은 포털 컨트롤을 사용할 수 없게 됩니다. 

+ [Azure Portal](https://portal.azure.com)를 확인 하려면 함수 앱에서 **플랫폼 기능** > **함수 앱 설정** 을 엽니다. 설정한 **일별 사용 할당량** 을 초과 하는 경우 다음 메시지가 표시 됩니다.

    `The function app has reached daily usage quota and has been stopped until the next 24 hours time frame.`

+ 이 문제를 해결 하려면 매일 할당량을 제거 하거나 늘리고 앱을 다시 시작 합니다. 그렇지 않으면 다음 날까지 앱 실행이 차단 됩니다.

## <a name="app-is-behind-a-firewall"></a>앱이 방화벽 뒤에 있습니다.

함수 앱이 [내부적으로 부하가 분산 된 App Service Environment](../app-service/environment/create-ilb-ase.md) 에서 호스트 되 고 인바운드 인터넷 트래픽을 차단 하도록 구성 되어 있거나 인터넷 액세스를 차단 하도록 구성 된 [인바운드 IP 제한이](functions-networking-options.md#inbound-ip-restrictions) 있는 경우 함수 런타임에 연결할 수 없게 됩니다. Azure Portal는 실행 중인 앱을 직접 호출 하 여 함수 목록을 가져오고 KUDU 끝점에 대 한 HTTP 호출도 수행 합니다. `Platform Features` 탭의 플랫폼 수준 설정은 계속 사용할 수 있습니다.

ASE 구성을 확인 하려면 ASE가 있는 서브넷의 NSG로 이동 하 고 인바운드 규칙의 유효성을 검사 하 여 응용 프로그램에 액세스 하는 컴퓨터의 공용 IP에서 들어오는 트래픽을 허용 합니다. 앱을 실행 하는 가상 네트워크에 연결 된 컴퓨터 또는 가상 네트워크에서 실행 되는 가상 컴퓨터에서 포털을 사용할 수도 있습니다. [인바운드 규칙 구성에 대 한 자세한 내용은 여기를 참조 하세요.](../app-service/environment/network-info.md#network-security-groups)

## <a name="next-steps"></a>다음 단계

함수 앱 모니터링에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [모니터 Azure Functions](functions-monitoring.md)
