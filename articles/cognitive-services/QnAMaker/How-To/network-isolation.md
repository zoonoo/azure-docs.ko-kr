---
title: 네트워크 격리
description: 사용자는 QnA Maker 리소스에 대 한 공용 액세스를 제한할 수 있습니다.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 4e9b45c72e5fb4cbd9e548727faf8946e3e5ba8f
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102125088"
---
# <a name="recommended-settings-for-network-isolation"></a>네트워크 격리에 대 한 권장 설정

QnA Maker 리소스에 대 한 공용 액세스를 제한 하려면 다음 단계를 수행 해야 합니다. [가상 네트워크를 구성](../../cognitive-services-virtual-networks.md?tabs=portal)하 여 공용 액세스에서 Cognitive Services 리소스를 보호 합니다.

## <a name="restrict-access-to-cognitive-search-resource"></a>Cognitive Search 리소스에 대 한 액세스 제한

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker 일반 공급(안정적인 릴리스)](#tab/v1)

VNET 내의 개인 끝점으로 Cognitive Search 구성 QnA Maker 리소스를 만드는 동안 검색 인스턴스가 생성 되는 경우 Cognitive Search을 강제 적용 하 여 고객의 VNet 내에서 완전히 생성 된 개인 끝점 구성을 지원할 수 있습니다.

모든 리소스를 동일한 지역에 만들어 개인 끝점을 사용 해야 합니다.

* QnA Maker 리소스
* 새 Cognitive Search 리소스
* 새 Virtual Network 리소스

[Azure Portal](https://portal.azure.com)에서 다음 단계를 완료 합니다.

1. [QnA Maker 리소스](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)를 만듭니다.
2. 끝점 연결 (데이터)을 _개인_ 으로 설정 하 여 새 Cognitive Search 리소스를 만듭니다. 1 단계에서 만든 QnA Maker 리소스와 동일한 지역에 리소스를 만듭니다. [Cognitive Search 리소스를 만드는](../../../search/search-create-service-portal.md)방법에 대해 자세히 알아보고이 링크를 사용 하 여 [리소스의 만들기 페이지로](https://ms.portal.azure.com/#create/Microsoft.Search)직접 이동 합니다.
3. 새 [Virtual Network 리소스](https://ms.portal.azure.com/#create/Microsoft.VirtualNetwork-ARM)를 만듭니다.
4. 이 절차의 1 단계에서 만든 App service 리소스에서 VNET을 구성 합니다. 2 단계에서 만든 새 Cognitive Search 리소스에 대해 VNET에 새 DNS 항목을 만듭니다. Cognitive Search IP 주소입니다.
5. 2 단계에서 만든 [새 Cognitive Search 리소스에 App service를 연결](../how-to/set-up-qnamaker-service-azure.md) 합니다. 그런 다음 1 단계에서 만든 원래 Cognitive Search 리소스를 삭제할 수 있습니다.
    
[QnA Maker 포털](https://www.qnamaker.ai/)에서 첫 번째 기술 자료를 만듭니다.

#  <a name="qna-maker-managed-preview-release"></a>[QnA Maker 관리형(미리 보기 릴리스)](#tab/v2)

Azure Search 리소스에 대 한 [개인 끝점을 만듭니다](../reference-private-endpoint.md) .

---

## <a name="restrict-access-to-app-service-qna-runtime"></a>App Service에 대 한 액세스 제한 (QnA 런타임)

App service allowlist에 Ip를 추가 하 여 액세스를 제한 하거나 QnA Maker App Service를 호스팅하도록 App Service 환경을 구성할 수 있습니다.

#### <a name="add-ips-to-app-service-allowlist"></a>App Service allowlist에 Ip 추가

1. Cognitive Services Ip의 트래픽만 허용 합니다. 이러한 설정은 서비스 태그에 이미 포함 되어 `CognitiveServicesManagement` 있습니다. 이는 app service를 호출 하 고 Azure Search 서비스를 업데이트 하는 Api (만들기/업데이트 KB)를 작성 하는 데 필요 합니다. [서비스 태그에 대 한 자세한 정보를](../../../virtual-network/service-tags-overview.md) 확인 하세요.
2. Azure Bot Service, QnA Maker 포털 등의 다른 진입점도 예측 "GenerateAnswer" API 액세스에 사용할 수 있도록 해야 합니다.
3. IP 주소 범위를 allowlist에 추가 하려면 다음 단계를 수행 하세요.

   1. [모든 서비스 태그의 IP 범위를](https://www.microsoft.com/download/details.aspx?id=56519)다운로드 합니다.
   2. "CognitiveServicesManagement"의 Ip를 선택 합니다.
   3. App Service 리소스의 네트워킹 섹션으로 이동 하 고 "액세스 제한 구성" 옵션을 클릭 하 여 allowlist에 Ip를 추가 합니다.

또한 App Service에 대해 동일한 작업을 수행 하는 자동화 된 스크립트가 있습니다. GitHub에서 allowlist을 [구성 하는 PowerShell 스크립트](https://github.com/pchoudhari/QnAMakerBackupRestore/blob/master/AddRestrictedIPAzureAppService.ps1) 를 찾을 수 있습니다. 구독 id, 리소스 그룹 및 실제 App Service 이름을 스크립트 매개 변수로 입력 해야 합니다. 스크립트를 실행 하면 App Service allowlist에 Ip가 자동으로 추가 됩니다.

#### <a name="configure-app-service-environment-to-host-qna-maker-app-service"></a>QnA Maker를 호스트 App Service Environment 구성 App Service
    
ASE (App Service Environment)를 사용 하 여 QnA Maker App Service를 호스트할 수 있습니다. 다음 단계를 따르세요.

1. App Service Environment 만들고 "external"으로 표시 합니다. 지침은 [자습서](../../../app-service/environment/create-external-ase.md) 를 따르세요.
2.  App Service Environment 내에서 App service를 만듭니다.
    1. App service에 대 한 구성을 확인 하 고 응용 프로그램 설정으로 ' PrimaryEndpointKey '를 추가 합니다. ' PrimaryEndpointKey '의 값을 " \<app-name\> -primaryendpointkey"로 설정 해야 합니다. 앱 이름은 App service URL에 정의 됩니다. 예를 들어 App service URL이 "mywebsite.myase.p.azurewebsite.net" 인 경우 응용 프로그램 이름은 "mywebsite"입니다. 이 경우 ' PrimaryEndpointKey '의 값을 "mywebsite-PrimaryEndpointKey"로 설정 해야 합니다.
    2. Azure search 서비스를 만듭니다.
    3. Azure Search 및 앱 설정이 적절히 구성 되어 있는지 확인 합니다. 
          이 [자습서](../reference-app-service.md?tabs=v1#app-service)를 수행 하세요.
3.  App Service Environment 연결 된 네트워크 보안 그룹을 업데이트 합니다.
    1. 요구 사항에 따라 미리 만든 인바운드 보안 규칙을 업데이트 합니다.
    2. 소스를 ' Service Tag '로, 소스 서비스 태그를 ' CognitiveServicesManagement '로 사용 하 여 새 인바운드 보안 규칙을 추가 합니다.
4.  QnA Maker 끝점을 위에서 만든 App Service 끝점 (https://mywebsite.myase.p.azurewebsite.net)으로 설정 해야 하는 Azure Resource Manager를 사용 하 여 QnA Maker 인식 서비스 인스턴스 (Cognitiveservices account/accounts)를 만듭니다.
    
---
