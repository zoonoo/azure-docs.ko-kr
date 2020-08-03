---
title: '빠른 시작: 첫 번째 Ruby 쿼리'
description: 이 빠른 시작에서는 단계에 따라 Ruby용 Resource Graph gem을 사용하도록 설정하고 첫 번째 쿼리를 실행합니다.
ms.date: 07/12/2020
ms.topic: quickstart
ms.openlocfilehash: 9763e2e9ec49d6fb4ea37fac12578ab23b7b5363
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87101141"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-ruby"></a>빠른 시작: Ruby를 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 Ruby에 필요한 gem이 설치되었는지 확인하는 것입니다. 이 빠른 시작에서는 Ruby 설치에 gem을 추가하는 과정을 안내합니다.

이 과정이 끝나면 Ruby 설치에 gem이 추가되고 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- _clientId_ 및 _clientSecret_을 포함하는 Azure 서비스 주체.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-the-resource-graph-project"></a>Resource Graph 프로젝트 만들기

Ruby를 사용하도록 설정하여 Azure Resource Graph를 쿼리하려면 해당 gem을 `Gemfile`에 추가해야 합니다. 이 gem은 [Azure Cloud Shell](https://shell.azure.com), [Windows 10의 bash](/windows/wsl/install-win10) 또는 로컬 설치를 비롯하여 Ruby를 사용할 수 있는 위치에 따라 작동합니다.

1. 최신 Ruby가 설치되었는지 확인합니다(**2.7.1** 이상). 아직 설치되지 않았으면 [Ruby-Lang.org](https://www.ruby-lang.org/en/downloads/)에서 다운로드합니다.

1. 선택한 Ruby 환경에서 새 프로젝트 폴더의 번들을 초기화합니다.

   ```bash
   # Initialize a bundle to create a new Gemfile
   bundle init
   ```

1. Azure Resource Graph에 필요한 gem을 사용하여 `Gemfile`을 업데이트합니다. 업데이트된 파일은 다음 예제와 비슷합니다.

   ```file
   # frozen_string_literal: true

   source "https://rubygems.org"

   git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

   # gem "rails"
   gem 'azure_mgmt_resourcegraph', '~> 0.17.2'
   ```

1. 프로젝트 폴더에서 `bundle install`을 실행합니다. `bundle list`를 사용하여 gem을 설치했는지 확인합니다.

1. 동일한 프로젝트 폴더에서 다음 코드를 사용하여 `argQuery.rb`를 만들고 업데이트된 파일을 저장합니다.

   ```ruby
   #!/usr/bin/env ruby

   require 'azure_mgmt_resourcegraph'
   ARG = Azure::ResourceGraph::Profiles::Latest::Mgmt

   # Get arguments and set options
   options = {
       tenant_id: ARGV[0],
       client_id: ARGV[1],
       client_secret: ARGV[2],
       subscription_id: ARGV[3]
   }

   # Create Resource Graph client from options
   argClient = ARG::Client.new(options)

   # Create Resource Graph QueryRequest for subscription with query
   query_request = ARGModels::QueryRequest.new.tap do |qr|
       qr.subscriptions = [ARGV[3]]
       qr.query = ARGV[4]
   end

   # Get the resources from Resource Graph
   response = argClient.resources(query_request)

   # Convert data to JSON and output
   puts response.data.to_json
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

Ruby 스크립트를 저장하고 사용할 준비가 되면 간단한 Resource Graph 쿼리를 사용해 볼 수 있습니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식**과 함께 처음 5개 Azure 리소스를 반환합니다.

`argQuery`를 호출할 때마다 다음 변수를 각자 해당하는 값으로 바꿔야 합니다.

- `{tenantId}` - 테넌트 ID로 대체
- `{clientId}` - 서비스 주체의 클라이언트 ID로 대체
- `{clientSecret}` - 서비스 주체의 클라이언트 암호로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

1. `Gemfile` 및 `argClient.rb` 파일을 만든 프로젝트 폴더로 디렉터리를 변경합니다.

1. gem 및 `resources` 메서드를 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다.

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다.

1. 마지막 매개 변수를 `argQuery.rb`로 변경하고, **Name** 속성을 기준으로 `order by`하도록 쿼리를 변경합니다.

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. 마지막 매개 변수를 `argQuery.rb`로 변경하고, 먼저 **Name** 속성을 기준으로 `order by`한 후 상위 5개 결과로 `limit`하도록 쿼리를 변경합니다.

   ```bash
   ruby argQuery.rb "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="clean-up-resources"></a>리소스 정리

Ruby 환경에서 설치된 gem을 제거하려면 다음 명령을 사용할 수 있습니다.

```bash
# Remove the installed gems from the Ruby environment
gem uninstall azure_mgmt_resourcegraph
```

> [!NOTE]
> gem `azure_mgmt_resourcegraph`는 사용자 환경에 따라 설치되었을 수 있는 `ms_rest` 및 `ms_rest_azure`와 같은 종속성이 있습니다. 더 이상 필요하지 않은 경우에도 이러한 gem을 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph gem을 Ruby 환경에 추가하고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)
