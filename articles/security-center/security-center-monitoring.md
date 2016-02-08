<properties
   pageTitle="Azure 보안 센터에서 보안 상태 모니터링 | Microsoft Azure"
   description="이 문서에서는 Azure 보안 센터의 모니터링 기능을 시작하는 방법을 설명합니다."
   services="security-center"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/22/2016"
   ms.author="yurid"/>

#Azure 보안 센터에서 보안 상태 모니터링
이 문서에서는 Azure 보안 센터의 모니터링 기능을 사용하여 정책 준수를 모니터링하는 방법을 설명합니다.

> [AZURE.NOTE] 이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다.

## Azure 보안 센터란?
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 에코시스템에서 작동합니다.

##보안 상태 모니터링이란?
상황에 대응할 수 있도록 이벤트가 발생할 때까지 이벤트를 감시하면서 기다리는 것을 모니터링이라고 생각하는 경우가 많습니다. 보안 모니터링은 리소스를 감사하여 조직의 표준 또는 모범 사례를 충족하지 않는 시스템을 식별하는 사전 예방 전략을 의미합니다.

##보안 상태 모니터링
구독의 리소스에 대한 [보안 정책](security-center-policies.md)을 활성화하면 Azure 보안 센터에서 리소스 보안을 분석하여 잠재적 취약성을 식별합니다. 네트워크 구성에 대한 정보는 즉시 이용할 수 있는 반면 보안 업데이트 상태, OS 구성 등 가상 컴퓨터 구성에 대한 정보를 이용하려면 1시간 이상이 걸립니다. **리소스 보안 상태** 블레이드의 문제와 함께 리소스의 보안 상태를 볼 수 있습니다. **권장 사항** 블레이드에서도 이러한 문제 목록을 볼 수 있습니다.

권장 사항을 적용하는 방법에 대한 자세한 내용은 [Azure 보안 센터에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요.

**리소스 상태** 타일을 사용하여 리소스의 보안 상태를 모니터링할 수 있습니다. 아래와 같이 심각도가 높음 및 보통인 여러 문제가 표시됩니다.

![리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig1-new.png)

활성화된 보안 정책은 여기서 모니터링되는 컨트롤 유형에 영향을 줍니다. Azure 보안 센터에서 보안 업데이트가 누락된 VM, [네트워크 보안 그룹](virtual-networks-nsg.md)이 없는 서브넷 등과 같이 조치가 필요한 취약점을 발견하면 여기에 식별됩니다.

###가상 컴퓨터
**리소스 상태** 타일에서 **가상 컴퓨터**를 클릭하면 아래와 같이 Azure 보안 센터에서 모니터링하는 전체 VM 목록과 온보딩 및 방지 단계에 대한 자세한 내용이 포함된 **가상 컴퓨터** 블레이드가 열립니다.

![VM에서 누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig2-new.png)

이 블레이드를 열면 세 개 섹션이 표시됩니다.

- 등록 단계
- 가상 컴퓨터 권장 사항
- 가상 컴퓨터

각 섹션에서 개별 옵션을 선택하면 해당 문제를 해결하는 권장 단계에 대한 자세한 내용을 볼 수 있습니다. 아래 섹션에서는 이 부분에 대해 더 자세히 설명합니다.

####등록 단계
이 섹션은 데이터 수집을 위해 초기화된 VM의 총량과 현재 상태를 보여줍니다. 각 VM에서 데이터 수집이 초기화되면, Azure 보안 센터 보안 정책을 수신할 준비가 됩니다. 이 항목을 클릭하면 **데이터 수집 초기화** 블레이드가 열리고 아래와 같이 VM의 이름 및 **설치 상태** 열의 내용과 같은 데이터 수집의 현재 상태를 볼 수 있습니다.

![초기화 상태](./media/security-center-monitoring/security-center-monitoring-fig3-new.png)


####가상 컴퓨터 권장 사항
이 섹션에는 Azure 보안 센터에서 모니터링하는 각 VM에 대한 권장 사항이 있습니다. 첫 번째 열에서는 권장 사항을 설명하고 두 번째 열에서는 권장 사항의 영향을 받은 VM의 총량을 표시하고, 세 번째 열에서는 아래와 같이 심각도를 표시합니다.

![VM 권장 사항](./media/security-center-monitoring/security-center-monitoring-fig4-new.png)

각 권장 사항에는 클릭하면 수행되는 작업 집합이 있습니다. 예를 들어 **누락된 시스템 업데이트**를 클릭하면 아래와 같이 패치가 누락된 VM 목록과 각각의 심각도가 표시되는 **누락된 시스템 업데이트** 블레이드가 열립니다.

![누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig5-new.png)

누락된 시스템 업데이트 블레이드에 다음 정보를 포함하는 테이블이 표시됩니다.

- **가상 컴퓨터**: 업데이트가 누락된 가상 컴퓨터의 이름입니다.
- **시스템 업데이트**: 누락된 시스템 업데이트의 양입니다.
- **마지막 검사 시간**: Azure 보안 센터에서 업데이트를 위해 VM을 검사한 마지막 시간입니다.
- **상태**: 권장 사항의 현재 상태: 
	- **열림**: 아직 해결되지 않음
	- **진행 중**: 권장 사항이 현재 해당 리소스에 적용되고 있으며, 사용자가 아무런 작업도 수행할 필요가 없음
	- **해결됨**: 권장 사항이 이미 완료됨(상태가 해결됨이면 선 색상이 회색)
- **심각도**: 특정 권장 사항의 심각도를 설명: 
	- **높음**: 중요한 리소스(응용 프로그램, VM, 네트워크 보안 그룹)에 취약점이 있으며 주의가 필요함
	- **보통**: 프로세스를 완료하거나 취약점을 제거하는 데 필요한 중요하지 않은 또는 추가 단계
	- **낮음**: 해결해야 하지만 즉시 조치하지 않아도 되는 취약점 (기본적으로 심각도가 낮은 권장 사항은 표시되지 않지만 보기로 선택할 경우 심각도가 낮은 권장 사항으로 필터링할 수 있습니다.)

권장 업데이트 세부 정보를 보려면 VM 이름을 클릭합니다. 해당 VM에 대한 새 블레이드가 열리고 누락된 업데이트 목록이 아래와 같이 표시됩니다.

![VM별 누락된 시스템 업데이트](./media/security-center-monitoring/security-center-monitoring-fig6-new.png)

> [AZURE.NOTE] 여기에 표시되는 보안 권장 사항과 권장 사항 블레이에 표시되는 보안 권장 사항이 동일합니다. 권장 사항 해결에 대한 자세한 내용은 [Azure 보안 센터에서 보안 권장 사항 구현](security-center-recommendations.md)을 참조하세요. 이는 VM뿐 아니라 리소스 상태 타일에서 제공되는 모든 리소스에 해당합니다.

####가상 컴퓨터
가상 컴퓨터 섹션에는 모든 VM과 권장 사항에 대한 개요가 제공됩니다. 각각의 권장 사항 열에는 아래와 같이 권장 사항 집합이 표시됩니다.

![VM](./media/security-center-monitoring/security-center-monitoring-fig7-new.png)

각 권장 사항 아래에 표시되는 아이콘은 주의를 기울여야 하는 VM과 VM에 필요한 권장 사항을 식별하는 데 도움이 됩니다.

위의 예제에는 VM 한 대에 맬웨어 방지에 관한 중요한 권장 사항이 있습니다. VM에 대해 보다 자세한 정보를 확보하려면, 해당 항목을 클릭하여 VM을 표시하는 새 블레이드가 아래와 같이 열리도록 합니다.

![VM 보안 세부 정보](./media/security-center-monitoring/security-center-monitoring-fig8-new.png)

이 블레이드에는 VM에 대한 보안 세부 정보가 있습니다. 이 블레이드의 하단에서 권장되는 작업과 각 권장 사항의 심각도를 볼 수 있습니다.

###네트워킹
네트워킹 방지 상태는 Azure 보안 센터에서 모니터링하는 가상 네트워크를 보여줍니다. 리소스 상태 타일에서 **네트워킹**을 클릭하면 아래와 같이 자세한 내용이 포함된 **네트워킹** 블레이드가 열립니다.

![네트워킹](./media/security-center-monitoring/security-center-monitoring-fig9-new.png)

이 블레이드를 열면 두 가지 섹션 즉, 네트워크 권장 사항 및 네트워킹이 표시됩니다.
 
각 섹션에서 개별 옵션을 선택하면 해당 문제를 해결하는 권장 단계에 대한 자세한 내용을 볼 수 있습니다. 아래 섹션에서는 이 부분에 대해 더 자세히 설명합니다.

####네트워킹 권장 사항

가상 컴퓨터 리소스 상태 정보와 마찬가지로, 이 블레이드의 상단에는 요약된 문제 목록, 하단에는 모니터링하는 네트워크 목록이 표시됩니다.

네트워킹 상태 분석 섹션에는 [끝점의 ACL](virtual-machines-set-up-endpoints.md)이 활성화되지 않음, [네트워크 보안 그룹](virtual-networks-nsg.md)이 활성화되지 않음, 서브넷 정상 및 **Access in NSG not restricted**(NSG 내 액세스에 제한 없음)등의 방지 단계가 나열됩니다. 이들 권장 사항 중 하나를 클릭하면 새 블레이드가 열리고 아래 예에 표시된 것과 같이 권장 사항에 대한 자세한 내용이 표시됩니다.

![끝점](./media/security-center-monitoring/security-center-monitoring-fig10-new.png)

이 예제의 **공용 외부 끝점을 통한 액세스 제한** 블레이드에는 이 경고의 일부분인 네트워크 보안 그룹(NSG), NSG가 연결된 서브넷과 네트워크, 권장 사항의 현재 상태와 심각도가 나열됩니다. 네트워크 보안 그룹을 클릭하면, 또 다른 블레이드가 아래와 같이 열립니다.

![끝점 제한](./media/security-center-monitoring/security-center-monitoring-fig11-new.png)

이 블레이드에는 네트워크 보안 그룹 정보, 위치 및 현재 활성화되어 있는 인바운드 규칙의 목록이 표시됩니다. 이 블레이드의 하단에는 네트워크 보안 그룹과 연관된 VM이 표시됩니다. 현재 열려 있지만 원하지 않는 포트를 차단하기 위하여 인바운드 규칙을 활성화하거나 현재 인바운드 규칙의 소스를 변경하려면 블레이드 상단의 **Edit inbound rule**(인바운드 규칙 편집) 단추를 클릭합니다.

####네트워킹
**네트워킹** 섹션에는 아래와 같이 VM과 연결된 리소스 그룹, 서브넷 및 네트워크 인터페이스에 대한 계층적 뷰가 있습니다.

![네트워크 트리](./media/security-center-monitoring/security-center-monitoring-fig121-new.png)

이 섹션은 [리소스 관리가 기반 VM과 클래식 VM](resource-manager-deployment-model.md)을 나누는데, 이것은 Azure 서비스 관리(ASM/클래식) 또는 Azure 리소스 관리(ARM) 네트워킹 기능을 가상 컴퓨터에서 사용할 수 있게 되면 신속하게 파악하는 데 도움이 됩니다. 이 위치에서 네트워크 인터페이스 카드의 속성에 액세스하려면 서브넷을 확장하고 VM 이름을 클릭해야 합니다. 리소스 관리자 기반 VM에 대해 이 작업을 수행하면 아래와 유사한 새 블레이드가 표시됩니다.

![네트워크 트리](./media/security-center-monitoring/security-center-monitoring-fig13-new.png)

이 블레이드에는 네트워크 인터페이스 카드 및 그에 대한 현재 권장 사항의 요약 정보가 표시됩니다. 선택한 VM이 클래식 VM이면 다른 옵션을 포함하는 새 블레이드가 아래와 같이 나타납니다.

![ACL](./media/security-center-monitoring/security-center-monitoring-fig14-new.png)

이 블레이드를 사용하여 공용 포트, 개인 포트를 변경할 수 있고 VM에 대한 ACL을 만들 수 있습니다.

###SQL
**리소스 상태** 타일에서 **SQL**을 클릭하면 SQL 블레이드가 열리고 감사가 활성화되지 않음, 투명한 데이터 암호화가 활성화되지 않음, 데이터베이스의 일반 성능 상태 등의 문제와 관련된 권장 사항이 표시됩니다.

![SQL 리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig15-new.png)

이러한 권장 사항을 클릭하면 자세한 정보를 확인하고 문제 해결을 위한 추가 조치를 취할 수 있습니다. 아래 예에는 **Database Auditing not enabled**(데이터베이스 감사 활성화되지 않음) 권장 사항의 확장이 있습니다.

![SQL 리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig16-new.png)

**SQL 데이터베이스에 대한 감사 활성화** 블레이드에는 SQL 데이터베이스, SQL 데이터베이스가 있는 서버, 설정이 서버로부터 상속되었는지 아니면 이 데이터베이스 고유의 설정인지 여부, 이 문제의 현재 상태 및 심각도가 나열됩니다. 권장 사항을 처리하기 위하여 데이터베이스를 클릭하면 **감사 및 위협 감지** 블레이드가 아래와 같이 열립니다.

![SQL 리소스 상태](./media/security-center-monitoring/security-center-monitoring-fig17-new.png)

이 기능을 활성화하려면 **감사** 옵션 아래에서 **켜기**를 선택하고 **저장**을 클릭합니다.

###응용 프로그램
Azure 워크로드에서 응용 프로그램이 [리소스 관리자 VM](resource-manager-deployment-model.md)에 있고 웹 포트(TCP 포트 80 및 443)가 노출된 경우 Azure 보안 센터에서 응용 프로그램을 모니터링하여 잠재적 보안 문제를 파악하여 해결 단계를 추천할 수 있습니다. **응용 프로그램** 타일을 클릭하면 아래와 같이 **응용 프로그램** 블레이드가 열리고 방지 단계 섹션에 일련의 권장 사항이 표시됩니다. 또한 호스트/가상 IP별 응용 프로그램 분석 결과가 표시됩니다.

![응용프로그램 보안 상태](./media/security-center-monitoring/security-center-monitoring-fig18-new.png)

이전 리소스에 대한 다른 권장 사항과 마찬가지로, 권장 사항을 클릭하여 문제에 대한 자세한 정보와 해결 방법을 확인할 수 있습니다. 아래 그림의 예는 **안전하지 않은 웹 응용 프로그램**으로 식별된 응용 프로그램입니다. 안전하지 않은 것으로 간주되는 응용 프로그램을 선택하면 또 다른 블레이드가 열리고 다음과 같은 옵션이 제공됩니다.

![앱](./media/security-center-monitoring/security-center-monitoring-fig19-new.png)

**Unsecured Web Applications**(안전하지 않은 웹 응용 프로그램) 블레이드에는 안전하지 않다고 간주되는 응용 프로그램을 포함하는 모든 VM이 나열됩니다. 목록에는 VM 이름, 문제의 현재 상태 및 심각도가 표시됩니다. 이 웹 응용 프로그램을 클릭하면 **웹 응용 프로그램 방화벽 추가** 블레이드가 열리고 타사 WAF(웹 응용 프로그램 방화벽)를 설치할 수 있는 옵션이 아래와 같이 표시됩니다.

![WAF 추가](./media/security-center-monitoring/security-center-monitoring-fig20-new.png)

## 다음 단계
이 문서에서는 Azure 보안 센터의 모니터링 기능을 사용하는 방법을 살펴보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 정책 설정](security-center-policies.md) - Azure 보안 센터에서 보안 설정을 구성하는 방법 알아보기
- [Azure 보안 센터에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기

<!---HONumber=AcomDC_0128_2016-->