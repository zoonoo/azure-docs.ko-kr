---
title: "Azure Security Center에서 적응 응용 프로그램 컨트롤 | Microsoft Docs"
description: "이 문서에서는 Azure Security Center에서 적응 응용 프로그램 컨트롤을 사용하여 Azure VM에서 실행되는 응용 프로그램의 허용 목록을 나열하는 방법이 설명되어 있습니다."
services: security-center
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2017
ms.author: yurid
ms.translationtype: HT
ms.sourcegitcommit: 890acae2aebf7684e567b9b49377ca7b6da95245
ms.openlocfilehash: 9c3a9a7255bbbdab8f4c356eb07022d7f1d242d7
ms.contentlocale: ko-kr
ms.lasthandoff: 09/20/2017

---
# <a name="adaptive-application-controls-in-azure-security-center-preview"></a>Azure Security Center에서 적응 응용 프로그램 컨트롤(미리 보기)
이 연습을 통해 Azure Security Center에서 응용 프로그램 컨트롤을 구성하는 방법에 대해 알아봅니다.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Security Center의 적응 응용 프로그램 컨트롤이란 무엇입니까?
적응 응용 프로그램 컨트롤은 Azure의 VM에서 실행할 수 있는 응용 프로그램과 여러 장점 중 맬웨어에 대해 VM을 강화하는 데 유리한 장점을 선택할 수 있습니다. Security Center는 기계 학습을 통해 VM에서 실행 중인 프로세스를 분석하고 이러한 인텔리전스를 사용하여 허용 목록 규칙을 적용할 수 있습니다. 이 기능은 응용 프로그램 허용 목록의 구성 및 유지 관리 프로세스를 상당히 단순화하며, 다음을 수행할 수 있도록 합니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 응용 프로그램 실행 시도를 방지하거나 경고합니다.
- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.
- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.
- 오래되고 지원되지 않는 앱을 실행하지 않습니다.
- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.
- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

> [!NOTE]
> 적응 응용 프로그램 컨트롤은 제한된 공개 미리 보기로서 Azure Security Center Standard 고객이 사용할 수 있습니다. 구독 ID를 [당사](mailto:ASC_appcontrol@microsoft.com)로 전자 메일을 보내어 미리 보기에 가입하세요.

## <a name="how-to-enable-adaptive-application-controls"></a>적응 응용 프로그램 컨트롤을 사용하는 방법은 무엇입니까?
적응 응용 프로그램 컨트롤은 구성된 리소스 그룹에서 실행되도록 허용하는 응용 프로그램의 집합을 정의할 수 있습니다. 이 기능은 Windows 컴퓨터(모든 버전, 클래식 또는 Azure Resource Manager)에서만 사용할 수 있습니다. Security Center에서 응용 프로그램 허용 목록을 구성하려면 아래 단계를 따릅니다.

1.  **Security Center** 대시보드를 열고 **개요**를 클릭합니다.
2.  **고급 클라우드 방어** 아래에서 **적응 응용 프로그램 컨트롤** 타일은 현재 제 위치에서 몇 대의 VM을 제어할 수 있는지 모든 VM과 비교하여 보여줍니다. 또한 지난 주에 발견된 문제의 수도 보여줍니다. 

    ![적응 응용 프로그램 컨트롤](./media/security-center-adaptive-application\security-center-adaptive-application-fig1.png)

3. **적응 응용 프로그램 컨트롤** 타일을 클릭하면 더 많은 옵션을 볼 수 있습니다.

    ![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

4. 리소스 그룹 섹션에는 세 개의 탭이 있습니다.
    * **권장**: 응용 프로그램 컨트롤이 권장되는 리소스 그룹의 목록입니다. Security Center는 기계 학습을 사용하여 VM이 동일한 응용 프로그램을 일관되게 실행하는지 여부에 따라 어떤 VM이 응용 프로그램을 제어하는 데 좋은 후보인지 식별합니다.
    * **구성됨**: 응용 프로그램 컨트롤로 구성된 VM을 포함하는 리소스 그룹의 목록입니다. 
    * **권장 사항 없음**: 응용 프로그램 컨트롤 권장 사항이 없는 VM을 포함하는 리소스 그룹의 목록입니다. 예를 들어 응용 프로그램이 항상 변경되어 안정적인 상태에 도달하지 않았던 VM이 있습니다.

이어지는 섹션은 각 옵션에 대한 자세한 내용과 사용 방법이 나와 있습니다.

### <a name="configure-a-new-application-control-policy"></a>새 응용 프로그램 컨트롤 정책 구성
**권장** 탭을 클릭하여 응용 프로그램 컨트롤 권장 사항이 있는 리소스 그룹의 목록을 확인합니다.

![권장](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

목록에는 다음이 포함됩니다.
- **이름**: 구독 또는 리소스 그룹의 이름
- **VM**: 리소스 그룹에 있는 가상 컴퓨터의 수
- **상태**: 권장의 상태로 대부분의 경우 열려 있음
- **심각도**: 권장의 심각도 수준

리소스 그룹을 선택하여 **응용 프로그램 컨트롤 규칙 만들기** 옵션을 엽니다.

![응용 프로그램 컨트롤 규칙](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

**VM 선택**에서 권장된 VM 목록을 검토하고 응용 프로그램 컨트롤을 적용하지 않으려는 것을 선택 취소합니다. **허용 목록 규칙에 대한 프로세스 선택**에서 권장된 응용 프로그램의 목록을 검토하고 적용하지 않으려는 것을 선택 취소합니다. 목록에는 다음이 포함됩니다.

- **이름**: 전체 응용 프로그램 경로
- **프로세스**: 모든 경로 내에 있는 응용 프로그램 수
- **일반**: true는 이러한 프로세스가 리소스 그룹에 있는 대부분의 VM에서 실행되었음을 나타냅니다.
- **악용 가능**: 경고 아이콘은 응용 프로그램 허용 목록을 우회하기 위해 공격자가 사용할 수 응용 프로그램임을 나타냅니다. 승인하기 전에 이들 응용 프로그램을 검토하는 것이 좋습니다. 

선택이 완료되면 **만들기** 단추를 클릭합니다. 기본적으로 Security Center는 항상*감사* 모드에서 응용 프로그램 컨트롤을 통해 사용할 수 있습니다. 허용 목록이 워크로드에 부정적인 영향을 주지 않는 것으로 확인되면 *적용* 모드로 변경할 수 있습니다.

> [!NOTE]
> 보안 모범 사례는 Security Center가 항상 허용 목록에 있는 응용 프로그램에 대한 게시자 규칙을 만들려고 시도하고, 응용 프로그램에 게시자 정보가 없는 경우에만(즉, 서명되지 않음) 특정 EXE의 전체 경로에 대해 경로 규칙을 만드는 것입니다.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>응용 프로그램 컨트롤을 사용하여 구성된 그룹의 편집 및 모니터링

응용 프로그램 컨트롤을 사용하여 구성된 그룹을 편집 및 모니터링하려면 **리소스 그룹** 아래에 있는 **구성됨**을 클릭합니다.

![리소스 그룹](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

목록에는 다음이 포함됩니다.

- **이름**: 구독 또는 리소스 그룹의 이름
- **VM**: 리소스 그룹에 있는 가상 컴퓨터의 수
- **모드**: 감사 모드는 허용 목록에 없는 응용 프로그램을 실행하려 할 것입니다. 차단은 허용 목록에 없는 응용 프로그램이 실행되지 않도록 합니다.
- **심각도**: 권장의 심각도 수준

**응용 프로그램 컨트롤 정책 편집** 페이지에서 리소스 그룹을 선택하여 변경합니다.

![보호](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

**보호 모드** 아래에서 다음 옵션 중 하나를 선택할 수 있습니다.
- **감사**: 이 모드에서 응용 프로그램 컨트롤 솔루션은 규칙을 적용하지 않고 보호되는 VM에서의 작업만 감사합니다. 이는 대상 VM에서 앱이 실행되는 것을 차단하기 전에 먼저 전반적인 동작을 관찰하려는 시나리오에 권장됩니다.
- **적용**: 이 모드에서 응용 프로그램 컨트롤 솔루션은 규칙을 적용하고 실행이 허용되지 않은 응용 프로그램을 차단합니다. 

앞서 언급한 것처럼 기본적으로 새 응용 프로그램 컨트롤 정책은 항상 *감사* 모드에서 구성됩니다. **정책 확장** 아래에서 허용 목록에 사용자 고유의 응용 프로그램 경로를 추가할 수 있습니다. 이러한 경로를 추가하면 Security Center는 이미 있는 규칙 외에도 이들 응용 프로그램에 대해 적절한 규칙을 만듭니다. **문제** 섹션에는 현재 위반이 나열됩니다.

![문제](./media/security-center-adaptive-application/security-center-adaptive-application-fig7.png)

이 목록에는 다음이 포함됩니다.

- **문제**: 기록된 위반으로, 다음이 포함됩니다.
    - **ViolationsBlocked**: 적용 모드에서 솔루션이 켜진 경우 허용되지 않는 응용 프로그램이 실행되려 합니다.
    - **ViolationsAudited**: 감사 모드에서 솔루션이 켜진 경우 허용되지 않는 응용 프로그램이 실행됩니다.
    - **RulesViolatedManually**: 사용자가 ASC 관리 포털을 통해서가 아닌 VM에서 규칙을 수동으로 구성하려고 하는 경우입니다.
- **아니요  VM 수**: 이러한 문제 유형이 있는 가상 컴퓨터의 수입니다.

이러한 줄을 하나씩 클릭하면 이러한 유형의 위반이 있는 모든 VM에 대한 정보를 볼 수 있는 [Azure Activity Log](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) 페이지로 리디렉션됩니다. 각 줄의 끝에 있는 세 점을 클릭하면 해당 특정 항목을 삭제할 수 있습니다. **가상 컴퓨터 구성** 섹션에는 이러한 규칙이 적용된 VM이 나열되어 있습니다. 

![구성된 가상 컴퓨터](./media/security-center-adaptive-application/security-center-adaptive-application-fig8.png)

**게시자 허용 목록 규칙**은 각 응용 프로그램에 대해 발견된 인증서 정보에 따라 게시자 규칙이 생성된 응용 프로그램을 나열합니다. 자세한 정보는 [Applocker의 게시자 규칙 이해](https://docs.microsoft.com/windows/device-security/applocker/understanding-the-publisher-rule-condition-in-applocker)를 참조하세요.

![허용 목록 규칙](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

각 줄의 끝에 있는 세 점을 클릭하면 특정 규칙을 삭제할 수 있습니다. **허용 목록 규칙 경로**은 디지털 인증서로 서명되지 않았지만, 여전히 허용 목록 규칙에 있는 응용 프로그램에 대한 전체 응용 프로그램 경로(실행 파일 포함)를 나열합니다. 

> [!NOTE]
> 기본적으로 보안 모범 사례는 Security Center가 항상 허용 목록에 있는 EXE에 대한 게시자 규칙을 만들려고 시도하고, EXE에 게시자 정보가 없는 경우에만(즉, 서명되지 않음) 특정 EXE의 전체 경로에 대해 경로 규칙을 만드는 것입니다.

![경로 허용 목록 규칙](./media/security-center-adaptive-application/security-center-adaptive-application-fig10.png)

목록에는 다음이 포함되어 있습니다.
- **이름**: 실행 파일의 전체 패치
- **악용 가능**: true는 응용 프로그램 허용 목록을 우회하기 위해 공격자가 사용할 수 응용 프로그램임을 나타냅니다.  

각 줄의 끝에 있는 세 점을 클릭하면 특정 규칙을 삭제할 수 있습니다. 변경한 후 **저장** 단추를 클릭하거나 변경 사항을 적용하지 않으려면 **취소**를 클릭합니다.

### <a name="not-recommended-list"></a>권장되지 않음 목록

Security Center는 안정적인 응용 프로그램의 집합을 실행하는 가상 컴퓨터를 위해 응용 프로그램 허용 목록만 권장합니다. 관련된 VM의 응용 프로그램이 계속 변경되는 경우 권장 사항이 생성되지 않습니다. 

![권장 사항](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

목록에는 다음이 포함되어 있습니다.
- **이름**: 구독 또는 리소스 그룹의 이름.
- **VM**: 리소스 그룹에 있는 가상 컴퓨터의 수.

## <a name="see-also"></a>참고 항목
이 문서에서는 Azure Security Center에서 적응 응용 프로그램 컨트롤을 사용하여 Azure VM에서 실행되는 응용 프로그램의 허용 목록을 나열하는 방법을 살펴보았습니다. Azure 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure Security Center 문제 해결 가이드](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다. 
* [Azure Security Center FAQ](security-center-faq.md)로 설정합니다. 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](http://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.


