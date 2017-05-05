

![독립 실행형 클라우드 서비스의 가상 컴퓨터](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

가상 컴퓨터를 가상 네트워크에 둔 경우 부하 분산 및 가용성 집합에 사용할 클라우드 서비스 수를 결정할 수 있습니다. 또한 온-프레미스 네트워크와 동일한 방법으로 서브넷에서 가상 컴퓨터를 구성하고 가상 컴퓨터를 온-프레미스 네트워크에 연결할 수 있습니다. 예를 들면 다음과 같습니다.

![가상 네트워크의 가상 컴퓨터](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

가상 네트워크는 Azure에서 가상 컴퓨터를 연결하는 데 권장되는 방법입니다. 응용 프로그램의 각 계층을 별도의 클라우드 서비스에 구성하는 것이 가장 좋습니다. 그러나 구독당 클라우드 서비스 수를 최대 200개의 범위 내에서 유지하기 위해 여러 응용 프로그램 계층의 일부 가상 컴퓨터를 동일한 클라우드 서비스에 통합해야 할 수도 있습니다. 이 제한 및 다른 제한을 검토하려면 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../articles/azure-subscription-service-limits.md)을 참조하세요.

## <a name="connect-vms-in-a-virtual-network"></a>가상 네트워크에서 VM 연결
가상 네트워크에서 가상 컴퓨터를 연결하려면

1. [Azure 포털](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)에서 가상 네트워크를 만듭니다.
2. 가용성 집합 및 부하 분산에 대한 디자인을 반영하기 위해 배포에 대한 클라우드 서비스의 집합을 만듭니다. Azure 클래식 포털에서 각 클라우드 서비스에 대해 **새로 만들기 > 계산 > 클라우드 서비스 > 사용자 지정 만들기**를 클릭합니다.
3. 각각의 새 가상 컴퓨터를 만들려면 **새로 만들기 > 계산 > 가상 컴퓨터 > 갤러리에서**를 클릭합니다. VM에 대한 올바른 클라우드 서비스 및 가상 네트워크를 선택합니다. 클라우드 서비스가 가상 네트워크에 이미 연결된 경우 해당 이름이 이미 선택되어 있을 것입니다.

![가상 컴퓨터에 대한 클라우드 서비스 선택](./media/virtual-machines-common-classic-connect-vms/VMConfig1.png)

## <a name="connect-vms-in-a-standalone-cloud-service"></a>독립 실행형 클라우드 서비스에서 VM 연결
독립 실행형 클라우드 서비스에서 가상 컴퓨터를 연결하려면

1. [Azure 클래식 포털](http://manage.windowsazure.com)에서 클라우드 서비스를 만듭니다. **새로 만들기 > 계산 > 클라우드 서비스 > 사용자 지정 만들기**를 클릭합니다. 또는 첫 번째 가상 컴퓨터를 만들 때 배포에 대한 클라우드 서비스를 만들 수 있습니다.
2. 가상 컴퓨터를 만들 때 이전 단계에서 만든 클라우드 서비스의 이름을 선택합니다.
   
   ![기존 클라우드 서비스에 가상 컴퓨터 추가](./media/virtual-machines-common-classic-connect-vms/Connect-VM-to-CS.png)

