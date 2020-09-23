---
title: GitHub Actions에서 Key Vault를 사용하여 Azure Spring Cloud 인증
description: GitHub 작업을 사용 하 여 Azure 스프링 클라우드의 CI/CD 워크플로와 함께 key vault를 사용 하는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java
ms.openlocfilehash: 995d10b3c7064e462500e0bec4d5d8aa010afe64
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888779"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>GitHub Actions에서 Key Vault를 사용하여 Azure Spring Cloud 인증

이 문서는 ✔️ Java ✔️ C **에 적용 됩니다.** #

Key vault는 키를 저장할 수 있는 안전한 장소입니다. 기업 사용자는 CI/CD 환경의 자격 증명을 제어 하는 범위 내에 저장 해야 합니다. 키 자격 증명 모음에서 자격 증명을 가져오는 키는 리소스 범위로 제한 되어야 합니다.  전체 Azure 범위가 아니라 key vault 범위에만 액세스할 수 있습니다. 이는 건물의 모든 도어를 열 수 있는 마스터 키가 아닌 강력한 상자를 열 수 있는 키와 같습니다. CICD 워크플로에서 유용한 다른 키를 사용 하 여 키를 가져오는 방법입니다. 

## <a name="generate-credential"></a>자격 증명 생성
키 자격 증명 모음에 액세스 하는 키를 생성 하려면 로컬 컴퓨터에서 아래 명령을 실행 합니다.
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
매개 변수로 지정 된 범위는 `--scopes` 리소스에 대 한 키 액세스를 제한 합니다.  강력한 상자에만 액세스할 수 있습니다.

결과 포함:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
그런 다음 [github 리포지토리 설정 및 Azure로 인증](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)에 설명 된 대로 github **비밀** 에 결과를 저장 합니다.

## <a name="add-access-policies-for-the-credential"></a>자격 증명에 대 한 액세스 정책 추가
위에서 만든 자격 증명은 저장 된 내용이 아니라 Key Vault에 대 한 일반 정보만 가져올 수 있습니다.  Key Vault에 저장 된 암호를 가져오려면 자격 증명에 대 한 액세스 정책을 설정 해야 합니다.

Azure Portal에서 **Key Vault** 대시보드로 이동 하 고 **Access control** 메뉴를 클릭 한 다음 **역할 할당** 탭을 엽니다. **유형** 및 범위에 대해 **앱** 을 선택 `This resource` 합니다. **scope**  이전 단계에서 만든 자격 증명이 표시 됩니다.

 ![액세스 정책 설정](./media/github-actions/key-vault1.png)

자격 증명 이름을 복사 합니다 (예:) `azure-cli-2020-01-19-04-39-02` . **액세스 정책** 메뉴를 열고 **+ 액세스 정책 추가** 링크를 클릭 합니다.  `Secret Management` **템플릿**에 대해를 선택 하 고 **보안 주체**를 선택 합니다. **보안 주체** / **선택** 입력 상자에 자격 증명 이름 붙여넣기:

 ![선택](./media/github-actions/key-vault2.png)

 **액세스 정책 추가** 대화 상자에서 **추가** 단추를 클릭 한 다음 **저장**을 클릭 합니다.

## <a name="generate-full-scope-azure-credential"></a>전체 범위 Azure 자격 증명 생성
빌딩의 모든 문을 여는 마스터 키입니다. 절차는 이전 단계와 유사 하지만 여기서는 마스터 키를 생성 하는 범위를 변경 합니다.

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth
```

다시, 결과:
```
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
    "resourceManagerEndpointUrl": "https://management.azure.com/",
    "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
    "galleryEndpointUrl": "https://gallery.azure.com/",
    "managementEndpointUrl": "https://management.core.windows.net/"
}
```
전체 JSON 문자열을 복사 합니다.  **Key Vault** 대시보드로 다시 Bo. **비밀** 메뉴를 열고 **생성/가져오기** 단추를 클릭 합니다. 암호 이름 (예:)을 입력 `AZURE-CREDENTIALS-FOR-SPRING` 합니다. JSON 자격 증명 문자열을 **값** 입력 상자에 붙여넣습니다. 값 입력 상자는 여러 줄 텍스트 영역이 아니라 한 줄 텍스트 필드 임을 알 수 있습니다.  여기에 전체 JSON 문자열을 붙여 넣을 수 있습니다.

 ![전체 범위 자격 증명](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>GitHub 작업에서 자격 증명 결합
CICD 파이프라인이 실행 될 때 사용 되는 자격 증명을 설정 합니다.

```
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}           # Strong box key you generated in the first step
    - uses: Azure/get-keyvault-secrets@v1.0
      with:
        keyvault: "<Your Key Vault Name>"
        secrets: "AZURE-CREDENTIALS-FOR-SPRING"           # Master key to open all doors in the building
      id: keyvaultaction
    - uses: azure/login@v1
      with:
        creds: ${{ steps.keyvaultaction.outputs.AZURE-CREDENTIALS-FOR-SPRING }}
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud             # Spring CLI commands from here
          az spring-cloud list

```

## <a name="next-steps"></a>다음 단계
* [스프링 클라우드 GitHub 작업](./spring-cloud-howto-github-actions.md)
