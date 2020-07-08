---
title: 테스트 드라이브의 유형, Microsoft 상업적 marketplace
description: 상용 marketplace에서 테스트 드라이브 유형
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: dsindona
ms.openlocfilehash: a2d12a372f773a4a389ea7e7f2b3d2811ba889d3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85801226"
---
# <a name="azure-resource-manager-test-drive"></a>시험 드라이브 Azure Resource Manager

Azure Marketplace 또는 AppSource에 대 한 제안이 있지만 Azure 리소스만 사용 하 여 테스트 드라이브를 빌드하려고 하는 경우이 유형을 사용 합니다. ARM (Azure Resource Manager) 템플릿은 솔루션을 가장 잘 나타내도록 설계 된 Azure 리소스의 코딩 된 컨테이너입니다. 테스트 드라이브는 제공 된 ARM 템플릿을 사용 하 여 필요한 모든 리소스를 리소스 그룹에 배포 합니다. 이는 가상 머신 또는 Azure 앱 제안에 대 한 유일한 테스트 드라이브 옵션입니다.

ARM 템플릿이 무엇 인지 잘 모르는 경우 [Azure Resource Manager?](../azure-resource-manager/resource-group-overview.md) 을 읽고, [arm 템플릿의 구조와 구문을 이해](../azure-resource-manager/resource-group-authoring-templates.md) 하 여 고유한 템플릿을 빌드하고 테스트 하는 방법을 더 잘 이해 하세요.

**호스팅된** 또는 **논리 앱** 테스트 드라이브에 대 한 자세한 내용은 [테스트 드라이브인 무엇 인가요?](what-is-test-drive.md) 를 참조 하세요.

## <a name="technical-configuration"></a>기술 구성

배포 템플릿에는 솔루션을 구성 하는 모든 Azure 리소스가 포함 되어 있습니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. 파트너 센터에서 다음 속성을 설정 합니다.

- **지역**(필수) - 현재 시험 사용을 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 최상의 성능을 위해 일반적으로 고객 수가 가장 많은 지역에서 시험 사용을 사용할 수 있도록 하여 가장 가까운 지역을 선택할 수 있게 합니다. 구독에서 선택한 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인해야 합니다.

- **인스턴스** – 유형(핫 또는 콜드)을 선택하고, 제품을 사용할 수 있는 지역 수를 곱하여 사용 가능한 인스턴스 수를 선택합니다.

  - **핫** – 이 유형의 인스턴스는 배포된 후 선택한 지역별로 액세스를 기다립니다. 고객은 배포를 기다릴 필요 없이 시험 사용의 핫 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객이 전체 배포를 기다리지 않으므로 핫 인스턴스를 하나 이상 사용하는 것이 좋습니다. 핫 인스턴스를 사용할 수 없는 경우 고객 사용량이 줄어들게 됩니다.

  - **콜드** - 이 유형의 인스턴스는 지역별로 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 시험 사용을 요청할 때 배포할 전체 시험 사용 Resource Manager 템플릿이 필요하므로 콜드 인스턴스의 로드 속도는 핫 인스턴스보다 훨씬 느립니다. 단점은 시험 사용 기간 동안 비용을 지불하는 것이고 핫 인스턴스와 같이 Azure 구독에서 항상 실행되는 것은 아닙니다.

- **시험 사용 Azure Resource Manager 템플릿** – Azure Resource Manager 템플릿이 포함된 .zip 파일을 업로드합니다. 자세한 내용은 빠른 시작 문서 [Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)에서 Azure Resource Manager 템플릿을 만드는 방법을 참조하세요.

- **시험 사용 기간**(필수) – 시험 사용이 활성 상태로 유지되는 기간(시간)을 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 정수만 사용합니다(예: "2" 시간. "1.5"는 잘못됨).

## <a name="write-the-test-drive-template"></a>테스트 드라이브 템플릿 작성

원하는 리소스 패키지를 디자인 한 후에는 test drive ARM 템플릿을 작성 하 고 빌드합니다. 테스트 드라이브는 완전히 자동화 된 모드에서 배포를 실행 하기 때문에 테스트 드라이브 템플릿에 몇 가지 제한이 있습니다.

### <a name="parameters"></a>매개 변수

대부분의 템플릿에는 리소스 이름, 리소스 크기 (예: 저장소 계정 또는 가상 머신 크기 유형), 사용자 이름 및 암호, DNS 이름 등을 정의 하는 매개 변수 집합이 있습니다. Azure Portal을 사용하여 솔루션을 배포할 때 이러한 매개 변수를 모두 수동으로 채우고, 사용 가능한 DNS 이름 또는 스토리지 계정 이름을 선택할 수 있습니다.

![Azure Resource Manager의 매개 변수 목록](media/test-drive/resource-manager-parameters.png)

그러나 테스트 드라이브는 사용자 상호 작용 없이 자동으로 작동 하므로 제한 된 매개 변수 범주만 지원 합니다. 테스트 드라이브 ARM 템플릿의 매개 변수가 지원 되는 범주 중 하나에 속하지 않는 경우이 매개 변수를 변수 또는 상수 값으로 바꾸어야 합니다.

매개 변수에 유효한 이름을 사용할 수 있습니다. 테스트 드라이브는 메타 데이터 형식 값을 사용 하 여 매개 변수 범주를 인식 합니다. 모든 템플릿 매개 변수에 대해 메타 데이터 형식을 지정 합니다. 그렇지 않으면 템플릿이 유효성 검사를 통과 하지 않습니다.

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> 모든 매개 변수는 선택 사항 이므로 사용 하지 않으려는 경우에는 필요 하지 않습니다.

### <a name="accepted-parameter-metadata-types"></a>허용되는 매개 변수 메타데이터 유형

| 메타데이터 유형   | 매개 변수 유형  | 설명     | 샘플 값    |
|---|---|---|---|
| **baseuri**     | string          | 배포 패키지의 기본 URI| `https:\//\<\..\>.blob.core.windows.net/\<\..\>` |
| **이름**    | string          | 새 임의 사용자 이름입니다.| admin68876      |
| **password**    | 보안 문자열    | 새 임의 암호 | Lp!ACS\^2kh     |
| **세션 id**   | string          | 고유 테스트 드라이브 세션 ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

이 매개 변수를 사용 하 여 패키지에 포함 된 모든 파일의 Uri를 생성할 수 있도록 드라이브 테스트는 배포 패키지의 **기본 Uri** 를 사용 하 여이 매개 변수를 초기화 합니다.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

이 매개 변수를 템플릿 내에서 사용 하 여 테스트 드라이브 배포 패키지에서 파일의 Uri를 생성 합니다. 다음 예제에서는 연결 된 템플릿의 Uri를 생성 하는 방법을 보여 줍니다.

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>사용자 이름

테스트 드라이브는 새 임의 사용자 이름으로이 매개 변수를 초기화 합니다.

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

샘플 값:`admin68876`

솔루션에 임의 또는 상수 사용자 이름을 사용할 수 있습니다.

#### <a name="password"></a>password

시험 사용은 새 임의의 암호를 사용 하 여이 매개 변수를 초기화 합니다.

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

샘플 값:`Lp!ACS^2kh`

솔루션에 임의 또는 상수 암호를 사용할 수 있습니다.

#### <a name="session-id"></a>세션 ID

시험 사용에서 테스트 드라이브 세션 ID를 나타내는 고유 GUID를 사용 하 여이 매개 변수를 초기화 합니다.

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

샘플 값:`b8c8693e-5673-449c-badd-257a405a6dee`

이 매개 변수를 사용 하 여 필요한 경우 테스트 드라이브 세션을 고유 하 게 식별할 수 있습니다.

### <a name="unique-names"></a>고유 이름

스토리지 계정 또는 DNS 이름과 같은 일부 Azure 리소스에는 전역적으로 고유한 이름이 필요합니다. 즉, 테스트 드라이브가 ARM 템플릿을 배포할 때마다 모든 해당 리소스에 대 한 고유한 이름으로 새 리소스 그룹을 만듭니다. 따라서 리소스 그룹 Id에 대 한 변수 이름과 연결 된 [uniquestring](../azure-resource-manager/templates/template-functions.md) 함수를 사용 하 여 임의의 고유 값을 생성 해야 합니다.

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

`contosovm` `resourceGroup().id` 각 변수의 고유성이 보장 되 고 안정성을 보장 하기 때문에 매개 변수/변수 문자열 ()을 고유한 문자열 출력 ()과 연결 해야 합니다.

예를 들어, 대부분의 리소스 이름은 숫자로 시작할 수 없지만, 고유한 문자열 함수는 숫자로 시작하는 문자열을 반환할 수 있습니다. 따라서 원시 고유 문자열 출력을 사용하는 경우 배포에 실패합니다.

[이 문서](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)에서 리소스 명명 규칙 및 제한 사항에 대한 추가 정보를 확인할 수 있습니다.

### <a name="deployment-location"></a>배포 위치

다른 Azure 지역에서 테스트 드라이브를 사용할 수 있도록 설정할 수 있습니다. 사용자가 최상의 사용자 경험을 제공하는 가장 가까운 지역을 선택하도록 허용하면 됩니다.

테스트 드라이브가 랩의 인스턴스를 만들 때 항상 사용자가 선택한 지역에 리소스 그룹을 만든 다음이 그룹 컨텍스트에서 배포 템플릿을 실행 합니다. 따라서 템플릿은 리소스 그룹에서 배포 위치를 선택해야 합니다.

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

그런 다음 이 위치를 특정 랩 인스턴스의 모든 리소스에 대해 사용합니다.

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

구독에서 선택한 각 지역에 원하는 모든 리소스를 배포할 수 있는지 확인 합니다. 또한 사용할 모든 지역에서 가상 머신 이미지를 사용할 수 있는지 확인 하세요. 그렇지 않으면 일부 지역에서 배포 템플릿이 작동 하지 않습니다.

### <a name="outputs"></a>출력

일반적으로 리소스 관리자 템플릿을 사용 하 여 출력을 생성 하지 않고 배포할 수 있습니다. 이는 템플릿 매개 변수를 채우는 데 사용하는 모든 값을 알고 있으며 항상 리소스의 속성을 수동으로 검사할 수 있기 때문입니다.

그러나 시험 사용 리소스 관리자 템플릿의 경우 랩에 액세스 하는 데 필요한 모든 정보 (웹 사이트 Uri, 가상 컴퓨터 호스트 이름, 사용자 이름 및 암호)를 시험 사용 하려면를 반환 하는 것이 중요 합니다. 이러한 변수가 고객에 게 표시 되므로 모든 출력 이름을 읽을 수 있는지 확인 합니다.

템플릿 출력과 관련된 제한 사항은 없습니다. 시험 사용에서 모든 출력 값을 문자열로 변환 하므로 개체를 출력으로 보내면 사용자에 게 JSON 문자열이 표시 됩니다.

예:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>구독 한도

구독 및 서비스 제한에 대해 잊지 마세요. 예를 들어 최대 4 코어 가상 머신을 배포 하려는 경우 랩에 사용 하는 구독을 통해 40 코어를 사용할 수 있는지 확인 해야 합니다. Azure 구독 및 서비스 제한에 대 한 자세한 내용은 [azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요. 동시에 여러 테스트 드라이브를 사용할 수 있으므로 구독에서 사용할 수 있는 총 동시 테스트 드라이브 수를 곱한 코어 수를 처리할 수 있는지 확인 합니다.

### <a name="what-to-upload"></a>업로드할 사항

시험 드라이브 ARM 템플릿은 다양 한 배포 아티팩트를 포함할 수 있지만 라는 하나의 파일이 있어야 하는 zip 파일로 업로드 됩니다 `main-template.json` . 테스트 드라이브에서 랩을 인스턴스화하는 데 사용 하는 ARM 배포 템플릿입니다. 이 파일 외에 추가 리소스가 있는 경우 템플릿 내에서 외부 리소스로 참조 하거나 zip 파일에 포함할 수 있습니다.

게시 인증 중에 테스트 드라이브는 배포 패키지를 풀고 하 고 내부 테스트 드라이브 blob 컨테이너에 콘텐츠를 넣습니다. 컨테이너 구조는 배포 패키지의 구조를 반영합니다.

| package.zip                       | 테스트 드라이브 blob 컨테이너         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

이 Blob 컨테이너의 URI를 기본 URI라고 합니다. 랩의 모든 수정 버전에는 자체 blob 컨테이너가 있으므로 랩의 모든 수정 버전에는 고유한 기본 Uri가 있습니다. 테스트 드라이브는 압축을 푼 배포 패키지의 기본 Uri를 템플릿 매개 변수를 통해 템플릿에 전달할 수 있습니다.

### <a name="transform-template-examples-for-test-drive"></a>테스트 드라이브의 변환 템플릿 예제

리소스의 아키텍처를 테스트 드라이브 리소스 관리자 템플릿으로 전환 하는 프로세스는 어려울 수 있습니다. 추가 도움말 [은 테스트 드라이브인?](what-is-test-drive.md#transforming-examples)에서 현재 배포 템플릿을 가장 잘 변환 하는 방법의 예제를 참조 하세요.

## <a name="test-drive-deployment-subscription-details"></a>테스트 드라이브 배포 구독 세부 정보

완료 하는 마지막 섹션은 Azure 구독 및 AD (Azure Active Directory)를 연결 하 여 자동으로 테스트 드라이브를 배포할 수 있도록 하는 것입니다.

![테스트 드라이브 배포 구독 세부 정보](media/test-drive/deployment-subscription-details.png)

1. **Azure 구독 ID**를 가져옵니다. Azure 서비스 및 Azure Portal 액세스 권한을 부여합니다. 해당 구독에서 리소스 사용량이 보고되며 서비스 대금이 청구됩니다. 테스트 드라이브만을 위한 별도의 Azure 구독을 보유 하 고 있지 않은 경우 하나를 만드세요. `1a83645ac-1234-5ab6-6789-1h234g764ghty1`Azure Portal에 로그인 하 고 왼쪽 탐색 메뉴에서 **구독** 을 선택 하 여 Azure 구독 id (예:)를 찾을 수 있습니다.

   ![Azure 구독](media/test-drive/azure-subscriptions.png)

2. **AZURE AD 테 넌 트 ID**를 가져옵니다. 사용 가능한 테 넌 트 id가 이미 있는 경우 **Azure Active Directory**  >  **속성**  >  **디렉터리 id**에서 찾을 수 있습니다.

   ![Azure Active Directory 속성](media/test-drive/azure-active-directory-properties.png)

   테 넌 트 ID가 없는 경우 Azure Active Directory에서 새 테 넌 트 ID를 만듭니다. 테 넌 트 설정에 대 한 도움말은 [빠른 시작: 테 넌 트 설정](../active-directory/develop/quickstart-create-new-tenant.md)을 참조 하세요.

3. **AZURE AD 앱 ID** – 새 응용 프로그램을 만들고 등록 합니다. 이 응용 프로그램을 사용 하 여 테스트 드라이브 인스턴스에서 작업을 수행 합니다.

   1. 새로 만든 디렉터리 또는 기존 디렉터리로 이동 하 고 필터 창에서 Azure Active Directory을 선택 합니다.
   2. **앱 등록** 검색 하 고 **추가**를 선택 합니다.
   3. 애플리케이션 이름을 입력합니다.
   4. **웹 앱/** a p i의 **유형을** 선택 합니다.
   5. 로그온 URL에 값을 제공 합니다 .이 필드는 사용 되지 않습니다.
   6. **만들기**를 선택합니다.
   7. 응용 프로그램을 만든 후에는 **속성**을 선택 하  >  고**응용 프로그램을 다중 테 넌 트로 설정한** 다음 **저장**을 선택 합니다.

4. **저장**을 선택합니다.

5. 이 등록 된 앱의 응용 프로그램 ID를 복사 하 여 test drive 필드에 붙여넣습니다.

   ![Azure AD 애플리케이션 ID 세부 정보](media/test-drive/azure-ad-application-id-detail.png)

6. 응용 프로그램을 사용 하 여 구독에 배포 하므로 구독에서 응용 프로그램을 참가자로 추가 해야 합니다.

   1. 테스트 드라이브에 사용할 **구독** 유형을 선택 합니다.
   1. **액세스 제어(IAM)** 를 선택합니다.
   1. **역할** 할당 탭을 선택한 다음 **역할 할당 추가**를 선택 합니다.

      ![새 Access Control 보안 주체를 추가합니다.](media/test-drive/access-control-principal.jpg)

   1. 다음과 같이 **역할** 을 설정 하 고에 대 한 **액세스 권한을 할당** 합니다. **선택** 필드에서 Azure AD 응용 프로그램의 이름을 입력 합니다. **참가자** 역할을 할당 하려는 응용 프로그램을 선택 합니다.

      ![사용 권한을 추가합니다.](media/test-drive/access-control-permissions.jpg)

   1. **저장**을 선택합니다.

7. **Azure AD 앱** 인증 키를 생성 합니다. 키 **아래에서** **키 설명을**추가 하 고, 기간을 **만료 되지 않도록** 설정 합니다. 만료 된 키가 프로덕션에서 테스트 드라이브를 중단 하 고 **저장**을 선택 합니다. 이 값을 복사 하 여 필요한 테스트 드라이브 필드에 붙여넣습니다.

![Azure AD 애플리케이션에 대한 키를 표시합니다.](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>다시 게시

이제 모든 테스트 드라이브 필드가 완료 되었으므로 제품을 다시 **게시** 합니다. 시험 드라이브가 인증을 통과 하면 제품 미리 보기에서 고객 환경을 테스트 합니다.

1. UI에서 테스트 드라이브를 시작 합니다.
1. Azure Portal 내에서 Azure 구독을 엽니다.
1. 테스트 드라이브가 올바르게 배포 되는지 확인 합니다.

   ![Azure portal](media/test-drive/azure-portal.png)

고객에 대해 프로 비전 된 테스트 드라이브 인스턴스를 삭제 하지 마세요. 사용자가 작업을 완료 한 후에는 test drive 서비스에서 이러한 리소스 그룹을 자동으로 정리 합니다.

미리 보기 기능을 사용 하는 데 익숙해지면 **실시간으로 이동할**수 있습니다. 전체 종단 간 환경을 두 번 확인 하는 최종 검토 프로세스가 있습니다. 제품을 거부 하는 경우 수정 해야 하는 사항을 설명 하는 제품에 대 한 엔지니어링 담당자에 게 전자 메일을 보냅니다.

## <a name="next-steps"></a>다음 단계

- 파트너 센터에서 제품을 만드는 방법에 대 한 지침을 수행 하는 경우 뒤로 화살표를 사용 하 여 해당 항목으로 돌아옵니다.
- [시험 드라이브용 이란?](what-is-test-drive.md)에서 다른 종류의 테스트 드라이브에 대해 자세히 알아보세요.
