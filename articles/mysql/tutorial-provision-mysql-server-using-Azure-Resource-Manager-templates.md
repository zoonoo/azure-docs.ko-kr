---
title: '자습서: Azure Resource Manager 템플릿을 사용하여 Azure Database for MySQL 서버 프로비전'
description: 이 자습서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Database for MySQL 서버의 배포를 프로비전하고 자동화하는 방법을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: json
ms.topic: tutorial
ms.date: 12/21/2018
ms.custom: mvc
ms.openlocfilehash: fa8091011575822907915b67cf48bffaf5e7e728
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657079"
---
# <a name="tutorial-provision-an-azure-database-for-mysql-server-using-azure-resource-manager-template"></a>자습서: Azure Resource Manager 템플릿을 사용하여 Azure Database for MySQL 서버 프로비전

[Azure Database for MySQL REST API](https://docs.microsoft.com/rest/api/mysql/)를 사용하면 DevOps 엔지니어가 Azure에서 관리형 MySQL 서버 및 데이터베이스의 프로비저닝, 구성 및 작업을 자동화하고 통합할 수 있습니다.  API를 통해 Azure Database for MySQL 서비스에서 MySQL 서버 및 데이터베이스를 생성, 열거, 관리 및 삭제할 수 있습니다.

Azure Resource Manager는 기본 REST API를 활용하여 규모에 맞게 배포하는 데 필요한 Azure 리소스를 코드 개념으로 인프라에 맞춰 선언하고 프로그래밍합니다. 템플릿은 Azure 리소스 이름, SKU, 네트워크, 방화벽 구성 및 설정을 매개변수화하면서 템플릿을 한 번 만들어 여러 번 사용할 수 있게 합니다.  [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal) 또는 [Visual Studio Code](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code?tabs=CLI)를 사용하면 Azure Resource Manager 템플릿을 쉽게 만들 수 있습니다. 이 템플릿을 통해 애플리케이션 패키징, 표준화 및 배포 자동화를 사용할 수 있으며, DevOps CI/CD 파이프라인에 통합할 수 있습니다.  예를 들어 Azure Database for MySQL 백 엔드를 통해 웹앱을 신속하게 배포하려면 GitHub 갤러리에서 [빠른 시작 템플릿](https://azure.microsoft.com/en-us/resources/templates/101-webapp-managed-mysql/)을 사용하여 엔드투엔드 배포를 수행할 수 있습니다.

이 자습서에서는 Azure Resource Manager 템플릿 및 다른 유틸리티를 사용하여 다음을 수행하는 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Azure Resource Manager 템플릿을 사용하여 VNet 서비스 엔드포인트에서 Azure Database for MySQL 서버 만들기
> * [mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트

## <a name="create-an-azure-database-for-mysql-server-with-vnet-service-endpoint-using-azure-resource-manager-template"></a>Azure Resource Manager 템플릿을 사용하여 VNet 서비스 엔드포인트에서 Azure Database for MySQL 서버 만들기

Azure Database for MySQL 서버에 대한 JSON 템플릿 참조를 가져오려면 [Microsoft.DBforMySQL 서버](/azure/templates/microsoft.dbformysql/servers) 템플릿 참조로 이동합니다. VNet 서비스 엔드포인트에서 Azure Database for MySQL을 실행하는 새 서버를 만드는 데 사용할 수 있는 샘플 JSON 템플릿은 다음과 같습니다.
```json
{
  "apiVersion": "2017-12-01",
  "type": "Microsoft.DBforMySQL/servers",
  "name": "string",
  "location": "string",
  "tags": "string",
  "properties": {
    "version": "string",
    "sslEnforcement": "string",
    "administratorLogin": "string",
    "administratorLoginPassword": "string",
    "storageProfile": {
      "storageMB": "string",
      "backupRetentionDays": "string",
      "geoRedundantBackup": "string"
    }
  },
  "sku": {
    "name": "string",
    "tier": "string",
    "capacity": "string",
    "family": "string"
  },
  "resources": [
    {
      "name": "AllowSubnet",
      "type": "virtualNetworkRules",
      "apiVersion": "2017-12-01",
      "properties": {
        "virtualNetworkSubnetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnetName'))]",
        "ignoreMissingVnetServiceEndpoint": true
      },
      "dependsOn": [
        "[concat('Microsoft.DBforMySQL/servers/', parameters('serverName'))]"
      ]
    }
  ]
}
```
이 요청에서 사용자 지정해야 하는 값은 다음과 같습니다.
+   `name` - 도메인 이름 없이 MySQL 서버의 이름을 지정합니다.
+   `location` - MySQL 서버에 대한 올바른 Azure 데이터 센터 지역을 지정합니다. 예: westus2.
+   `properties/version` - 배포할 MySQL 서버 버전을 지정합니다. 예: 5.6 또는 5.7.
+   `properties/administratorLogin` -서버에 대한 MySQL 관리자 로그인을 지정합니다. 관리자 로그인 이름은 azure_superuser, admin, administrator, root, guest 또는 public일 수 없습니다.
+   `properties/administratorLoginPassword` - 위에서 지정한 MySQL 관리 사용자의 암호를 지정합니다.
+   `properties/sslEnforcement` - sslEnforcement를 활성화/비활성화하려면 사용/사용 안 함을 지정합니다.
+   `storageProfile/storageMB` - 서버에 필요한 프로비전된 최대 스토리지 크기(메가바이트)를 지정합니다. 예: 5120.
+   `storageProfile/backupRetentionDays` - 원하는 백업 보존 기간(일)을 지정합니다. 예: 7. 
+   `storageProfile/geoRedundantBackup` - Geo-DR 요구 사항에 따라 사용/사용 안 함을 지정합니다.
+   `sku/tier` - 배포에 대한 기본, GeneralPurpose 또는 MemoryOptimized 계층을 지정합니다.
+   `sku/capacity` - vCore 용량을 지정합니다. 가능한 값은 2, 4, 8, 16, 32 또는 64입니다.
+   `sku/family` - Gen4 또는 Gen5를 지정하여 서버 배포에 대한 하드웨어 세대를 선택합니다.
+   `sku/name` - TierPrefix_family_capacity를 지정합니다. 예: B_Gen4_1, GP_Gen5_16, MO_Gen5_32. 지역당 및 계층당 유효한 값을 이해하려면 [가격 책정 계층](./concepts-pricing-tiers.md) 설명서를 참조하세요.
+   `resources/properties/virtualNetworkSubnetId` - Azure MySQL 서버를 배치해야 할 VNet에서 서브넷의 Azure 식별자를 지정합니다. 
+   `tags(optional)` - 선택적 태그가 청구 등에 대한 리소스를 분류하는 데 사용할 키 값 쌍이 되도록 지정합니다.

조직의 Azure Database for MySQL 배포를 자동화하기 위해 Azure Resource Manager 템플릿을 빌드하려는 경우 먼저 Azure Quickstart GitHub Gallery의 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) 샘플에서 시작하고 그 위에 빌드하는 것이 좋습니다. 

Azure Resource Manager 템플릿에 익숙치 않은데 시도하려는 경우 이러한 단계를 따라 시작할 수 있습니다.
+   Azure 빠른 시작 갤러리에서 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/101-managed-mysql-with-vnet) 샘플을 복제 또는 다운로드합니다.  
+   사용자 기본 설정에 따라 매개 변수 값을 업데이트하려면 azuredeploy.parameters.json을 수정한 다음, 파일을 저장합니다. 
+   Azure CLI를 통해 다음 명령을 사용하여 Azure MySQL 서버 만들기

브라우저에서 Azure Cloud Shell을 사용하거나 컴퓨터에 Azure CLI를 설치하여 이 자습서의 코드 블록을 실행할 수 있습니다.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

```azurecli-interactive
az login
az group create -n ExampleResourceGroup  -l “West US2”
az group deployment create -g $ ExampleResourceGroup   --template-file $ {templateloc} --parameters $ {parametersloc}
```

## <a name="get-the-connection-information"></a>연결 정보 가져오기
서버에 연결하려면 호스트 정보와 액세스 자격 증명을 제공해야 합니다.
```azurecli-interactive
az mysql server show --resource-group myresourcegroup --name mydemoserver
```

결과는 JSON 형식입니다. **fullyQualifiedDomainName** 및 **administratorLogin**을 기록해 둡니다.
```json
{
  "administratorLogin": "myadmin",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "mydemoserver.mysql.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforMySQL/servers/mydemoserver",
  "location": "westus2",
  "name": "mydemoserver",
  "resourceGroup": "myresourcegroup",
 "sku": {
    "capacity": 2,
    "family": "Gen4",
    "name": "GP_Gen4_2",
    "size": null,
    "tier": "GeneralPurpose"
  },
  "sslEnforcement": "Enabled",
  "storageProfile": {
    "backupRetentionDays": 7,
    "geoRedundantBackup": "Disabled",
    "storageMb": 5120
  },
  "tags": null,
  "type": "Microsoft.DBforMySQL/servers",
  "userVisibleState": "Ready",
  "version": "5.7"
}
```

## <a name="connect-to-the-server-using-mysql"></a>mysql을 사용하여 서버에 연결
[mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 Azure Database for MySQL 서버에 연결합니다. 이 예제에서 명령은 다음과 같습니다.
```cmd
mysql -h mydemoserver.database.windows.net -u myadmin@mydemoserver -p
```

## <a name="create-a-blank-database"></a>빈 데이터베이스 만들기
서버에 연결되면 빈 데이터베이스를 만듭니다.
```sql
mysql> CREATE DATABASE mysampledb;
```

프롬프트에서 다음 명령을 실행하여 새로 만든 이 데이터베이스에 대한 연결로 전환합니다.
```sql
mysql> USE mysampledb;
```

## <a name="create-tables-in-the-database"></a>데이터베이스에서 테이블 만들기
이제 Azure Database for MySQL 데이터베이스에 연결하는 방법을 알았으므로 몇 가지 기본 작업을 수행할 수 있습니다.

먼저 테이블을 만들고 일부 데이터와 함께 로드합니다. 인벤토리 정보를 저장하는 테이블을 만들어 보겠습니다.
```sql
CREATE TABLE inventory (
    id serial PRIMARY KEY, 
    name VARCHAR(50), 
    quantity INTEGER
);
```

## <a name="load-data-into-the-tables"></a>테이블에 데이터 로드
이제 테이블을 만들었으므로 이 테이블에 일부 데이터를 삽입합니다. 열린 명령 프롬프트 창에서 다음 쿼리를 실행하여 데이터 행을 일부 삽입합니다.
```sql
INSERT INTO inventory (id, name, quantity) VALUES (1, 'banana', 150); 
INSERT INTO inventory (id, name, quantity) VALUES (2, 'orange', 154);
```

이제 앞에서 만든 테이블에 두 개의 샘플 데이터 행이 있습니다.

## <a name="query-and-update-the-data-in-the-tables"></a>테이블의 데이터 쿼리 및 업데이트
다음 쿼리를 실행하여 데이터베이스 테이블에서 정보를 검색합니다.
```sql
SELECT * FROM inventory;
```

또한 테이블의 데이터를 업데이트할 수도 있습니다
```sql
UPDATE inventory SET quantity = 200 WHERE name = 'banana';
```

이에 따라 데이터를 검색할 때 해당 행이 업데이트됩니다.
```sql
SELECT * FROM inventory;
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 다음에 대해 알아보았습니다.
> [!div class="checklist"]
> * Azure Resource Manager 템플릿을 사용하여 VNet 서비스 엔드포인트에서 Azure Database for MySQL 서버 만들기
> * [mysql 명령줄 도구](https://dev.mysql.com/doc/refman/5.6/en/mysql.html)를 사용하여 데이터베이스 만들기
> * 샘플 데이터 로드
> * 쿼리 데이터
> * 데이터 업데이트

> [MySQL용 Azure Database에 애플리케이션을 연결하는 방법](./howto-connection-string.md)
