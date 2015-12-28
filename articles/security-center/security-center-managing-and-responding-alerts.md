<properties
   pageTitle="Azure 보안 센터에서 보안 경고 관리 및 대응 | Microsoft Azure"
   description="이 문서는 Azure 보안 센터 기능을 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다."
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
   ms.date="12/16/2015"
   ms.author="yurid"/>

# Azure 보안 센터에서 보안 경고 관리 및 대응
이 문서는 Azure 보안 센터 기능을 사용하여 보안 경고를 관리하고 대응하는 데 도움이 됩니다.

> [AZURE.NOTE]이 문서의 정보는 Azure 보안 센터의 미리 보기 버전에 적용됩니다.

## Azure 보안 센터란?
Azure 보안 센터는 Azure 리소스의 보안에 대한 향상된 가시성과 제어권을 통해 위협을 예방하고 감지하며 위협에 대응하는 데 도움이 됩니다. 이는 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

## 보안 경고란?
Azure 보안 센터는 Azure 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 로그 데이터를 수집, 분석 및 통합하여 실제 위협을 감지하고 거짓 긍정을 줄입니다. 우선 순위가 지정된 경고의 목록이 보안 경고로 표시됩니다.

보안 경고 타일을 확인하여 현재 경고를 검토할 수 있습니다. 각 경고에 대한 세부 정보를 보려면 다음 단계를 수행합니다.

1. **Azure 보안 센터** 대시보드에서 **보안 경고** 타일을 봅니다. 

    ![데이터 수집 사용](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1.png)

2.  그래픽의 보안 경고 발생을 클릭하면 **보안 경고** 블레이드가 열리고 아래와 같이 이 경고에 관한 세부 정보가 표시됩니다.
    
    ![데이터 수집 사용](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2.png)

이 블레이드의 아래 부분에 각 경고에 대한 세부 정보가 있습니다. 원하는 열을 클릭하여 각 열에 대해 정보를 정렬할 수 있습니다. 각 열에 대한 정의는 다음과 같습니다.

- **경고**: 경고에 대해 간략히 설명합니다.
- **개수**: 특정 날짜에서 감지된 이 특정 유형의 모든 경고의 수를 나타냅니다.
- **검색 기준**: 경고를 트리거하는 일을 담당하는 서비스입니다.
- **날짜**: 이벤트가 발생한 시기입니다.
- **상태**: 해당 경고에 대한 현재 상태를 표시합니다. 세 가지 유형의 상태가 있습니다.
    - **활성**: 보안 경고가 검색되었습니다.
    - **해제됨**: 사용자가 보안 경고를 해제했습니다. 이 상태는 일반적으로 조사했지만 실제 공격이 아닌 것으로 밝혀졌거나 조사하고 완화된 경고에 사용됩니다.

- **심각도**: 높음, 중간 또는 낮음 심각도 수준을 표시합니다.
  

### 보안 경고에 대응
많은 활동은 대부분의 조직에 대해 예상되는 공격을 나타낼 수 있습니다. 예를 들어 적법한 네트워크 캡처를 수행하는 네트워크 관리자는 몇몇 형태의 공격을 시작하는 사람과 유사하게 나타날 수 있습니다. 다른 경우 잘못 구성된 시스템 때문에 침입 감지 시스템에서 다수의 거짓 긍정이 발생하여 실제 인시던스를 찾기가 더 어려워질 수 있습니다. Azure 보안 센터를 사용하여 보안 경고를 검토한 후 경고의 심각도에 따라 작업을 시작할 수 있습니다.

조치를 취하기 위해 응답할 경고를 선택하면 오른쪽에 새 블레이드가 열리고 아래와 같이 더 자세한 정보가 표시됩니다.

![데이터 수집 사용](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3.png)

이 경우 트리거된 경고는 의심스러운 원격 데스크톱 프로토콜(RDP) 활동을 참조합니다. 첫 번째 열은 공격을 받은 리소스, 이 공격이 감지된 시간, 경고의 상태 및 심각도를 나타냅니다. 이 정보를 검토한 후 공격을 받은 리소스를 클릭하면 새 블레이드가 열리가 아래 예와 같이 다음에 수행할 작업에 대한 더 자세한 제안이 표시됩니다.

![데이터 수집 사용](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig4.png)
  
> [AZURE.NOTE]Azure 보안 센터가 제안하는 수정은 보안 경고에 따라 달라지며 경우에 따라 권장 수정을 구현하기 위해 다른 Azure 기능을 사용해야 할 수 있습니다. 예를 들어 이 공격에 대한 수정은 [네트워크 ACL](virtual-networks-acl.md) 또는 [네트워크 보안 그룹](virtual-networks-nsg.md) 규칙을 사용하여 이 공격을 발생시키고 있는 IP 주소를 블랙리스트에 추가하는 것입니다.

### 보안 경고 관리
날짜, 상태 및 심각도를 기반으로 경고를 필터링할 수 있습니다. 보안 경고 블레이드 아래에서 필터를 클릭하고 아래와 같이 원하는 옵션을 사용하도록 설정합니다.

![데이터 수집 사용](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5.png)

경고 필터링은 대시보드에서 볼 내용의 범위를 좁혀야 하는 시나리오에 유용할 수 있습니다. 예를 들어 시스템에서 잠재적 위반을 조사하는 중에 지난 24시간 동안 발생한 보안 경고를 확인하고자 할 수 있습니다. 대부분의 보안 경고에는 적용해야 하는 권장 사항이 있지만, 어떤 상황에서는 환경의 거짓 긍정 또는 특정 리소스의 예상 행동 때문에 경고를 무시해야 할 수 있습니다. 어떤 경우이든 **해제** 옵션을 사용하여 특정 리소스에 대한 권장 사항을 숨길 수 있습니다.

작업을 해제하려면 리소스를 클릭하여 추가 정보 보기를 적용하거나 권장된 구성을 적용합니다. 작업을 마우스 오른쪽 단추로 클릭하면 아래 이미지와 유사한 **해제** 옵션이 보입니다.

![데이터 수집 사용](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6.png)

경고의 컬렉션에 각 경고의 아주 구체적인 설명과 수정이 있습니다. Azure 보안 센터에 나오는 경고는 공격 시나리오를 기반으로 합니다. 다음에 나오는 공격 시나리오는 Microsoft 엔진에 의해 트리거됩니다.

- **네트워크 데이터에 대한 무작위 공격 감지**: 이 감지는 네트워크 트래픽 데이터에서 학습한 기계 학습 모델을 기반으로 합니다. 
- **끝점 데이터에 대한 무작위 공격 감지**: 이 감지는 Azure 보안 센터의 컴퓨터 로그 쿼리를 기반으로 하며, 이 감지를 사용하여 실패한 시도와 성공적인 시도를 구별할 수 있습니다. 
- **악성 IP와 통신 중인 가상 컴퓨터**: 이 감지는 봇으로 손상되어 봇의 명령 및 제어(C&C) 서버와 통신 중인 컴퓨터를 검색하는 Azure 보안 센터를 기반으로 합니다. 

## 다음 단계
이 문서에서는 Azure 보안 센터에서 보안 정책을 구성하는 방법을 배웠습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

- [Azure 보안 센터에서 보안 상태 모니터링](security-center-monitoring.md) – Azure 리소스의 상태를 모니터링하는 방법 알아보기
- [Azure 보안 센터 FAQ](security-center-faq.md) – 서비스 사용에 관한 질문과 대답 찾기
- [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/) – Azure 보안 및 규정 준수에 관한 블로그 게시물 찾기

<!---HONumber=AcomDC_1217_2015-->