가상 컴퓨터 크기 집합을 사용하여 여러 VM을 집합으로 관리할 수 있습니다. 개략적으로 크기 집합의 장점과 단점은 다음과 같습니다.

장점:

1. 고가용성. 각 크기 집합은 가용성을 보장하기 위해 5개의 FD(장애 도메인)와 5개의 UD(업데이트 도메인)가 있는 가용성 집합에 VM을 배치합니다(FD와 UD에 대한 자세한 내용은 [VM 가용성](../articles/virtual-machines/virtual-machines-linux-manage-availability.md) 참조).
2. Azure 부하 분산 장치 및 앱 게이트웨이와의 간편한 통합.
3. Azure 자동 크기 조정과의 간편한 통합.
4. VM 배포, 관리 및 정리 간소화.
5. 일반적인 Windows 및 Linux 버전과 사용자 지정 이미지 지원.

단점:

1. 크기 집합의 VM 인스턴스에 데이터 디스크를 연결할 수 없습니다. 대신 Blob 저장소, Azure 파일, Azure 테이블 또는 다른 저장소 솔루션을 사용해야 합니다.

## Azure CLI를 사용한 빠른 생성
[Azure 구독 무료 평가판](https://azure.microsoft.com/pricing/free-trial/) 및 [Azure 계정에 연결된](../articles/xplat-cli-connect.md) [Azure CLI](../articles/xplat-cli-install.md)를 가져올 수 있습니다. 그러면 다음 명령을 실행하여 VM 크기 집합을 신속하게 만들 수 있습니다.

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

위치 또는 이미지 urn을 사용자 지정하려는 경우 `azure location list` 및 `azure vm image {list-publishers|list-offers|list-skus|list|show}` 명령을 확인하세요.

이 명령이 반환되면 크기 집합이 만들어진 것입니다. 이 크기 집합에는 해당 부하 분산 장치의 50,000+i 포트를 VM i의 포트 22에 매핑하는 NAT 규칙이 설정된 부하 분산 장치가 있습니다. 따라서 부하 분산 장치의 FQDN을 확인한 경우 VM에 SSH를 적용할 수 있습니다.

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## 다음 단계
일반적인 정보는 [VM 크기 집합에 대한 주 방문 페이지](https://azure.microsoft.com/services/virtual-machine-scale-sets/)를 확인하세요.

설명서는 [VM 크기 집합에 대한 주 설명서 페이지](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/)를 참조하세요.

VM 크기 집합을 사용하는 예제 Azure Resource Manager 템플릿은 [Azure 퀵 스타트 템플릿 github 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 "vmss"를 검색하세요.

<!---HONumber=AcomDC_0406_2016-->