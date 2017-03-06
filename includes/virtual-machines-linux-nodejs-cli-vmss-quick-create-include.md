## <a name="prerequisites"></a>필수 조건

아직 없는 경우 [Azure 구독 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 받고 [Azure 계정에 연결된](../articles/xplat-cli-connect.md) [Azure CLI 1.0](../articles/xplat-cli-install.md)을 받을 수 있습니다. 다음과 같이 Azure CLI가 리소스 관리자 모드인지 확인합니다.

```azurecli
azure config mode arm
```

## <a name="create-the-scale-set"></a>크기 집합 만들기

이제 `azure vmss quick-create` 명령을 사용하여 크기 집합을 만듭니다. 다음 예제에서는 `myResourceGroup`이라는 리소스 그룹에서&5;개 VM 인스턴스를 사용하여 `myVMSS`라는 크기 집합을 만듭니다.

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04-LTS:latest
```

다음 예제에서는 같은 구성으로 Windows 크기 집합을 만듭니다.

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

위치 또는 이미지 urn을 사용자 지정하려는 경우 `azure location list` 및 `azure vm image {list-publishers|list-offers|list-skus|list|show}` 명령을 확인합니다.

이 명령은 크기 집합에서 배포를 완료하면 반환합니다. 이 때 크기 집합에는 해당 부하 분산 장치의 50,000+i 포트를 VM i의 포트 22에 매핑하는 NAT 규칙이 설정된 부하 분산 장치가 있습니다. 따라서 부하 분산 장치의 FQDN을 확인한 경우 VM에 ssh를 통해 연결할 수 있습니다.

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```