---
title: '빠른 시작: JavaScript를 사용하여 새 정책 할당'
description: 이 빠른 시작에서는 JavaScript를 사용하여 비규격 리소스를 식별하는 Azure Policy 할당을 만듭니다.
ms.date: 09/24/2020
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 7548053e4bd5be214bf7de3eef3dc4c6c95442d4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348172"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-javascript"></a>빠른 시작: JavaScript를 사용하여 비규격 리소스를 식별하는 정책 할당 만들기

Azure의 규정 준수를 이해하는 첫 번째 단계는 리소스 상태를 식별하는 것입니다. 이 빠른 시작에서는 관리 디스크를 사용하지 않는 가상 머신을 식별하는 정책 할당을 만듭니다. 완료되면 _비준수_ 가상 머신을 식별하게 됩니다.

JavaScript 라이브러리는 명령줄 또는 스크립트에서 Azure 리소스를 관리하는 데 사용됩니다. 이 가이드에서는 JavaScript 라이브러리를 사용하여 정책 할당을 만드는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

- **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- **Node.js**: [Node.js](https://nodejs.org/) 버전 12 이상이 필요합니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-libraries"></a>정책 라이브러리 추가

JavaScript가 Azure Policy와 작업할 수 있도록 하려면 라이브러리를 추가해야 합니다. 이러한 라이브러리는 [Windows 10의 bash](/windows/wsl/install-win10)를 비롯하여 JavaScript를 사용할 수 있는 모든 위치에서 작동합니다.

1. 다음 명령을 실행하여 새 Node.js 프로젝트를 설정합니다.

   ```bash
   npm init -y
   ```

1. yargs 라이브러리에 참조를 추가합니다.

   ```bash
   npm install yargs
   ```

1. Azure Policy 라이브러리에 대한 참조를 추가합니다.

   ```bash
   # arm-policy is for working with Azure Policy objects such as definitions and assignments
   npm install @azure/arm-policy

   # arm-policyinsights is for working with Azure Policy compliance data such as events and states
   npm install @azure/arm-policyinsights
   ```

1. Azure 인증 라이브러리에 참조를 추가합니다.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > _package.json_ `@azure/arm-policy`가 버전 **3.1.0** 이상이고 `@azure/arm-policyinsights`가 버전 **3.2.0** 이상이고, `@azure/ms-rest-nodeauth`가 버전 **3.0.5** 이상인지 확인합니다.

## <a name="create-a-policy-assignment"></a>정책 할당 만들기

이 빠른 시작에서는 정책 할당을 만들고 **관리 디스크를 사용하지 않는 VM 감사**(`06a78e20-9358-41c9-923c-fb736d382a4d`) 정의를 할당합니다. 이 정책 정의는 정책 정의에 설정된 조건을 준수하지 않는 리소스를 식별합니다.

1. _policyAssignment.js_라는 새 파일을 만들고 다음 코드를 입력합니다.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyObjects = require("@azure/arm-policy");
   
   if (argv.subID && argv.name && argv.displayName && argv.policyDefID && argv.scope && argv.description) {
   
       const createAssignment = async () => {
           const credentials = await authenticator.interactiveLogin();
           const client = new policyObjects.PolicyClient(credentials, argv.subID);
           const assignments = new policyObjects.PolicyAssignments(client);
   
           const result = await assignments.create(
               argv.scope,
               argv.name,
               {
                   displayName: argv.displayName,
                   policyDefinitionId: argv.policyDefID,
                   description: argv.description
               }
           );
           console.log(result);
       };
   
       createAssignment();
   }
   ```

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   node policyAssignment.js `
      --subID "{subscriptionId}" `
      --name "audit-vm-manageddisks" `
      --displayName "Audit VMs without managed disks Assignment" `
      --policyDefID "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d" `
      --description "Shows all virtual machines not using managed disks" `
      --scope "{scope}"
   ```

이 명령은 다음 정보를 사용합니다.

- **subID** - 인증 컨텍스트에 대한 구독 ID입니다. `{subscriptionId}`를 사용자의 구독으로 바꾸어야 합니다.
- **name** – 정책 할당 개체의 고유한 이름입니다. 위의 예제에서는 _audit-vm-manageddisks_를 사용합니다.
- **displayName** - 정책 할당의 표시 이름입니다. 이 예제에서는 ‘관리 디스크 할당이 없는 VM 감사’를 사용합니다.
- **policyDefID** – 할당을 만드는 데 기준으로 사용되는 정책 정의 경로입니다. 이 예제에서는 ‘관리 디스크를 사용하지 않는 VM 감사’ 정책 정의의 ID입니다.
- **설명** - 정책에서 수행하는 작업 또는 이 범위에 할당된 이유에 대한 자세한 설명입니다.
- **범위** - 범위는 정책 할당이 적용되는 리소스 또는 리소스 그룹을 결정합니다. 관리 그룹에서 개별 리소스에 이르기까지 다양할 수 있습니다. `{scope}`를 다음 패턴 중 하나로 바꾸어야 합니다.
  - 관리 그룹: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - 구독: `/subscriptions/{subscriptionId}`
  - 리소스 그룹: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - 리소스: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

이제 비규격 리소스를 식별하여 환경의 규정 준수 상태를 파악할 준비가 되었습니다.

## <a name="identify-non-compliant-resources"></a>비규격 리소스 식별

이제 정책 할당이 생성되었으므로 미준수 리소스를 식별할 수 있습니다.

1. _policyState.js_라는 새 파일을 만들고 다음 코드를 입력합니다.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const policyInsights = require("@azure/arm-policyinsights");
   
   if (argv.subID && argv.name) {
   
       const getStates = async () => {
   
           const credentials = await authenticator.interactiveLogin();
           const client = new policyInsights.PolicyInsightsClient(credentials);
           const policyStates = new policyInsights.PolicyStates(client);
           const result = await policyStates.listQueryResultsForSubscription(
               "latest",
               argv.subID,
               {
                   queryOptions: {
                       filter: "IsCompliant eq false and PolicyAssignmentId eq '" + argv.name + "'",
                       apply: "groupby((ResourceId))"
                   }
               }
           );
           console.log(result);
       };
   
       getStates();
   }
   ```

1. 터미널에서 다음 명령을 입력합니다.

   ```bash
   node policyState.js --subID "{subscriptionId}" --name "audit-vm-manageddisks"
   ```

`{subscriptionId}`를 이전 단계에서 만든 'audit-vm-manageddisks'라는 정책 할당에 대한 호환성 결과를 보려는 구독으로 바꿉니다. 다른 범위와 데이터를 요약하는 방법 목록은 [PolicyStates*](/javascript/api/@azure/arm-policyinsights/) 메서드를 참조하세요.

결과는 다음 예제와 유사합니다.

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

결과는 Azure Portal 보기에서 정책 할당의 **리소스 규정 준수** 탭에 보이는 것과 일치합니다.

## <a name="clean-up-resources"></a>리소스 정리

- 포털을 통해 _관리 디스크 할당을 사용하지 않는 VM 감사_ 정책 할당을 삭제합니다. 이 정책 정의는 기본 제공되는 정의이므로 제거할 정의가 없습니다.

- 애플리케이션에서 설치된 라이브러리를 제거하려는 경우 다음 명령을 실행합니다.

  ```bash
  npm uninstall @azure/arm-policy @azure/arm-policyinsights @azure/ms-rest-nodeauth yargs
  ```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure 환경에서 규정 비준수 리소스를 식별하는 정책 정의를 할당했습니다.

새 리소스가 규정을 준수하는지 확인하기 위해 정책 정의를 할당하는 방법에 대해 자세히 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [정책 만들기 및 관리](./tutorials/create-and-manage.md)