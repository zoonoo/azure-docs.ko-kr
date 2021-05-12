---
title: '오류 해결: Azure Functions 런타임에 연결할 수 없음'
description: 잘못된 스토리지 계정 문제를 해결하는 방법에 대해 알아봅니다.
ms.topic: article
ms.date: 09/05/2018
ms.openlocfilehash: 6353e40113c3552ec26b20ded1833c8eb88f9f16
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108137674"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>오류 해결: “Azure Functions 런타임에 연결할 수 없음”

이 문서는 Azure Portal에 표시되는 다음과 같은 오류 문자열의 문제를 해결하는 데 도움이 됩니다.

> “오류: Azure Functions 런타임에 연결할 수 없습니다. 스토리지 구성에 대한 자세한 내용은 여기를 클릭하세요.”

Functions 런타임을 시작할 수 없으면 이 문제가 발생합니다. 이 오류가 발생하는 가장 일반적인 원인은 함수 앱이 스토리지 계정에 액세스할 수 없기 때문입니다. 자세한 내용은 [스토리지 계정 요구 사항](storage-considerations.md#storage-account-requirements)을 참조하세요.

이 문서의 나머지 부분은 각 사례를 식별하고 해결하는 방법을 비롯하여 이 오류의 특정 원인을 해결하는 데 도움을 줍니다.

## <a name="storage-account-was-deleted"></a>스토리지 계정이 삭제됨

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수 앱이 작동하지 않습니다.

먼저 애플리케이션 설정에서 스토리지 계정 이름을 조회합니다. `AzureWebJobsStorage` 또는 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 중 하나에 스토리지 계정 이름이 연결 문자열의 일부로 포함되어 있습니다. 자세한 내용은 [Azure Functions에 대한 앱 설정 참조](./functions-app-settings.md#azurewebjobsstorage)를 참조하세요.

Azure Portal에서 스토리지 계정을 검색하여 계정이 아직 있는지 확인합니다. 계정이 삭제된 경우에는 스토리지 계정을 다시 만들고 스토리지 연결 문자열을 바꿉니다. 그러면 함수 코드가 손실되므로 다시 배포해야 합니다.

## <a name="storage-account-application-settings-were-deleted"></a>스토리지 계정 애플리케이션 설정이 삭제됨

이전 단계에서 스토리지 계정 연결 문자열을 찾지 못했다면 설정을 삭제했거나 덮어썼을 가능성이 높습니다. 일반적으로는 배포 슬롯 또는 Azure Resource Manager 스크립트를 사용하여 애플리케이션 설정을 지정할 때 애플리케이션 설정을 삭제합니다.

### <a name="required-application-settings"></a>필수 애플리케이션 설정

* 필수:
    * [`AzureWebJobsStorage`](./functions-app-settings.md#azurewebjobsstorage)
* 프리미엄 플랜 함수에 필수:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](./functions-app-settings.md)
    * [`WEBSITE_CONTENTSHARE`](./functions-app-settings.md)

자세한 내용은 [Azure Functions에 대한 앱 설정 참조](./functions-app-settings.md)를 참조하세요.

### <a name="guidance"></a>지침

* 해당 설정에 대해서는 **슬롯 설정** 을 선택하지 않습니다. 배포 슬롯을 교환하면 함수 앱이 손상됩니다.
* 해당 설정을 자동 배포의 일부로 수정하지 마세요.
* 이러한 설정은 생성 시에 제공되어야 하며 유효한 상태여야 합니다. 해당 설정이 없는 자동 배포는 나중에 설정이 추가되더라도 함수 앱이 실행되지 않습니다.

## <a name="storage-account-credentials-are-invalid"></a>스토리지 계정 자격 증명이 잘못됨

스토리지 키를 다시 생성하는 경우에는 앞에서 언급한 스토리지 계정 연결 문자열을 업데이트해야 합니다. 스토리지 키 관리에 대한 자세한 내용은 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

## <a name="storage-account-is-inaccessible"></a>스토리지 계정에 액세스할 수 없음

함수 앱은 스토리지 계정에 액세스할 수 있어야 합니다. 함수 앱의 스토리지 계정 액세스를 차단하는 일반적인 문제는 다음과 같습니다.

* 함수 앱이 스토리지 계정에서 전송/수신하는 트래픽을 허용하는 올바른 네트워크 규칙을 포함하지 않고 ASE(App Service Environment)에 배포됩니다.

* 스토리지 계정 방화벽이 사용하도록 설정되었는데 Functions에서 전송/수신하는 트래픽을 허용하도록 구성되지 않았습니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../storage/common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.
* `allowSharedKeyAccess` 설정이 기본값인 `true`로 설정되어 있는지 확인합니다. 자세한 내용은 [Azure Storage 계정에 대한 공유 키 권한 부여 방지](../storage/common/shared-key-authorization-prevent.md?tabs=portal#verify-that-shared-key-access-is-not-allowed)를 참조하세요. 

## <a name="daily-execution-quota-is-full"></a>일일 실행 할당량이 꽉 참

일일 실행 할당량을 구성한 경우 함수 앱이 일시적으로 비활성화되고 포털 컨트롤을 대부분 사용할 수 없게 됩니다. 

[Azure Portal](https://portal.azure.com)에서 할당량을 확인하려면 함수 앱에서 **플랫폼 기능** > **함수 앱 설정** 을 선택합니다. 설정한 **일일 사용 할당량** 을 초과하는 경우 다음 메시지가 표시됩니다.

  > “함수 앱이 일일 사용 할당량에 도달하여 다음 24시간까지 중지되었습니다.”

이 문제를 해결하려면 일일 할당량을 제거하거나 늘리고 앱을 다시 시작합니다. 그러지 않으면 다음 날까지 애플리케이션의 실행이 차단됩니다.

## <a name="app-is-behind-a-firewall"></a>앱이 방화벽 뒤에 있음

다음 이유 중 하나로 인해 함수 앱에 연결할 수 없습니다.

* 함수 앱이 [내부적으로 부하가 분산된 App Service Environment](../app-service/environment/create-ilb-ase.md)에서 호스트되고 인바운드 인터넷 트래픽을 차단하도록 구성되어 있습니다.

* 함수 앱에 인터넷 액세스를 차단하도록 구성된 [인바운드 IP 제한](functions-networking-options.md#inbound-access-restrictions)이 있습니다. 

Azure Portal은 실행 중인 앱을 직접 호출하여 함수 목록을 가져오고 Kudu 엔드포인트에 대한 HTTP 호출을 수행합니다. 플랫폼 수준 설정은 **플랫폼 기능** 탭에서 계속 사용할 수 있습니다.

ASE 구성을 확인하려면 다음을 수행합니다.
1. ASE가 있는 서브넷의 NSG(네트워크 보안 그룹)로 이동합니다.
1. 애플리케이션에 액세스하는 컴퓨터의 공용 IP에서 들어오는 트래픽을 허용하는 인바운드 규칙의 유효성을 검사합니다. 
   
앱을 실행하는 가상 네트워크에 연결된 컴퓨터 또는 가상 네트워크에서 실행되는 가상 머신에 연결된 컴퓨터에서 포털을 사용할 수도 있습니다. 

인바운드 규칙 구성에 대한 자세한 내용은 [App Service Environment에 대한 네트워킹 고려 사항](../app-service/environment/network-info.md#network-security-groups)의 “네트워크 보안 그룹” 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

함수 앱 모니터링에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Functions 모니터링](functions-monitoring.md)