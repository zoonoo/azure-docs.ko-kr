Azure Redis Cache 인스턴스에 연결하려면 캐시 클라이언트에는 캐시의 호스트 이름, 포트 및 키가 필요합니다. 일부 클라이언트는 약간 다른 이름으로 이러한 항목을 참조할 수 있습니다. Azure Portal에서 또는 Azure CLI와 같은 명령줄 도구를 사용하여 이 정보를 검색할 수 있습니다.

### <a name="retrieve-host-name-ports-and-access-keys-using-the-azure-portal"></a>Azure Portal을 사용하여 호스트 이름, 포트 및 액세스 키를 검색합니다.
Azure Portal을 사용하여 호스트 이름, 포트 및 액세스 키를 검색하려면 [Azure Portal](https://portal.azure.com)에서 캐시를 [찾아보고](../articles/redis-cache/cache-configure.md#configure-redis-cache-settings) **리소스 메뉴**에서 **액세스 키** 및 **속성**을 클릭합니다. 

![Redis Cache 설정](media/redis-cache-access-keys/redis-cache-hostname-ports-keys.png)

### <a name="retrieve-host-name-ports-and-access-keys-using-azure-cli"></a>Azure CLI를 사용하여 호스트 이름, 포트 및 액세스 키를 검색합니다.
Azure CLI 2.0을 사용하여 호스트 이름 및 포트를 검색하려면 [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show)를 호출하고 키를 검색하려면 [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys)를 호출하면 됩니다. 다음 스크립트는 이러한 두 개의 명령을 호출하고 호스트 이름, 포트 및 키를 콘솔에 적용합니다.

```azurecli
#/bin/bash

# Retrieve the hostname, ports, and keys for contosoCache located in contosoGroup

# Retrieve the hostname and ports for an Azure Redis Cache instance
redis=($(az redis show --name contosoCache --resource-group contosoGroup --query [hostName,enableNonSslPort,port,sslPort] --output tsv))

# Retrieve the keys for an Azure Redis Cache instance
keys=($(az redis list-keys --name contosoCache --resource-group contosoGroup --query [primaryKey,secondaryKey] --output tsv))

# Display the retrieved hostname, keys, and ports
echo "Hostname:" ${redis[0]}
echo "Non SSL Port:" ${redis[2]}
echo "Non SSL Port Enabled:" ${redis[1]}
echo "SSL Port:" ${redis[3]}
echo "Primary Key:" ${keys[0]}
echo "Secondary Key:" ${keys[1]}
```

이 스크립트에 대한 자세한 내용은 [Azure Redis Cache의 호스트 이름, 포트 및 키 가져오기](../articles/redis-cache/scripts/cache-keys-ports.md)를 참조하세요. Azure CLI 2.0에 대한 자세한 내용은 [Azure CLI 2.0 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 및 [Azure CLI 2.0 시작](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)을 참조하세요.
