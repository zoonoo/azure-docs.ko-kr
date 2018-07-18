---
title: API 버전 프로필을 사용 하 여 Azure Stack에서 Ruby를 사용 하 여 | Microsoft Docs
description: Azure Stack에서 Ruby를 사용 하 여 API 버전 프로필을 사용 하는 방법을 알아봅니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 4d62c192b4e74980fc8cd8a671a702ba2ddfdbcb
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2018
ms.locfileid: "37866596"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Azure Stack에서 Ruby를 사용 하 여 API 버전 프로필 사용

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

## <a name="ruby-and-api-version-profiles"></a>Ruby 및 API 버전 프로필

Ruby SDK는 Azure Stack Resource Manager에 대 한 빌드 및 인프라를 관리 하는 데 유용한 도구를 제공 합니다. SDK의 리소스 공급자에는 계산, 가상 네트워크 및 Ruby 언어를 사용 하 여 storage 포함 됩니다. Ruby SDK의 API 프로필 전역 Azure 리소스 및 Azure Stack에서 리소스 간에 전환할 수 있도록 하 여 하이브리드 클라우드 개발을 사용 합니다.

프로필을 API에는 리소스 공급자 및 서비스 버전의 조합입니다. 다른 리소스 유형을 결합 하는 API 프로필을 사용할 수 있습니다.

 - 최신 버전의 모든 서비스를 사용 하 고 사용 합니다 **최신** Azure SDK 롤업 gem의 프로필입니다.
 - Azure Stack 호환 서비스를 사용 하려면 사용 합니다 **V2017_03_09** Azure SDK 롤업 gem의 프로필입니다.
 - 서비스의 최신 api 버전을 사용 하려면 사용 합니다 **최신** 특정 gem의 프로필입니다. 단독으로 계산 서비스의 최신 api 버전을 사용 하려는 경우 사용 하는 예를 들어,를 **최신** 의 프로필을 **계산** gem 합니다.
 - 서비스에 대 한 특정 api 버전을 사용 하 여 gem 내에 정의 된 특정 API 버전을 사용 합니다.

> [!Note]   
> 모든 동일한 응용 프로그램 옵션을 결합할 수 있습니다.

## <a name="install-the-azure-ruby-sdk"></a>Azure Ruby SDK 설치

 - 공식 설치 지침을 따릅니다 [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)합니다.
 - 공식 설치 지침을 따릅니다 [Ruby](https://www.ruby-lang.org/en/documentation/installation/)합니다.
    - 설치 하는 동안 선택 **경로 변수에 추가 Ruby**
    - 메시지가 표시 되 면 Ruby 설치 하는 동안 개발자 키트를 설치 합니다.
    - 다음으로, 다음 명령을 사용 하 여 bundler 설치:  
      `Gem install bundler`
 - 사용할 수 없는 경우 구독을 만들고 나중에 사용할 구독 ID를 저장 합니다. 구독을 만들려면 지침은 [여기](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm)합니다. 
 - 서비스 주체를 만들고 해당 ID 및 암호를 저장 합니다. Azure Stack에 대 한 서비스 주체를 만드는 지침은 [여기](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)합니다. 
 - 구독에 서비스 주체에 참가자/소유자 역할이 있는지 확인 합니다. 서비스 주체에 역할을 할당 하는 방법에 지침은 [여기](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)합니다.

## <a name="install-the-rubygem-packages"></a>Rubygem 패키지 설치

Azure rubygem 패키지를 직접 설치할 수 있습니다.

````Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
Or use them in your Gemfile.
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
````

Azure Resource Manager Ruby SDK 미리 보기로 제공 되며 이후 릴리스에서 주요 인터페이스 변경 내용 있을 알아야 합니다. 부 버전 번호를 증가 차이 나타낼 수 있습니다.

## <a name="usage-of-the-azuresdk-gem"></a>Azure_sdk gem 사용

Gem을 azure_sdk는 Ruby SDK에서 지원 되는 모든 gem 합계입니다. 이 입력 하 여이 gem으로 이루어져를 **최신** 프로필을 지 원하는 모든 서비스의 최신 버전입니다. 버전이 지정 된 프로필 소개 **V2017_03_09** 프로필 Azure Stack 용 기본 제공 됩니다.

다음 명령을 사용 하 여 azure_sdk 롤업 gem을 설치할 수 있습니다.  

````Ruby  
  gem install 'azure_sdk
````

## <a name="prerequisite"></a>필수 요소

Ruby Azure SDK에서 Azure Stack을 사용 하려면 다음 값을 제공 하 고 환경 변수를 사용 하 여 값을 설정 해야 합니다. 운영 체제에서 환경 변수 설정에 대 한 표 다음에 나오는 지침을 참조 하세요. 

| 값 | 환경 변수 | 설명 | 
| --- | --- | --- | --- |
| 테넌트 ID | AZURE_TENANT_ID | Azure Stack에 값 [테 넌 트 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-overview)합니다. |
| 클라이언트 ID | AZURE_CLIENT_ID | 주 응용 프로그램 ID 저장이 문서의 이전 섹션에서 서비스 주체를 만들 때 서비스입니다.  |
| 구독 ID | AZURE_SUBSCRIPTION_ID | 합니다 [구독 ID](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview#subscriptions) 제품을 액세스 하는 방법에 Azure Stack에서. |
| 클라이언트 암호 | AZURE_CLIENT_SECRET | 서비스 주체 응용 프로그램 서비스 주체를 만들 때 암호 저장 합니다. |
| Resource Manager 끝점 | ARM_ENDPOINT | 참조 [The Azure Stack resource manager endpoin](#The-azure-stack-resource-manager-endpoint)합니다.  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Azure Stack resource manager 끝점

Microsoft Azure 리소스 관리자는 관리자가 배포, 관리 및 Azure 리소스를 모니터링할 수 있도록 관리 합니다. Azure Resource Manager 그룹으로 대신 개별적으로 단일 작업에서 이러한 작업을 처리할 수 있습니다.

Resource Manager 끝점에서 메타 데이터 정보를 가져올 수 있습니다. 코드를 실행 하는 데 필요한 정보를 사용 하 여 JSON 파일을 반환 하는 끝점입니다.

  > [!Note]  
  > 합니다 **ResourceManagerUrl** 에 Azure Stack 개발 키트 ASDK ()는: `https://management.local.azurestack.external/`  
  > 합니다 **ResourceManagerUrl** 통합된 시스템의: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
  > 에 필요한 메타 데이터를 검색 합니다. `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
  샘플 JSON 파일:

  ```json
  { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
    "graphEndpoint": "https://graph.windows.net/",  
    "portal Endpoint": "https://portal.local.azurestack.external/", 
    "authentication": {
      "loginEndpoint": "https://login.windows.net/", 
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
    }
  }
  ```

### <a name="set-environmental-variables"></a>환경 변수 설정

**Microsoft Windows**  
Windows 명령 프롬프트에서 환경 변수를 설정 하려면 다음 형식을 사용 합니다.  
`set AZURE_TENANT_ID=<YOUR_TENANT_ID>`

**macOS, Linux 및 Unix 기반 시스템**  
Unix 기반 시스템에서와 같은 명령을 사용할 수 있습니다.  
`export AZURE_TENANT_ID=<YOUR_TENANT_ID>`

## <a name="existing-api-profiles"></a>기존 API 프로필

Azure_sdk 롤업 gem에는 다음 두 프로필에 있습니다.

1. **V2017_03_09**  
  Azure Stack 용으로 작성 된 프로필입니다. Azure Stack을 사용 하 여 가장 호환 되도록 서비스에 대 한이 프로필을 사용 합니다.
2. **최신**  
  프로필의 모든 서비스의 최신 버전으로 구성 됩니다. 모든 서비스의 최신 버전을 사용 합니다.

Azure Stack 및 API 프로필에 대 한 자세한 내용은 참조는 [API의 요약 프로필](azure-stack-version-profiles.md#summary-of-api-profiles)합니다.

## <a name="azure-ruby-sdk-api-profile-usage"></a>Azure Ruby SDK API 프로필 사용

다음 줄 프로필 클라이언트를 인스턴스화하려면 사용할 해야 합니다. 이 매개 변수는 Azure Stack 또는 다른 사설 클라우드 필요 합니다. 전역 Azure는 기본적으로 이러한 설정에 이미 있습니다.

````Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
    ENV['AZURE_TENANT_ID'],
    ENV['AZURE_CLIENT_ID'],
    ENV['AZURE_CLIENT_SECRET'],
    active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
````

계산, 저장소 및 네트워크와 같은 개별 리소스 공급자에 액세스 하는 프로필 클라이언트를 사용할 수 있습니다.

````Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2017_03_09::Compute::Mgmt::Models::PurchasePlan.new
````

## <a name="define-azurestack-environment-setting-functions"></a>AzureStack 환경 설정 함수를 정의 합니다.

Azure Stack 환경에 서비스 주체 인증을 사용 하 여 끝점을 정의 **get_active_directory_settings()** 합니다. 이 메서드를 사용 합니다 **ARM_Endpoint** 환경 변수를 설정할 때 설정 하는 환경 변수입니다.

````Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
        error_model = JSON.load(response_content)
        fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
end
````

## <a name="samples-using-api-profiles"></a>API 프로필을 사용 하는 샘플

다음에 있는 샘플 GitHub repositoreis Ruby 및 Azure Stack API 프로필을 사용 하 여 솔루션을 만드는 참조로 사용할 수 있습니다.

 - [Ruby를 사용하여 Azure 리소스 및 리소스 그룹 관리](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)
 - [Ruby를 사용 하 여 virtual machines 관리](https://github.com/Azure-Samples/compute-ruby-manage-vm/tree/master/Hybrid)
 - [Ruby에서 템플릿을 사용하여 SSH 사용 VM 배포](https://github.com/Azure-Samples/resource-manager-ruby-template-deployment/tree/master/Hybrid)

### <a name="sample-resource-manager-and-groups"></a>샘플 Resource Manager 및 그룹

샘플을 실행 하려면 Ruby 설치를 확인 합니다. Visual Studio Code를 사용 하는 경우에 확장으로 Ruby SDK를 다운로드 합니다. 

> [!Note]  
> 샘플 리포지토리를 가져올 수 있습니다 "[Ruby 사용 하 여 리소스 그룹과 Azure 리소스 관리](https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups/tree/master/Hybrid)"입니다.

1. 리포지토리를 복제 합니다.

    ````Bash
    git clone https://github.com/Azure-Samples/resource-manager-ruby-resources-and-groups.git
    ````

2. 번들을 사용 하 여 종속성을 설치 합니다.

    ````Bash
    cd resource-manager-ruby-resources-and-groups\Hybrid\
    bundle install
    ````

3. PowerShell을 사용 하 여 Azure 서비스 주체를 만들고 필요한 값을 검색 합니다. 

  서비스 주체를 만드는 방법에 지침은 [인증서를 사용 하 여 서비스 주체를 만들려면 Azure PowerShell을 사용 하 여](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals)입니다.

  필요한 값은
  - 테넌트 ID
  - 클라이언트 ID
  - 클라이언트 암호
  - 구독 ID
  - Resource Manager 끝점

  사용자가 만든 서비스 주체에서를 검색 한 정보를 사용 하 여 다음 환경 변수를 설정 합니다.

  - AZURE_TENANT_ID 내보내기 = {테 넌 트 id}
  - AZURE_CLIENT_ID 내보내기 = {client id}
  - AZURE_CLIENT_SECRET 내보내기 = {클라이언트 암호}
  - AZURE_SUBSCRIPTION_ID 내보내기 = {구독 id}
  - ARM_ENDPOINT 내보내기 = {azurestack의 경우 Resource manager url}

  > [!Note]  
  > Windows에서 내보내기 하는 대신 집합을 사용 합니다.

4. 위치 변수 AzureStack 위치로 설정 되었는지 확인 합니다. 예를 들어 로컬 = "local"

5. 다음 코드 줄에 추가 하는 경우 오른쪽 active directory 끝점을 대상으로 Azure Stack 또는 다른 사설 클라우드를 사용 하 여 합니다.

  ````Ruby  
  active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
  ````

6. 옵션 변수 내에서 active directory 설정 및 Azure Stack을 사용 하려면 기본 URL을 추가 합니다. 

  ````Ruby  
  options = {
    credentials: credentials,
    subscription_id: subscription_id,
    active_directory_settings: active_directory_settings,
    base_url: ENV['ARM_ENDPOINT']
  }
  ````

7. Azure Stack 프로필을 대상으로 하는 프로필 클라이언트를 만듭니다.

  ````Ruby  
    client = Azure::Resources::Profiles::V2017_03_09::Mgmt::Client.new(options)
  ````

8. Azure Stack을 사용 하 여 서비스 주체를 인증 하려면 끝점 정의 되어야 합니다 **get_active_directory_settings()** 합니다. 이 메서드를 사용 합니다 **ARM_Endpoint** 환경 변수를 설정할 때 설정 하는 환경 변수입니다.

  ````Ruby  
  def get_active_directory_settings(armEndpoint)
    settings = MsRestAzure::ActiveDirectoryServiceSettings.new
    response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
    status_code = response.code
    response_content = response.body
    unless status_code == "200"
      error_model = JSON.load(response_content)
      fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
    end
    result = JSON.load(response_content)
    settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
    settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
    settings
  end
  ````

9. 샘플을 실행합니다.

  ````Ruby
    bundle exec ruby example.rb
  ````

## 

## <a name="next-steps"></a>다음 단계

* [Azure Stack용 PowerShell 설치](azure-stack-powershell-install.md)
* [Azure Stack 사용자의 PowerShell 환경 구성](azure-stack-powershell-configure-user.md)  
