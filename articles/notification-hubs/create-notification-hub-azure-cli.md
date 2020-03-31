---
title: 빠른 시작 - Azure CLI를 사용하여 Azure 알림 허브 만들기 | Microsoft Docs
description: 이 자습서에서는 Azure CLI를 사용하여 Azure 알림 허브를 만드는 방법을 알아봅니다.
services: notification-hubs
author: dbradish-microsoft
manager: barbkess
editor: sethmanheim
ms.service: notification-hubs
ms.devlang: azurecli
ms.workload: mobile
ms.topic: quickstart
ms.date: 03/17/2020
ms.author: dbradish
ms.reviewer: sethm
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 830fd33e19a10ec6472650e3d26fec677b82c3d7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "80069486"
---
# <a name="quickstart-create-an-azure-notification-hub-using-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Azure 알림 허브 만들기

Azure Notification Hubs는 모든 백 엔드(클라우드 또는 온-프레미스)에서 모든 플랫폼(iOS, Android, Windows, Kindle, Baidu 등)에 알림을 보낼 수 있도록 하는 사용하기 쉬운 스케일 아웃 푸시 엔진을 제공합니다. 서비스에 대한 자세한 내용은 [Azure Notification Hubs란?](notification-hubs-push-notification-overview.md)을 참조하세요.

이 빠른 시작에서는 Azure CLI를 사용하여 알림 허브를 만듭니다. 첫 번째 섹션에서는 알림 허브 네임스페이스를 만들고, 해당 네임스페이스의 액세스 정책 정보를 쿼리하는 단계를 안내합니다. 두 번째 섹션에서는 기존 네임스페이스에서 알림 허브를 만드는 단계를 안내합니다.  사용자 지정 액세스 정책을 만드는 방법도 알아봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Notification Hubs를 사용하려면 Azure CLI 2.0.67 이상 버전이 필요합니다. `az --version`을 실행하여 설치된 버전과 종속 라이브러리를 찾습니다. 설치하거나 업그레이드하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="prepare-your-environment"></a>환경 준비

1. 로그인합니다.

   로컬에 설치된 CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 로그인합니다.

    ```azurecli-interactive
    az login
    ```

    터미널에 표시된 단계에 따라 인증 프로세스를 완료합니다.

2. Azure CLI 확장을 설치합니다.

   알림 허브에 대한 Azure CLI 명령을 실행하려면 [Notification Hubs용 Azure CLI 확장](/cli/azure/ext/notification-hub/notification-hub)을 설치합니다.  

    ```azurecli-interactive
    az extension add --name notification-hub
   ```

3. 리소스 그룹을 만듭니다.

   모든 Azure 리소스와 마찬가지로 Azure 알림 허브는 리소스 그룹에 배포해야 합니다. 리소스 그룹을 사용하면 관련 Azure 리소스를 구성하고 관리할 수 있습니다.

   이 빠른 시작에서는 다음과 같이 [az group create](/cli/azure/group#az-group-create) 명령을 사용하여 *eastus* 위치에 *spnhubrg*라는 리소스 그룹을 만듭니다.

   ```azurecli-interactive
   az group create --name spnhubrg --location eastus
   ```

## <a name="create-a-notification-hub-namespace"></a>알림 허브 네임스페이스 만들기

1. 알림 허브에 대한 네임스페이스 만들기

   네임스페이스는 하나 이상의 허브를 포함하며, 이름은 모든 Azure 구독에서 고유해야 합니다.  지정된 서비스 네임스페이스의 가용성을 확인하려면 [az notification-hub namespace check-availability](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-check-availability) 명령을 사용합니다.  [az notification-hub namespace create](/cli/azure/ext/notification-hub/notification-hub/namespace#ext-notification-hub-az-notification-hub-namespace-create) 명령을 실행하여 네임스페이스를 만듭니다.  

   ```azurecli-interactive
   #check availability
   az notification-hub namespace check-availability --name spnhubns

   #create the namespace
   az notification-hub namespace create --resource-group spnhubrg --name spnhubns  --location eastus --sku Free
   ```

2. 네임스페이스 액세스 정책에 대한 키 및 연결 문자열을 나열합니다.

   새 네임스페이스에 대한 **RootManageSharedAccessKey**라는 액세스 정책이 자동으로 만들어집니다.  모든 액세스 정책에는 두 가지 키 및 연결 문자열 세트가 있습니다.  네임스페이스에 대한 키 및 연결 문자열을 나열하려면 [az notification-hub namespace authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 명령을 실행합니다.

   ```azurecli-interactive
   az notification-hub namespace authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --name RootManageSharedAccessKey
   ```

## <a name="create-notification-hubs"></a>알림 허브 만들기

1. 첫 번째 알림 허브를 만듭니다.

   이제 새 네임스페이스에서 알림 허브를 만들 수 있습니다.  [az notification-hub create](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-create) 명령을 실행하여 알림 허브를 만듭니다.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name spfcmtutorial1nhub --location eastus --sku Free
   ```

2. 두 번째 알림 허브를 만듭니다.

   단일 네임스페이스에 여러 알림 허브를 만들 수 있습니다.  같은 네임스페이스에 두 번째 알림 허브를 만들려면 다른 허브 이름을 사용하여 `az notification-hub create` 명령을 다시 실행합니다.

   ```azurecli-interactive
   az notification-hub create --resource-group spnhubrg --namespace-name spnhubns --name mysecondnhub --location eastus --sku Free
   ```

## <a name="work-with-access-policies"></a>액세스 정책 작업

1. 알림 허브에 대한 새 권한 부여 규칙을 만듭니다.

   각각의 새 알림 허브에 대한 액세스 정책이 자동으로 생성됩니다.  사용자 고유의 액세스 정책을 만들고 사용자 지정 하려면 [az notification-hub authorization-rule create](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-create) 명령을 사용합니다.

   ```azurecli-interactive
   az notification-hub authorization-rule create --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --rights Listen Send
   ```

2. 알림 허브에 대한 액세스 정책을 나열합니다.

   알림 허브에 대해 존재하는 액세스 정책을 쿼리하려면 [az  notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) 명령을 사용합니다.

   ```azurecli-interactive
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table
   ```

   > [!IMPORTANT]
   > 애플리케이션에서 **DefaultFullSharedAccessSignature** 정책을 사용하지 마세요. 이는 백 엔드에서만 사용할 수 있습니다.  클라이언트 애플리케이션에서는 **수신 대기** 액세스 정책만 사용하세요.

3. 알림 허브 액세스 정책에 대한 키 및 연결 문자열을 나열합니다.

   액세스 정책마다 두 가지 키 및 연결 문자열 세트가 있습니다.  나중에 푸시 알림을 처리하는 데 필요합니다.  알림 허브 액세스 정책에 대한 키 및 연결 문자열을 나열하려면 [az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) 명령을 사용합니다.

   ```azurecli-interactive
   #query the keys and connection strings for DefaultListenSharedAccessSignature
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name DefaultListenSharedAccessSignature --output json

   #query the keys and connection strings for the custom policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name spnhub1key --output table
   ```

   > [!NOTE]
   > [알림 허브 네임스페이스](/cli/azure/ext/notification-hub/notification-hub/namespace/authorization-rule#ext-notification-hub-az-notification-hub-namespace-authorization-rule-list-keys) 및 [알림 허브](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys)는 별도의 액세스 정책을 사용합니다.  키 및 연결 문자열을 쿼리할 때 올바른 Azure CLI 참조를 사용하고 있는지 확인해야 합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 리소스 그룹 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name spnhubrg
```

## <a name="see-also"></a>참고 항목

Azure CLI를 사용하여 알림 허브를 관리하기 위한 전체 기능을 검색합니다.

* [Notification Hubs 전체 Azure CLI 참조 목록](/cli/azure/ext/notification-hub/notification-hub)
* [Notification Hubs 네임스페이스 Azure CLI 참조 목록](/cli/azure/ext/notification-hub/notification-hub/namespace)
* [Notification Hubs 권한 부여 규칙 Azure CLI 참조 목록](/cli/azure/ext/notification-hub/notification-hub/authorization-rule)
* [Notification Hubs 자격 증명 Azure CLI 참조 목록](/cli/azure/ext/notification-hub/notification-hub/credential)
