---
title: Azure에서 Cloud Foundry 만들기
description: Azure에서 Cloud Foundry PCF 클러스터를 프로비전하는 데 필요한 매개 변수를 설정하는 방법을 알아봅니다.
services: Cloud Foundry
documentationcenter: CloudFoundry
author: ruyakubu
manager: brunoborges
editor: ruyakubu
ms.assetid: ''
ms.author: ruyakubu
ms.date: 09/13/2018
ms.devlang: ''
ms.service: Cloud Foundry
ms.tgt_pltfrm: multiple
ms.topic: tutorial
ms.workload: web
ms.openlocfilehash: a0a3379a8a2579080d9b686917395feec9cf8f3d
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48250624"
---
# <a name="create-cloud-foundry-on-azure"></a>Azure에서 Cloud Foundry 만들기

이 자습서에는 Azure에서 Pivotal Cloud Foundry PCF 클러스터를 프로비전하는 데 필요한 매개 변수를 만들고 생성하는 신속한 단계가 제공됩니다.  Pivotal Cloud Foundry 솔루션은 Azure [MarketPlace](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry)에서 검색을 수행하여 찾을 수 있습니다.

![대체 이미지 텍스트](media/deploy/pcf-marketplace.png "Azure에서 Pivotal Cloud Foundry 검색")


## <a name="generate-an-ssh-public-key"></a>SSH 공개 키 생성

Windows, Mac 또는 Linux를 사용하여 공용 SSH 키를 생성하는 방법은 여러 가지입니다.

```Bash
ssh-keygen -t rsa -b 2048
```
- 환경에 대한 [지침]( https://docs.microsoft.com/azure/virtual-machines/linux/ssh-from-windows)을 보려면 여기를 클릭하십시오.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

> [!NOTE]
>
> 서비스 주체를 만들려면 소유자 계정 권한이 필요합니다.  서비스 주체 만들기를 자동화하도록 스크립트를 작성할 수도 있습니다. 예를 들어 Azure CLI [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest)를 사용하면 됩니다.

1. Azure 계정에 로그인합니다.

    `az login`

    ![대체 이미지 텍스트](media/deploy/az-login-output.png "Azure CLI 로그인")
 
    “id” 값을 복사해둡니다. **구독 ID**와 **tenantId** 값을 나중에 사용됩니다.

2. 이 구성에 대한 기본 구독을 설정합니다.

    `az account set -s {id}`

3. PCF에 대한 AAD 응용 프로그램을 만들고 고유한 영숫자 암호를 지정합니다.  **clientSecret**은 나중에 사용되므로 암호를 저장해둡니다.

    `az ad app create --display-name "Svc Prinicipal for OpsManager" --password {enter-your-password} --homepage "{enter-your-homepage}" --identifier-uris {enter-your-homepage}`

    **ClientID**는 나중에 사용되므로 출력의 “appId” 값을 복사해둡니다.

    > [!NOTE]
    >
    > 자체적인 응용 프로그램 홈페이지와 식별자 URI를 선택합니다.  예: http://www.contoso.com.

4. 새로운 “appId”로 서비스 주체를 만듭니다.

    `az ad sp create --id {appId}`

5. 서비스 주체의 권한 역할을 **기여자**로 설정합니다.

    `az role assignment create --assignee “{enter-your-homepage}” --role “Contributor” `

    또는 다음을 사용할 수도 있습니다.

    `az role assignment create --assignee {service-princ-name} --role “Contributor” `

    ![대체 이미지 텍스트](media/deploy/svc-princ.png "서비스 주체 역할 할당")

6. appId, 암호 및 tenantId를 사용하여 서비스 주체에 성공적으로 로그인할 수 있는지 확인합니다.

    `az login --service-principal -u {appId} -p {your-passward}  --tenant {tenantId}`

7. 위에서 복사해둔 모든 **구독 ID**, **tenantId**, **clientID** 및 **clientSecret** 값을 사용하여 다음 형식으로 .json 파일을 만듭니다.  파일을 저장합니다.

    ```json
    {
        "subscriptionID": "{enter-your-subscription-Id-here}",
        "tenantID": "{enter-your-tenant-Id-here}",
        "clientID": "{enter-your-app-Id-here}",
        "clientSecret": "{enter-your-key-here}"
    }
    ```

## <a name="get-the-pivotal-network-token"></a>Pivotal 네트워크 토큰 구하기

1. [Pivotal 네트워크](https://network.pivotal.io) 계정을 등록하거나 로그인합니다.
2. 페이지 오른쪽 상단의 프로필 이름을 클릭하고 **프로필 편집”을 선택합니다.
3. 페이지 하단으로 스크롤하여 **레거시 API 토큰** 값을 복사합니다.  이 값은 나중에 사용될 **Pivotal 네트워크 토큰** 값입니다.

## <a name="provision-your-cloud-foundry-on-azure"></a>Azure에서 Cloud Foundry 프로비전

1. 이제 [Azure에서 Pivotal Cloud Foundry](https://azuremarketplace.microsoft.com/marketplace/apps/pivotal.pivotal-cloud-foundry) 클러스터를 프로비전하는 데 필요한 모든 매개 변수가 준비되었습니다.
2. 매개 변수를 입력하고 PCF 클러스터를 만듭니다.

## <a name="verify-the-deployment-and-log-into-the-pivotal-ops-manager"></a>배포를 확인하고 Pivotal Ops Manager에 로그인합니다.

1. PCF 클러스터에 배포 상태가 표시됩니다.

    ![대체 이미지 텍스트](media/deploy/deployment.png "Azure 배포 상태")

2. 왼쪽 탐색에서 **배포** 링크를 클릭하여 PCF Ops Manager에 대한 자격 증명을 구한 다음, 다음 페이지에서 **배포 이름**을 클릭합니다.
3. 왼쪽 탐색에서 **출력** 링크를 클릭하여 PCF Ops Manager에 URL, 사용자 이름 및 암호를 표시합니다.  “OPSMAN-FQDN” 값은 URL입니다.
 
    ![대체 이미지 텍스트](media/deploy/deploy-outputs.png "Cloud Foundry 배포 출력")
 
4. 웹 브라우저에서 URL을 시작하고 이전 단계에서 구한 자격 증명을 입력하여 로그인합니다.

    ![대체 이미지 텍스트](media/deploy/pivotal-login.png "Pivotal 로그인 페이지")
         
    > [!NOTE]
    >
    > 사이트가 안전하지 않다는 경고 메시지로 인해 Internet Explorer 브라우저에 오류가 발생하면 [자세한 정보]를 클릭하고 웹 페이지로 이동합니다.  Firefox에서는 Advance(고급)을 클릭하고 인증서를 추가하여 진행합니다.

5. PCF Ops Manager에 배포된 Azure 인스턴스가 표시됩니다. 이제 여기에서 응용 프로그램을 배포하고 관리할 수 있습니다.
               
    ![대체 이미지 텍스트](media/deploy/ops-mgr.png "Pivotal에 배포된 Azure 인스턴스")
 
