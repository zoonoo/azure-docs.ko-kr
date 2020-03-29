---
title: GitHub 작업에서 키 볼트로 Azure 스프링 클라우드 인증
description: GitHub 작업을 사용 하 여 Azure 스프링 클라우드에 대 한 CI/CD 워크플로와 키 볼트를 사용 하는 방법
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/20/2019
ms.openlocfilehash: 78cd5945e394219be0551bbe97afef07f18b61f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945479"
---
# <a name="authenticate-azure-spring-cloud-with-key-vault-in-github-actions"></a>GitHub 작업에서 키 볼트로 Azure 스프링 클라우드 인증
키 볼트는 키를 보관할 수 있는 안전한 장소입니다. 엔터프라이즈 사용자는 CI/CD 환경에 대한 자격 증명을 제어하는 범위에 저장해야 합니다. 키 자격 증명에서 자격 증명을 얻는 키는 리소스 범위로 제한되어야 합니다.  전체 Azure 범위가 아닌 키 자격 증명 모음 범위에만 액세스할 수 있습니다. 그것은 단지 건물의 모든 문을 열 수있는 마스터 키가 아닌 강한 상자를 열 수있는 키처럼. CICD 워크플로우에 유용한 다른 키로 키를 얻는 방법입니다. 

## <a name="generate-credential"></a>자격 증명 생성
키 자격 증명 모음에 액세스하는 키를 생성하려면 로컬 컴퓨터에서 아래 명령을 실행합니다.
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.KeyVault/vaults/<KEY_VAULT> --sdk-auth
```
매개 변수에 `--scopes` 의해 지정된 범위는 리소스에 대한 키 액세스를 제한합니다.  그것은 단지 강한 상자에 액세스 할 수 있습니다.

결과:
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
그런 다음 [GitHub 리포지토리 설정에](./spring-cloud-howto-github-actions.md#set-up-github-repository-and-authenticate)설명된 대로 GitHub **암호에** 결과를 저장하고 Azure를 사용하여 인증합니다.

## <a name="add-access-policies-for-the-credential"></a>자격 증명에 대한 액세스 정책 추가
위에서 만든 자격 증명은 키 자격 증명에 대한 일반 정보만 얻을 수 있으며 저장하는 콘텐츠는 얻을 수 없습니다.  키 볼트에 저장된 비밀을 얻으려면 자격 증명에 대한 설정 된 액세스 정책이 필요합니다.

Azure 포털의 **키 볼트** 대시보드로 이동하여 액세스 **제어** 메뉴를 클릭한 다음 **Apps** 역할 **Type** **할당** **scope**탭을 엽니다. `This resource`  이전 단계에서 만든 자격 증명이 표시됩니다.

 ![액세스 정책 설정](./media/github-actions/key-vault1.png)

자격 증명 이름을 복사합니다(예: `azure-cli-2020-01-19-04-39-02`. 액세스 **정책** 메뉴를 열고 **+Access 정책** 링크를 클릭합니다.  `Secret Management` **템플릿을**선택한 다음 **보안 주체를**선택합니다. **보안 주체**/**선택** 입력 상자에 자격 증명 이름을 붙여 넣습니다.

 ![선택](./media/github-actions/key-vault2.png)

 **액세스 추가 정책** 대화 상자에서 **추가** 단추를 클릭한 다음 에서 **추가 를**클릭합니다.

## <a name="generate-full-scope-azure-credential"></a>전체 범위 Azure 자격 증명 생성
이것은 건물의 모든 문을 여는 마스터 키입니다. 프로시저는 이전 단계와 유사하지만 여기서는 마스터 키를 생성하기 위해 범위를 변경합니다.

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
전체 JSON 문자열을 복사합니다.  보 다시 **키 볼트** 대시보드에. **비밀** 메뉴를 연 다음 **생성/가져오기** 단추를 클릭합니다. 와 같은 `AZURE-CRENDENTIALS-FOR-SPRING`비밀 이름을 입력합니다. JSON 자격 증명 문자열을 **값** 입력 상자에 붙여 넣습니다. 값 입력 상자는 여러 줄텍스트 영역이 아니라 한 줄텍스트 필드임을 알 수 있습니다.  전체 JSON 문자열을 붙여 칠 수 있습니다.

 ![전체 범위 자격 증명](./media/github-actions/key-vault3.png)

## <a name="combine-credentials-in-github-actions"></a>GitHub 작업에서 자격 증명 결합
CICD 파이프라인이 실행될 때 사용되는 자격 증명을 설정합니다.

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
        keyvault: "zlhe-test"
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