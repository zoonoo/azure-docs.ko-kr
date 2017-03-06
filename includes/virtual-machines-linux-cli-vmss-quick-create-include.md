## <a name="prerequisites"></a>필수 조건

아직 구독이 없으면 [Azure 구독 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 다운로드하고 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2)을 설치합니다.

## <a name="create-the-scale-set"></a>크기 집합 만들기

먼저 크기 집합을 배포할 리소스 그룹을 만듭니다.

```azurecli
az group create --location westus --name myResourceGroup
```

이제 `az vmss create` 명령을 사용하여 크기 집합을 만듭니다. 다음 예제에서는 `myrg`라는 리소스 그룹에 `myvmss`이라는 크기 집합을 만듭니다.

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image UbuntuLTS --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

다음 예제에서는 같은 구성으로 Windows 크기 집합을 만듭니다.

```azurecli
az vmss create --resource-group myResourceGroup --name myVmss \
    --image Win2016Datacenter --admin-username azureuser \
    --authentication-type password --admin-password P4$$w0rd
```

다른 OS 이미지를 선택하고 싶으면 `az vm image list` 또는 `az vm image list --all` 명령을 사용하여 다른 이미지를 볼 수 있습니다. 크기 집합의 VM에 대한 연결 정보를 보려면 `az vmss list_instance_connection_info` 명령을 사용합니다.

```azurecli
az vmss list_instance_connection_info --resource-group myResourceGroup --name myVmss
```