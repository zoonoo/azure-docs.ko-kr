---
title: '오류 해결: Azure 함수 런타임에 연결할 수 없습니다.'
description: 잘못된 스토리지 계정 문제를 해결하는 방법에 대해 알아봅니다.
author: alexkarcher-msft
ms.topic: article
ms.date: 09/05/2018
ms.author: alkarche
ms.openlocfilehash: 8fcd0661e2c7cab505121cf0d4d7b4c1d29017f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063784"
---
# <a name="troubleshoot-error-azure-functions-runtime-is-unreachable"></a>오류 해결: "Azure 함수 런타임에 연결할 수 없습니다."

이 문서에서는 Azure 포털에 나타나는 다음과 같은 오류 문자열을 해결하는 데 도움이 됩니다.

> "오류: Azure 함수 런타임에 연결할 수 없습니다. 저장소 구성에 대한 자세한 내용을 보려면 여기를 클릭하십시오."

이 문제는 Azure Functions 런타임을 시작할 수 없는 경우에 발생 합니다. 이 문제의 가장 일반적인 이유는 함수 앱이 저장소 계정에 액세스할 수 없게 되어 서 있습니다. 자세한 내용은 [저장소 계정 요구 사항을](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)참조하십시오.

이 문서의 나머지 부분에서는 각 사례를 식별하고 해결하는 방법을 포함하여 이 오류의 다음 원인을 해결하는 데 도움이 됩니다.

## <a name="storage-account-was-deleted"></a>저장소 계정이 삭제되었습니다.

모든 함수 앱은 스토리지 계정이 있어야 작동합니다. 해당 계정이 삭제되면 함수가 작동하지 않습니다.

먼저 응용 프로그램 설정에서 저장소 계정 이름을 검색합니다. `AzureWebJobsStorage` 또는 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 연결 문자열에 싸여 저장소 계정의 이름을 포함합니다. 자세한 내용은 [Azure Functions에 대한 앱 설정 참조를](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)참조하십시오.

Azure 포털에서 저장소 계정을 검색하여 저장소 계정이 여전히 존재하는지 확인합니다. 삭제된 경우 저장소 계정을 다시 만들고 저장소 연결 문자열을 바꿉니다. 함수 코드가 손실되어 다시 배포해야 합니다.

## <a name="storage-account-application-settings-were-deleted"></a>저장소 계정 응용 프로그램 설정이 삭제되었습니다.

이전 단계에서 저장소 계정 연결 문자열을 찾을 수 없는 경우 삭제 되었거나 덮어씁니다. 응용 프로그램 설정을 삭제하는 것은 배포 슬롯 또는 Azure 리소스 관리자 스크립트를 사용하여 응용 프로그램 설정을 설정할 때 가장 일반적으로 발생합니다.

### <a name="required-application-settings"></a>필수 애플리케이션 설정

* 필수:
    * [`AzureWebJobsStorage`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings#azurewebjobsstorage)
* 소비 계획 기능에 필요한 사항:
    * [`WEBSITE_CONTENTAZUREFILECONNECTIONSTRING`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)
    * [`WEBSITE_CONTENTSHARE`](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)

자세한 내용은 [Azure Functions에 대한 앱 설정 참조를](https://docs.microsoft.com/azure/azure-functions/functions-app-settings)참조하십시오.

### <a name="guidance"></a>지침

* 이러한 설정에 대해 "슬롯 설정"을 확인하지 마십시오. 배포 슬롯을 교체하면 함수 앱이 중단됩니다.
* 자동화된 배포의 일부로 이러한 설정을 수정하지 마십시오.
* 이러한 설정은 생성 시에 제공되어야 하며 유효한 상태여야 합니다. 이러한 설정이 포함되지 않은 자동화된 배포는 나중에 설정이 추가되더라도 실행되지 않는 함수 앱으로 이됩니다.

## <a name="storage-account-credentials-are-invalid"></a>저장소 계정 자격 증명이 잘못되었습니다.

저장소 키를 다시 생성하는 경우 이전에 설명한 저장소 계정 연결 문자열을 업데이트해야 합니다. 저장소 키 관리에 대한 자세한 내용은 [Azure Storage 계정 만들기를](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account)참조하십시오.

## <a name="storage-account-is-inaccessible"></a>저장소 계정에 액세스할 수 없습니다.

함수 앱은 저장소 계정에 액세스할 수 있어야 합니다. 함수 앱이 저장소 계정에 액세스하는 것을 차단하는 일반적인 문제는 다음과 같습니다.

* 함수 앱은 저장소 계정과 트래픽을 허용하는 올바른 네트워크 규칙 없이 앱 서비스 환경에 배포됩니다.

* 저장소 계정 방화벽이 활성화되어 있으며 함수와 트래픽을 허용하도록 구성되지 않았습니다. 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

## <a name="daily-execution-quota-is-full"></a>일일 실행 할당량이 가득 찼습니다.

일일 실행 할당량이 구성된 경우 함수 앱이 일시적으로 비활성화되어 많은 포털 컨트롤을 사용할 수 없게 됩니다. 

[Azure 포털에서](https://portal.azure.com)할당량을 확인하려면 함수 앱에서 **플랫폼 기능** > **기능 앱 설정을** 선택합니다. 설정한 일일 사용량 **할당량을** 초과한 경우 다음 메시지가 표시됩니다.

  > "함수 앱은 일일 사용 할당량에 도달했으며 다음 24시간 시간 까지 중지되었습니다."

이 문제를 해결하려면 일일 할당량을 제거하거나 늘인 다음 앱을 다시 시작합니다. 그렇지 않으면 다음 날까지 앱 실행이 차단됩니다.

## <a name="app-is-behind-a-firewall"></a>앱이 방화벽 뒤에 있습니다.

다음 이유 중 하나에 따라 함수 런타임에 연결할 수 없습니다.

* 함수 앱은 내부적으로 [부하가 분산된 앱 서비스 환경에서](../app-service/environment/create-ilb-ase.md) 호스팅되며 인바운드 인터넷 트래픽을 차단하도록 구성됩니다.

* 함수 앱에는 인터넷 액세스를 차단하도록 구성된 [인바운드 IP 제한이](functions-networking-options.md#inbound-ip-restrictions) 있습니다. 

Azure 포털은 실행 중인 앱에 직접 호출하여 함수 목록을 가져오고 KUdu 끝점에 HTTP 호출을 합니다. **플랫폼 기능** 탭에서 플랫폼 수준 설정을 계속 사용할 수 있습니다.

앱 서비스 환경 구성을 확인하려면 다음을 수행하십시오.
1. 앱 서비스 환경이 있는 서브넷의 NSG(네트워크 보안 그룹)로 이동합니다.
1. 인바운드 규칙의 유효성을 검사하여 응용 프로그램에 액세스하는 컴퓨터의 공용 IP에서 오는 트래픽을 허용합니다. 
   
앱을 실행하는 가상 네트워크에 연결된 컴퓨터 또는 가상 네트워크에서 실행 중인 가상 시스템에 포털을 사용할 수도 있습니다. 

인바운드 규칙 구성에 대한 자세한 내용은 앱 서비스 [환경에 대한 네트워킹 고려 사항의](https://docs.microsoft.com/azure/app-service/environment/network-info#network-security-groups)"네트워크 보안 그룹" 섹션을 참조하십시오.

## <a name="next-steps"></a>다음 단계

기능 앱 모니터링에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Functions 모니터링](functions-monitoring.md)
