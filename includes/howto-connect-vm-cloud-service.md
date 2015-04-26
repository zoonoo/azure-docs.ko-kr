<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#가상 컴퓨터를 가상 네트워크 또는 클라우드 서비스와 연결하는 방법

가상 컴퓨터는 컨테이너를 역할을 하며 인터넷을 통해 가상 컴퓨터에 액세스할 수 있도록 고유한 공용 DNS 이름, 공용 IP 주소 및 끝점 집합을 제공하는 클라우드 서비스에 있어야 합니다. 클라우드 서비스는 선택적으로 가상 네트워크에 있을 수 있습니다. 

클라우드 서비스가 가상 네트워크에 없는 경우 해당 클라우드 서비스의 가상 컴퓨터는 다른 가상 컴퓨터의 공용 DNS 이름을 통해서만 해당 가상 컴퓨터와 통신할 수 있으며, 이 트래픽은 인터넷을 통해 전달됩니다. 클라우드 서비스가 가상 네트워크에 있는 경우 해당 클라우드 서비스의 가상 컴퓨터는 인터넷을 통해 트래픽을 보내지 않고도 가상 네트워크에 있는 다른 모든 가상 컴퓨터와 통신할 수 있습니다.

가상 컴퓨터를 독립 실행형 클라우드 서비스에 두면 부하 분산 및 가용성 집합을 활용할 수 있습니다. 자세한 내용은 [가상 컴퓨터 부하 분산](../../articles/load-balance-virtual-machines/) 및 [가상 컴퓨터의 가용성 관리](../../articles/manage-availability-virtual-machines/)를 참조하세요. 그러나 서브넷에서 가상 컴퓨터를 구성하거나 독립 실행형 클라우드 서비스를 온-프레미스 네트워크에 연결할 수는 없습니다. 다음은 예제입니다.

![Virtual machines in a standalone cloud service](./media/howto-connect-vm-cloud-service/CloudServiceExample.png)
 
가상 컴퓨터를 가상 네트워크에 둔 경우 부하 분산 및 가용성 집합을 활용하는 데 사용할 클라우드 서비스 수를 결정할 수 있습니다. 또한 온-프레미스 네트워크와 동일한 방법으로 서브넷에서 가상 컴퓨터를 구성하고 가상 컴퓨터를 온-프레미스 네트워크에 연결할 수 있습니다. 다음은 예제입니다.

![Virtual machines in a virtual network](./media/howto-connect-vm-cloud-service/VirtualNetworkExample.png)

가상 네트워크는 Azure에서 가상 컴퓨터를 연결하는 데 권장되는 방법입니다. 응용 프로그램의 각 계층을 별도의 클라우드 서비스에 구성하는 것이 가장 좋습니다. 그러면 RBAC(역할 기반 액세스 제어)를 통한 고급 사용자 권한 위임이 지원됩니다. 자세한 내용은 [Azure 미리 보기 포털의 역할 기반 액세스 제어](../../articles//role-based-access-control-configure/)를 참조하세요. 그러나 구독당 클라우드 서비스 수를 최대 200개의 범위 내에서 유지하기 위해 여러 응용 프로그램 계층의 일부 가상 컴퓨터를 동일한 클라우드 서비스에 통합해야 할 수도 있습니다.

가상 네트워크에서 가상 컴퓨터를 연결하려면

1.	Azure 관리 포털에서 가상 네트워크를 만듭니다. 자세한 내용은 [가상 네트워크 구성 작업](https://msdn.microsoft.com/library/azure/jj156206.aspx)을 참조하세요.
2.	가용성 집합 및 부하 분산에 대한 설계를 반영하기 위해 Azure 관리 포털에서 **새로 만들기 > 계산 > 클라우드 서비스 > 사용자 지정 만들기**를 사용하여 배포에 대한 클라우드 서비스 집합을 만듭니다.
3.	새 가상 컴퓨터를 만들 때마다 올바른 클라우드 서비스 및 가상 네트워크를 지정합니다. 클라우드 서비스가 이전에 가상 네트워크와 연결된 경우에는 해당 이름이 이미 선택되어 있습니다.

다음은 Azure 관리 포털을 사용하는 예제입니다.

![Selecting a cloud service for a virtual machine](./media/howto-connect-vm-cloud-service/VMConfig1.png)
 
독립 실행형 클라우드 서비스에서 가상 컴퓨터를 연결하려면

1.	Azure 관리 포털에서 **새로 만들기 > 계산 > 클라우드 서비스 > 사용자 지정 만들기**를 사용하여 배포에 대한 클라우드 서비스를 만듭니다.
2.	가상 컴퓨터를 만들 때 1단계에서 만든 클라우드 서비스의 이름을 지정합니다. 
또는 첫 번째 가상 컴퓨터를 만들 때 배포에 대한 클라우드 서비스를 만들 수 있습니다.

다음은 EndpointTest라는 기존 클라우드 서비스에 Azure 관리 포털을 사용하는 예제입니다.
 
![Add a virtual machine to an existing cloud service](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

##리소스
[가상 컴퓨터 부하 분산](../../articles/load-balance-virtual-machines/)

[가상 컴퓨터의 가용성 관리](../../articles/manage-availability-virtual-machines/)

[가상 네트워크 구성 작업](https://msdn.microsoft.com/library/azure/jj156206.aspx)

가상 컴퓨터를 만들고 나서 서비스 및 작업에 데이터를 저장할 위치가 포함되도록 데이터 디스크를 추가하는 것이 좋습니다. 다음 중 하나를 참조하세요.

[Linux 가상 컴퓨터에 데이터 디스크를 연결하는 방법](http://azure.microsoft.com/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](http://azure.microsoft.com/documentation/articles/storage-windows-attach-disk/)


<!--HONumber=45--> 
