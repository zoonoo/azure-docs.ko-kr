<properties
 pageTitle="HPC 팩 클러스터에 버스트 노드 추가 | Microsoft Azure"
 description="주문형 클라우드 서비스에서 실행되는 작업자 역할 인스턴스를 Azure의 HPC 팩 헤드 노드에 계산 리소스로 추가하는 방법을 살펴봅니다."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# 주문형 "버스트" 노드(작업자 역할 인스턴스)를 Azure의 HPC 팩 헤드 노드에 계산 리소스로 추가

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]리소스 관리자 모델.


이 문서는 Azure 주문형 "버스트" 노드(클라우드 서비스에서 실행되는 작업자 역할 인스턴스)를 Azure의 기존 HPC 팩 헤드 노드에 계산 리소스로 추가하는 방법을 보여줍니다. 이렇게 하면 사전 구성된 계산 노드 VM을 유지 관리하지 않고도 Azure에서 HPC 클러스터의 계산 용량을 확장할 수 있습니다.

![버스트 노드][burst]

>[AZURE.TIP] [HPC 팩 IaaS 배포 스크립트](virtual-machines-hpcpack-cluster-powershell-script.md)를 사용하여 Azure에 클러스터를 만들 경우 자동 배포에 Azure 버스트 노드를 포함할 수 있습니다.

이 문서의 단계를 통해 Azure 노드를 클라우드 기반 HPC 팩 헤드 노드 VM에 빠르게 추가하여 테스트 또는 개념 증명 배포를 수행할 수 있습니다. 이 절차는 기본적으로 온-프레미스 HPC 팩 클러스터에 클라우드 계산 용량을 추가하는 "Azure로 버스트"와 동일합니다. 자습서를 보려면 [Microsoft HPC 팩을 사용하여 하이브리드 계산 클러스터 설정](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)을 참조하세요. 프로덕션 배포에 대한 자세한 지침과 고려 사항을 보려면 [Microsoft HPC 팩을 사용하여 Azure로 버스트](http://go.microsoft.com/fwlink/p/?LinkID=200493)를 참조하세요.

A8 또는 A9 계산 집약적 인스턴스 크기를 사용하려는 경우 [A8, A9, A10 및 A11 계산 집약적 인스턴스 정보](virtual-machines-a8-a9-a10-a11-specs.md)를 참조하세요.

## 필수 조건

* **Azure VM에 배포된 HPC 팩 헤드 노드** - [Azure VM에 HPC 팩 헤드 노드 배포](virtual-machines-hpcpack-cluster-headnode.md)에서 클래식(서비스 관리) 배포 모델로 클러스터 헤드 노드를 만드는 단계를 참조하세요.

* **Azure 구독** - Azure 노드를 추가하려는 경우 헤드 노드 VM을 배포하는 데 사용하는 것과 동일한 구독 또는 다른 구독을 하나 이상 선택할 수 있습니다.

* **코어 할당량** - 멀티 코어 크기를 사용하여 여러 Azure 노드를 배포하려는 경우 특히 코어 할당량을 늘려야 할 수 있습니다. 할당량을 늘리려면 무료로 [온라인 고객 지원 요청을 개설](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)합니다.

## 1단계: Azure 노드를 추가할 클라우드 서비스 및 저장소 계정을 만듭니다.

Azure 포털 또는 동급의 도구를 사용하여 Azure 노드 배포에 필요한 다음 항목을 구성합니다.

* 새 Azure 클라우드 서비스
* 새 Azure 저장소 계정

>[AZURE.NOTE]구독에서 기존 클라우드 서비스를 다시 사용하지 마십시오. 또한 이 클라우드 서비스에 별도의 사용자 지정 클라우드 서비스 패키지를 배포하지 마십시오. Azure 노드를 시작(프로비전)하면 HPC 팩에서 클라우드 서비스 패키지를 자동으로 배포합니다.

**고려 사항**

* 만들려는 각 Azure 노드 템플릿에 대해 별도의 클라우드 서비스를 구성합니다. 하지만 여러 노드 템플릿에 대해 동일한 저장소 계정을 사용할 수 있습니다.

* 동일한 지역에서 배포하는 데 사용할 저장소 계정과 클라우드 서비스는 일반적인 방법으로 찾을 수 있습니다.




## 2단계: Azure 관리 인증서 구성

Azure 노드를 계산 리소스로 추가하려면 헤드 노드에 관리 인증서를 추가하고 해당 인증서를 배포에 사용한 Azure 구독에 업로드해야 합니다.

이 시나리오의 경우 HPC 팩에서 설치한 **기본 HPC Azure 관리 인증서**를 선택하고 헤드 노드에 자동으로 구성할 수 있습니다. 이 인증서는 테스트 목적 및 개념 증명 배포에 사용됩니다. 이 인증서를 사용하려면 헤드 노드 VM에서 구독으로 C:\\Program Files\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer 파일을 업로드합니다.

관리 인증서 구성에 대한 추가 옵션을 보려면 [Azure 버스트 배포를 위한 Azure 관리 인증서 구성 시나리오](http://technet.microsoft.com/library/gg481759.aspx)를 참조하세요.

## 단계 3: 클러스터에 Azure 노드 배포



이 시나리오에서 Azure 노드를 추가 및 시작하는 단계는 온-프레미스 헤드 노드에서 사용하는 방법과 일반적으로 동일합니다. 자세한 내용은 [Microsoft HPC 팩을 사용하여 Azure 노드를 배포하는 단계]에서 다음 섹션을 참조하세요(https://technet.microsoft.com/library/gg481758(v=ws.10).aspx)).

* Azure 노드 템플릿 만들기

* Windows HPC 클러스터에 Azure 노드 추가

* Azure 노드 시작(프로비전)

노드를 추가 및 시작하면 클러스터 작업을 실행하는 데 사용할 수 있습니다.

Azure 노드를 배포할 때 문제가 발행할 경우 [Microsoft HPC 팩을 사용하여 Azure 노드 배포 시 문제 해결]을 참조하세요(http://technet.microsoft.com/library/jj159097(v=ws.10).aspx)).

## 다음 단계

* Azure 컴퓨팅 리소스를 현재 작업 워크로드 및 클러스터 작업에 따라 자동으로 증가 또는 축소하려는 경우 [HPC 팩 클러스터에서 Azure 계산 리소스 증가 및 축소](virtual-machines-hpcpack-cluster-node-autogrowshrink.md)를 참조하세요.

<!--Image references-->
[burst]: ./media/virtual-machines-hpcpack-cluster-node-burst/burst.png

<!---HONumber=Nov15_HO3-->