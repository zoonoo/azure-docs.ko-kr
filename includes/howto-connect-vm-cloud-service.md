<properties authors="kathydav" editor="tysonn" manager="timlt" /> 


#클라우드 서비스에서 가상 컴퓨터를 연결하는 방법

가상 컴퓨터를 만들면 컴퓨터를 포함할 클라우드 서비스가 자동으로 만들어집니다. 가상 컴퓨터가 서로 통신할 수 있도록 같은 클라우드 서비스 내에 여러 가상 컴퓨터를 만들 수 있습니다. 

> [AZURE.NOTE] VM이 같은 클라우드 서비스에 있으면 VM의 부하를 분산하고 가용성을 관리할 수도 있습니다. 두 작업에 모두 추가적인 단계가 필요합니다. 자세한 내용은 다음을 참조하세요. [가상 컴퓨터 부하 분산](../../articles/load-balance-virtual-machines/) 및 [가상 컴퓨터의 가용성 관리](../../articles/manage-availability-virtual-machines/). 

먼저 새 클라우드 서비스를 사용하여 가상 컴퓨터를 만들어야 합니다. 그리고 같은 클라우드 서비스에 추가로 가상 컴퓨터를 만듭니다. 그러면 가상 컴퓨터가 '연결'됩니다. 

1. 먼저 다음 단계에 따라 가상 컴퓨터를 만듭니다. [사용자 지정 가상 컴퓨터를 만드는 방법](../../articles/virtual-machines-create-custom/).

2. 클라우드 서비스를 만드는 대신 클라우드 서비스에 가상 컴퓨터를 추가한다는 점을 제외하고, 같은 기본 프로세스에 따라 다른 가상 컴퓨터를 만듭니다. 예를 들어 *EndpointTest*라는 클라우드 서비스를 만들었으면 해당 서비스를 선택합니다. 다음 그림에서 이를 보여 줍니다.

	![기존 클라우드 서비스에 가상 컴퓨터 추가](./media/howto-connect-vm-cloud-service/Connect-VM-to-CS.png)

14. 이 페이지와 다음 페이지의 나머지 필드를 완료하고 확인 표시를 클릭하여 연결된 가상 컴퓨터를 만듭니다.

#리소스

가상 컴퓨터를 만들고 나서 서비스 및 작업에 데이터를 저장할 위치가 포함되도록 데이터 디스크를 추가하는 것이 좋습니다. 다음의 하나를 참조하세요.

[Linux 가상 컴퓨터에 데이터 디스크를 연결하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/virtual-machines-linux-how-to-attach-disk/)

[Windows 가상 컴퓨터에 데이터 디스크를 연결하는 방법](http://azure.microsoft.com/ko-kr/documentation/articles/storage-windows-attach-disk/)



<!--HONumber=42-->
