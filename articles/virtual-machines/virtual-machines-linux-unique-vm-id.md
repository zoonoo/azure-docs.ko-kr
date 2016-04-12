<properties
   pageTitle="VM ID에 액세스"
   description="Azure VM 고유 ID 액세스 및 사용에 대해 설명합니다."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="kmouss"
   manager="drewm"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/08/2016"
   ms.author="kmouss"/>
   
# Azure VM 고유 ID 액세스 및 사용

Azure VM 고유 ID는 128비트 식별자로 인코딩된 후 모든 Azure IaaS VM의 SMBIOS에 저장되며, 현재 플랫폼 BIOS 명령을 사용하여 읽을 수 있습니다.

Azure VM 고유 ID는 읽기 전용 속성입니다. Azure 고유 VM ID는 재부팅 종료(계획된 경우 또는 계획되지 않은 경우), 할당 해제 시작/중지, 서비스 복구 또는 위치로 복원 시 변경되지 않습니다. 그러나 VM이 스냅숏이고 새 인스턴스를 만들기 위해 복사되면 새 Azure VM ID가 구성됩니다.

> [AZURE.NOTE] 이 기능이 롤아웃된 이후(2014년 9월 18일) 이전 VM을 만들어 실행한 경우 VM을 다시 시작하여 Azure 고유 ID를 자동으로 가져오세요.


VM 내에서 Azure 고유 VM ID에 액세스하려면


## VM 만들기
 

자세한 내용은 [가상 컴퓨터 만들기](virtual-machines-linux-creation-choices.md)를 참조하세요.


## VM에 연결
 

자세한 내용은 [Linux의 SSH](virtual-machines-linux-ssh-from-linux.md)를 참조하세요.


## VM 고유 ID 쿼리

명령(예제에서는 **Ubuntu** 사용):

    sudo dmidecode | grep UUID
    
예제의 예상 결과:

    UUID: 090556DA-D4FA-764F-A9F1-63614EDA019A
    
Big Endian 비트 순서 때문에, 이 경우의 실제 고유 VM ID는 다음과 같습니다.

    DA 56 05 09 – FA D4 – 4f 76 - A9F1-63614EDA019A
    
    
Azure VM 고유 ID는 VM이 Azure 또는 온-프레미스에서 실행되는 다양한 시나리오에서 사용될 수 있으며, Azure IaaS 배포에서 발생할 수 있는 라이선스, 보고 또는 일반 추적 요구 사항에도 도움이 될 수 있습니다. 응용 프로그램을 구축하고 Azure에서 인증을 받는 많은 독립 소프트웨어 공급업체는 전체 수명 주기 동안 Azure VM을 식별하고, VM이 Azure, 온-프레미스 또는 기타 클라우드 공급자 중 어디에서 실행되는지 파악해야 할 수 있습니다. 예를 들어 이 플랫폼 식별자는 소프트웨어의 사용이 제대로 허가되는지 여부를 감지하거나 VM 데이터와 원본 간 상관 관계를 파악하는 데 도움이 되므로 올바른 플랫폼에 대해 올바른 메트릭을 설정할 수 있도록 하고, 이러한 메트릭을 추적하고 다양한 경우에 상관 관계를 파악할 수 있도록 합니다.

<!---HONumber=AcomDC_0323_2016-->