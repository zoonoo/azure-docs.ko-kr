<properties
   pageTitle="부하 분산 장치에 대한 Azure 리소스 관리자 지원 미리 보기 | Microsoft Azure"
   description="ARM(Azure 리소스 관리자)(미리 보기)과 함께 부하 분산 장치용 PowerShell을 사용합니다. 부하 분산 장치에 템플릿을 사용합니다."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="joaoma" />


# 부하 분산 장치에 대한 Azure 리소스 관리자 지원 

ARM(Azure 리소스 관리자)은 Azure 서비스에 대한 새로운 관리 프레임워크입니다. 이제 Azure 리소스 관리자 기반 API 및 도구를 사용하여 Azure 부하 분산 장치를 관리할 수 있습니다. Azure 리소스 관리자에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-preview-portal-using-resource-groups.md)를 참조하세요.

## 개념

ARM을 사용하면 Azure 부하 분산 장치에 다음과 같은 자식 리소스가 포함됩니다.

- 프런트 엔드 IP 구성 - 부하 분산 장치는 하나 이상의 프런트 엔드 IP 주소(VIP(가상 IP)라고 함)를 포함할 수 있습니다. 이러한 IP 주소는 트래픽에 대한 수신으로 사용됩니다.

- 백 엔드 주소 풀 - 부하가 분산될 가상 컴퓨터 NIC(네트워크 인터페이스 카드)와 연결된 IP 주소입니다.

- 부하 분산 규칙 - 규칙 속성은 지정된 프런트 엔드 IP와 포트 조합을 백 엔드 IP 주소와 포트 조합 집합에 매핑합니다. 부하 분산 장치 리소스의 단일 정의를 사용하여 각각 VM에 연결된 프런트 엔드 IP와 포트 및 백 엔드 IP와 포트 조합을 반영하는 여러 부하 분산 규칙을 정의할 수 있습니다.

- 검색 - 검색을 사용하여 VM 인스턴스의 상태를 추적할 수 있습니다. 상태 검색에 실패하면 해당 VM 인스턴스는 자동으로 회전에서 제외됩니다.

- 인바운드 NAT 규칙 - 프런트 엔드 IP를 통해 흐르고 백 엔드 IP에 분산되는 인바운드 트래픽을 정의하는 NAT 규칙입니다.


![](./media/load-balancer-arm/load-balancer-arm.png)



## 빠른 시작 템플릿
Azure 리소스 관리자를 사용하면 선언적 템플릿을 통해 응용 프로그램을 프로비전할 수 있습니다. 단일 템플릿에서 여러 서비스를 해당 종속성과 함께 배포할 수 있습니다. 동일한 템플릿을 사용하여 응용 프로그램 수명 주기의 각 단계 중에 응용 프로그램을 반복해서 배포합니다.

템플릿은 가상 컴퓨터, 가상 네트워크, 가용성 집합, NIC(네트워크 인터페이스), 저장소 계정, 부하 분산 장치, 네트워크 보안 그룹 및 공용 IP를 포함합니다. 템플릿을 사용하면 체크 인하여 공동으로 작업할 수 있는 간단한 파일을 통해 복잡한 응용 프로그램에 필요한 모든 요소를 만들 수 있습니다.

[템플릿에 대한 자세한 정보](http://go.microsoft.com/fwlink/?LinkId=544798)

[네트워크 리소스에 대한 자세한 정보](../resource-groups-networking)

Azure 부하 분산 장치를 사용한 템플릿은 커뮤니티 생성 템플릿 집합을 호스트하는 [GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)에서 찾을 수 있습니다.

템플릿의 예:

- [부하 분산 장치의 2개 VM 및 부하 분산 규칙](http://go.microsoft.com/fwlink/?LinkId=544799)

- [내부 부하 분산 장치를 포함하는 VNET의 2개 VM 및 부하 분산 장치 규칙](http://go.microsoft.com/fwlink/?LinkId=544800)

- [부하 분산 장치의 2개 VM 및 LB에서 NAT 규칙 구성](http://go.microsoft.com/fwlink/?LinkId=544801)


## PowerShell 또는 CLI를 사용하여 Azure 부하 분산 장치 설정

[Azure 네트워킹 Cmdlet](https://msdn.microsoft.com/library/azure/mt163510.aspx)을 사용하여 부하 분산 장치를 만들 수 있습니다. ARM cmdlet 및 REST API 시작

- [Azure 리소스 관리자를 사용하여 부하 분산 장치를 만드는 방법](../load-balancer-arm-powershell)

- [Azure 리소스 관리에서 Azure CLI 사용](../xplat-cli-azure-resource-manager)

- [부하 분산 장치 REST API](https://msdn.microsoft.com/library/azure/mt163651.aspx)


## 참고 항목

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=Oct15_HO3-->