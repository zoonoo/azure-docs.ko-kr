---
title: 네트워크 격리
description: 사용자는 QnA Maker 리소스에 대한 퍼블릭 액세스를 제한할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: fa24347c8fcc0550dc6dc86c96624d1b1f6dcf25
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376326"
---
# <a name="recommended-settings-for-network-isolation"></a>네트워크 격리에 대한 권장 설정

QnA Maker 리소스에 대한 퍼블릭 액세스를 제한하려면 다음 단계를 수행해야 합니다. [가상 네트워크를 구성](../../cognitive-services-virtual-networks.md?tabs=portal)하여 퍼블릭 액세스에서 Cognitive Services 리소스를 보호합니다.

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service(QnA 런타임)에 대한 액세스 제한

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

App Service 허용 목록에 IP를 추가하여 액세스를 제한하거나 QnA Maker App Service를 호스트하도록 App Service Environment를 구성할 수 있습니다.

#### <a name="add-ips-to-app-service-allow-list"></a>App Service 허용 목록에 IP 추가

1. Cognitive Services IP의 트래픽만 허용합니다. 이는 이미 서비스 태그 `CognitiveServicesManagement`에 포함되어 있습니다. 이는 작성 API(KB 만들기/업데이트)가 App Service를 호출하고 그에 따라 Azure Search Service를 업데이트하는 데 필요합니다. [서비스 태그에 대한 자세한 정보](../../../virtual-network/service-tags-overview.md)를 확인하세요.
2. Azure Bot Service, QnA Maker 포털 등의 다른 진입점도 예측 "GenerateAnswer" API 액세스에 사용할 수 있도록 해야 합니다.
3. IP 주소 범위를 허용 목록에 추가하려면 다음 단계를 수행하세요.

   1. [모든 서비스 태그의 IP 범위](https://www.microsoft.com/download/details.aspx?id=56519)를 다운로드합니다.
   2. "CognitiveServicesManagement"의 IP를 선택합니다.
   3. App Service 리소스의 네트워킹 섹션으로 이동하고 "액세스 제한 구성" 옵션을 클릭하여 허용 목록에 IP를 추가합니다.

또한 App Service에 대해 동일한 작업을 수행하는 자동화된 스크립트가 있습니다. GitHub에서 [허용 목록을 구성하는 PowerShell 스크립트](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1)를 찾을 수 있습니다. 구독 ID, 리소스 그룹 및 실제 App Service 이름을 스크립트 매개 변수로 입력해야 합니다. 스크립트를 실행하면 App Service 허용 목록에 IP가 자동으로 추가됩니다.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>QnA Maker App Service를 호스트하도록 App Service Environment 구성
    
App Service Environment(ASE)는 QnA Maker App Service를 호스트하는 데 사용할 수 있습니다. 다음 단계를 따르세요.

1. App Service Environment를 만들고 "external"로 표시합니다. 지침은 [자습서](../../../app-service/environment/create-external-ase.md)를 따르세요.
2.  App Service Environment 내부에 App Service를 만듭니다.
    1. App Service에 대한 구성을 확인하고 애플리케이션 설정으로 'PrimaryEndpointKey'를 추가합니다. 'PrimaryEndpointKey'의 값을 "\<app-name\>-PrimaryEndpointKey”로 설정해야 합니다. 앱 이름은 App Service URL에 정의됩니다. 예를 들어 App Service URL이 "mywebsite.myase.p.azurewebsite.net"인 경우 애플리케이션 이름은 "mywebsite"입니다. 이 경우 'PrimaryEndpointKey'의 값을 "mywebsite-PrimaryEndpointKey"로 설정해야 합니다.
    2. Azure Search Service를 만듭니다.
    3. Azure Search 및 앱 설정이 적절히 구성되어 있는지 확인합니다. 
          이 [자습서](../reference-app-service.md?tabs=v1#app-service)를 따르세요.
3.  App Service Environment와 연결된 네트워크 보안 그룹을 업데이트합니다.
    1. 요구 사항에 따라 미리 만든 인바운드 보안 규칙을 업데이트합니다.
    2. 소스를 'Service Tag'로, 소스 서비스 태그를 'CognitiveServicesManagement'로 사용하여 새 인바운드 보안 규칙을 추가합니다.
       
    ![인바운드 포트 예외](../media/inbound-ports.png)

4.  Azure Resource Manager를 사용하여 QnA Maker Cognitive Service 인스턴스(Microsoft.CognitiveServices/accounts)를 만듭니다. 여기서는 QnA Maker 엔드포인트를 앞에서 만든 App Service 엔드포인트로 설정해야 합니다(https:// mywebsite.myase.p.azurewebsite.net).
    
# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

앱 서비스는 사용자 지정 질문 답변과 함께 배포되지 않습니다.

---

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search 리소스에 대한 액세스 제한

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

Cognitive Search 인스턴스는 QnA Maker 리소스를 만든 후 프라이빗 엔드포인트를 통해 격리할 수 있습니다. 프라이빗 엔드포인트 연결에는 Search Service 인스턴스에 액세스할 수 있는 VNet이 필요합니다. 

App Service Environment를 사용하여 QnA Maker App Service를 제한한 경우 동일한 VNet을 사용하여 Cognitive Search 인스턴스에 대한 프라이빗 엔드포인트 연결을 만듭니다. VNet에서 새 DNS 항목을 만들어 Cognitive Search 엔드포인트를 Cognitive Search 프라이빗 엔드포인트 IP 주소에 매핑합니다. 

QnA Maker App Service에 App Service Environment를 사용하지 않는 경우 먼저 새 VNet 리소스를 만든 다음, Cognitive Search 인스턴스에 대한 프라이빗 엔드포인트 연결을 만듭니다. 이 경우 QnA Maker App Service를 [VNet과 통합](../../../app-service/web-sites-integrate-with-vnet.md)하여 Cognitive Search 인스턴스에 연결해야 합니다. 

# <a name="custom-question-answering-preview-release"></a>[사용자 지정 질문 답변(미리 보기 릴리스)](#tab/v2)

Azure Search 리소스에 대한 [프라이빗 엔드포인트를 만듭니다](../reference-private-endpoint.md).

---

