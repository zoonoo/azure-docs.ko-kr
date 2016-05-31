캐시를 만들려면 먼저 [Azure 포털](https://portal.azure.com)에 로그인하고 **새로 만들기**, **데이터 + 저장소**, **Redis Cache**를 클릭합니다.

>[AZURE.NOTE] Azure 포털에서 캐시를 만드는 것 외에도 ARM 템플릿, PowerShell 또는 Azure CLI를 사용해서도 만들 수 있습니다.
>
>-	ARM 템플릿을 사용하여 캐시를 만들려면 [템플릿을 사용하여 Redis Cache 만들기](../articles/redis-cache/cache-redis-cache-arm-provision.md)를 참조하세요.
>-	Azure PowerShell을 사용하여 캐시를 만들려면 [Azure PowerShell을 사용하여 Azure Redis Cache 관리](../articles/redis-cache/cache-howto-manage-redis-cache-powershell.md)를 참조하세요.
>-	Azure CLI를 사용하여 캐시를 만들려면 [Azure 명령줄 인터페이스(Azure CLI)를 사용하여 Azure Redis Cache를 만들고 관리하는 방법](../articles/redis-cache/cache-manage-cli.md)을 참조하세요.

![새 캐시](media/redis-cache-create/redis-cache-new-cache-menu.png)

>[AZURE.NOTE] Azure 계정이 없는 경우 몇 분 만에 [Azure 무료 계정을 열 수 있습니다](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero).

**새 Redis Cache** 블레이드에서 원하는 캐시 구성을 지정합니다.

![캐시 만들기](media/redis-cache-create/redis-cache-cache-create.png)

-	**DNS 이름** - 캐시 끝점에 사용할 캐시 이름을 입력합니다. 캐시 이름은 1~63자의 문자열로, 숫자, 영문자 및 `-` 문자만 포함할 수 있습니다. 캐시 이름은 `-` 문자로 시작하거나 끝날 수 없고 연속되는 `-` 문자는 유효하지 않습니다.
-	**구독**에서는 캐시에 사용할 Azure 구독을 선택합니다. 계정에 구독이 하나뿐인 경우 해당 구독이 자동으로 선택되며 **구독** 드롭다운은 표시되지 않습니다.
-	**리소스 그룹**에서 캐시에 대한 리소스 그룹을 선택하거나 만듭니다. 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../articles/resource-group-overview.md)(영문)를 참조하세요. 
-	**위치**를 사용하여 캐시가 호스트되는 지리적 위치를 지정합니다. 성능을 최적화하려면 캐시 클라이언트 응용 프로그램과 동일한 지역에 캐시를 만드는 것이 좋습니다.
-	**가격 계층**을 사용하여 원하는 캐시 크기 및 기능을 선택합니다.
-	**Redis 클러스터**를 사용하면 53GB 보다 큰 캐시를 만들고 여러 Redis 노드에 걸쳐 데이터를 분할할 수 있습니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 클러스터링을 구성하는 방법](../articles/redis-cache/cache-how-to-premium-clustering.md)을 참조하세요.
-	**Redis 지속성**은 Azure 저장소 계정으로 캐시를 유지하는 기능을 제공합니다. 지속성 구성에 대한 지침은 [프리미엄 Azure Redis Cache에 지속성을 구성하는 방법](../articles/redis-cache/cache-how-to-premium-persistence.md)을 참조하세요.
-	**가상 네트워크**는 지정된 Azure 가상 네트워크 내에서 해당 클라이언트에 캐시에 대한 액세스를 제한하여 향상된 보안 및 격리를 제공합니다. 서브넷, 액세스 제어 정책과 같은 VNet의 모든 기능 및 다른 기능을 사용하여 추가로 Redis에 대한 액세스를 제한할 수 있습니다. 자세한 내용은 [프리미엄 Azure Redis Cache에 가상 네트워크 지원을 구성하는 방법](../articles/redis-cache/cache-how-to-premium-vnet.md)을 참조하세요.

새 캐시 옵션을 구성했으면 **만들기**를 클릭합니다. 캐시를 만드는 데 몇 분 정도 걸릴 수 있습니다. 상태를 확인하려면 시작 보드에서 진행률을 모니터링하면 됩니다. 캐시가 생성되면 새로운 캐시는 **Running** 상태가 되며, 기본 설정을 이용해 바로 사용할 수 있습니다.

![캐시 만듬](media/redis-cache-create/redis-cache-cache-created.png)

