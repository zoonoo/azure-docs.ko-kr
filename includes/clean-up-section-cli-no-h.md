이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하여 해당 리소스를 삭제할 수 있습니다.
 
1. 다음 명령을 실행하여 리소스 그룹에 저장할 리소스가 없는지 확인합니다.

  ```azurecli
  az group show --name myResourceGroup
  ```

2. 표시된 리소스가 모두 삭제하려는 리소스이면 다음 명령을 실행합니다.
 
  ```azurecli
  az group delete --name myResourceGroup
  ```
