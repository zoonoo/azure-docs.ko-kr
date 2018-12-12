---
title: Python을 사용하여 Azure Databricks에서 Azure Data Explorer에 연결
description: 이 항목에서는 Azure Databricks에서 Python 라이브러리를 사용하여 두 가지 인증 방법 중 하나로 ADX(Azure Data Explorer)에서 데이터에 액세스하는 방법을 보여 줍니다.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52429928"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Python을 사용하여 Azure Databricks에서 Azure Data Explorer에 연결

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks)는 Microsoft Azure Cloud Services 플랫폼에 대해 최적화된 Apache Spark 기반 분석 플랫폼입니다. 이 문서에서는 Azure Databricks에서 Python 라이브러리를 사용하여 ADX(Azure Data Explorer)에서 데이터에 액세스하는 방법을 보여 줍니다. 디바이스 로그인 및 Azure AD(Azure Active Directory) 앱을 포함하여 ADX로 인증하는 방법에는 여러 가지가 있습니다.

## <a name="prerequisites"></a>필수 조건

- [Azure Data Explorer 클러스터 및 데이터베이스 만들기](/azure/data-explorer/create-cluster-database-portal)
- [Azure Databricks 작업 영역 만들기](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    **Azure Databricks 서비스** 아래 **가격 책정 계층** 드롭다운에서 **프리미엄**을 선택합니다. 이렇게 하면 Azure Databricks 비밀을 사용하여 자격 증명을 저장하고 Notebook 및 작업에서 이를 참조할 수 있습니다.

- 다음 사양(샘플 Notebook을 실행하는 데 필요한 최소 설정)을 사용하여 Azure Databricks에서 [클러스터](https://docs.azuredatabricks.net/user-guide/clusters/create.html)를 만듭니다.

![클러스터 만들기](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Azure Databricks 클러스터에 Python 라이브러리 설치

Azure Databricks 클러스터에 [Python 라이브러리](/azure/kusto/api/python/kusto-python-client-library)를 설치하려면:

1. Azure Databricks 작업 영역으로 이동하여 [라이브러리 만들기](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Python PyPI 패키지 또는 Python Egg 업로드](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Databricks 클러스터에 라이브러리를 업로드, 설치 및 연결합니다.
    - PyPi 이름 *azure-kusto-data*를 입력합니다.

## <a name="connect-to-adx-using-device-login"></a>디바이스 로그인을 사용하여 ADX에 연결

[Query-ADX-device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Notebook을 사용하여 [Notebook을 가져오고](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) 자격 증명을 사용하여 ADX에 연결합니다.

## <a name="connect-to-adx-using-azure-ad-app"></a>Azure AD 앱을 사용하여 ADX에 연결

1. [AAD 애플리케이션을 프로비전](/azure/kusto/management/access-control/how-to-provision-aad-app)하여 Azure AD 앱을 만듭니다.
1. Azure Data Explorer 데이터베이스에서 Azure AD 앱에 대한 액세스 권한을 다음과 같이 부여합니다.

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | 데이터베이스 이름 |
    | ```AAD App ID``` | Azure AD 앱 ID |
    | ```AAD Tenant ID``` | Azure AD 테넌트 ID |

### <a name="find-your-azure-ad-tenant-id"></a>Azure AD 테넌트 ID 찾기

애플리케이션을 인증하기 위해 Azure Data Explorer는 Azure AD 테넌트 ID를 사용합니다. 테넌트 ID를 찾으려면 다음 URL을 사용하여 *YourDomain*을 사용자 도메인으로 대체합니다.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

예를 들어 도메인이 *contoso.com*인 경우 URL은 [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/)입니다. 결과를 보려면 이 URL을 클릭합니다. 첫 번째 줄은 다음과 같습니다. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

테넌트 ID는 `6babcaad-604b-40ac-a9d7-9fd97c0b779f`입니다. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Azure AD 앱 ID 및 키 저장 및 보호 

Azure Databricks [비밀](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)을 사용하여 다음과 같이 Azure AD 앱 ID 및 키를 저장하고 보호합니다.
1. [CLI 설정](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [CLI 설치](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [인증을 설정](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication)합니다.
1. 다음 샘플 명령을 사용하여 [비밀](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets)을 구성합니다.

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Notebook 가져오기
[Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) Notebook을 사용하여 [Notebook을 가져오고](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) ADX에 연결합니다. 클러스터 이름, 데이터베이스 이름 및 Azure AD 테넌트 ID를 사용하여 자리 표시자 값을 업데이트합니다.