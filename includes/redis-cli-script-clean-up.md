## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, Azure Redis Cache 인스턴스 및 리소스 그룹에서 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli
az group delete --name contosoGroup
```