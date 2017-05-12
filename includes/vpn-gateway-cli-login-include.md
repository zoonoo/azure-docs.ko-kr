[az login](/cli/azure/#login) 명령으로 Azure 구독에 로그인하고 화면의 지시를 따릅니다. 로그인에 대한 자세한 내용은 [Azure CLI 2.0 시작](/cli/azure/get-started-with-azure-cli)을 참조하세요.

```azurecli
az login
```

Azure 구독이 두 개 이상인 경우 계정의 구독을 나열합니다.

```azurecli
az account list --all
```

사용할 구독을 지정합니다.

```azurecli
az account set --subscription <replace_with_your_subscription_id>
```

