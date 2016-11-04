아직 없는 경우 [Azure 구독 무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 및 [Azure 계정에 연결된](../articles/xplat-cli-connect.md) [Azure CLI](../articles/xplat-cli-install.md)를 가져올 수 있습니다. 그러면 다음 명령을 실행하여 크기 집합을 신속하게 만들 수 있습니다.

```bash
# make sure we are in Resource Manager mode (https://azure.microsoft.com/documentation/articles/resource-manager-deployment-model/)
azure config mode arm

# quick-create a scale set
#
# generic syntax:
# azure vmss quick-create -n SCALE-SET-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4ssw0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

위치 또는 이미지 urn을 사용자 지정하려는 경우 `azure location list` 및 `azure vm image {list-publishers|list-offers|list-skus|list|show}` 명령을 확인하세요.

이 명령이 반환되면 크기 집합이 만들어진 것입니다. 이 크기 집합에는 해당 부하 분산 장치의 50,000+i 포트를 VM i의 포트 22에 매핑하는 NAT 규칙이 설정된 부하 분산 장치가 있습니다. 따라서 부하 분산 장치의 FQDN을 확인한 경우 VM에 ssh를 통해 연결할 수 있습니다.

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

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated to it
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

<!---HONumber=AcomDC_0413_2016-->