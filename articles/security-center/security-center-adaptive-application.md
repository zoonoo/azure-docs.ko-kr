---
title: Azure Security Center의 적응형 애플리케이션 제어
description: 이 문서는 Azure 보안 센터에서 적응형 응용 프로그램 제어를 사용하여 Azure 컴퓨터에서 실행 중인 응용 프로그램을 화이트리스트에 사용하는 데 도움이 됩니다.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604678"
---
# <a name="adaptive-application-controls"></a>적응 애플리케이션 컨트롤
이 연습을 통해 Azure Security Center에서 애플리케이션 컨트롤을 구성하는 방법에 대해 알아봅니다.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Security Center의 적응형 애플리케이션 제어란 무엇입니까?
적응형 응용 프로그램 제어는 Azure Security Center의 지능형 자동 종단 간 솔루션으로 Azure 및 비Azure 컴퓨터(Windows 및 Linux)에서 실행할 수 있는 응용 프로그램을 제어하는 데 도움이 됩니다. 다른 이점 중, 이 악성 코드에 대 한 컴퓨터를 강화 하는 데 도움이. 보안 센터는 기계 학습을 사용하여 컴퓨터에서 실행되는 응용 프로그램을 분석하고 이 인텔리전스에서 허용 목록을 만듭니다. 이 기능은 응용 프로그램 허용 목록 정책을 구성하고 유지 관리하는 프로세스를 크게 간소화하므로 다음을 수행할 수 있습니다.

- 맬웨어 방지 솔루션에서 누락될 수 있는 것들을 포함하여 악성 애플리케이션 실행 시도를 차단하거나 경고합니다.
- 사용이 허가된 소프트웨어만 사용하도록 규정된 조직의 보안 정책을 준수합니다.
- 사용자 환경에서 사용자 동의 없이 설치된 소프트웨어를 사용하지 않습니다.
- 오래되고 지원되지 않는 앱을 실행하지 않습니다.
- 조직에서 허용되지 않는 특정 소프트웨어 도구를 사용하지 않습니다.
- IT 기술을 사용하여 앱 사용을 통해 중요한 데이터에 대한 액세스를 제어할 수 있습니다.

> [!NOTE]
> 비Azure 및 Linux 컴퓨터의 경우 적응형 응용 프로그램 컨트롤은 감사 모드에서만 지원됩니다.

## <a name="how-to-enable-adaptive-application-controls"></a>적응형 애플리케이션 제어를 사용하는 방법은 무엇입니까?

적응형 응용 프로그램 컨트롤을 사용하면 구성된 컴퓨터 그룹에서 실행할 수 있는 응용 프로그램 집합을 정의할 수 있습니다. 이 기능은 Azure 및 비 Azure Windows(모든 버전, 클래식 또는 Azure 리소스 관리자) 및 Linux 컴퓨터에서 모두 사용할 수 있습니다. 다음 단계를 사용하여 응용 프로그램 허용 목록을 구성합니다.

1. **Security Center** 대시보드를 엽니다.

1. 왼쪽 창의 **고급 클라우드 방어** 아래에서 **적응형 애플리케이션 제어**를 선택합니다.

    [![방어](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

**적응형 애플리케이션 제어** 페이지가 표시됩니다.

![controls](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

**VM** 섹션에는 세 개의 탭이 있습니다.

* **구성됨**: 애플리케이션 제어로 구성된 VM을 포함하는 그룹 목록입니다.
* **권장**: 애플리케이션 제어가 권장되는 그룹 목록입니다. Security Center는 기계 학습을 사용하여 VM이 동일한 애플리케이션을 일관되게 실행하는지 여부에 따라 어떤 VM이 애플리케이션을 제어하는 데 좋은 후보인지 식별합니다.
* **권장 사항 없음**: 애플리케이션 제어 권장 사항이 없는 VM을 포함하는 그룹 목록입니다. 예를 들어 애플리케이션이 항상 변경되어 안정적인 상태에 도달하지 않았던 VM이 있습니다.

> [!NOTE]
> 비슷한 VM이 최적의 권장 애플리케이션 제어 정책을 사용하도록, Security Center에서는 독점적 클러스터링 알고리즘을 사용하여 VM 그룹을 만듭니다.
>
>

### <a name="configure-a-new-application-control-policy"></a>새 애플리케이션 컨트롤 정책 구성

1. 응용 프로그램 제어 권장 사항이 있는 그룹 목록에 대한 **권장** 탭을 선택합니다.

   ![권장](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   이 목록에는 다음과 같은 정보가 포함됩니다.

   - **그룹 이름**: 구독 및 그룹의 이름
   - **VM 및 컴퓨터**: 그룹의 가상 컴퓨터 수
   - **상태**: 권장 사항의 상태
   - **심각도**: 권장 사항의 심각도 수준

2. 그룹을 클릭하여 **애플리케이션 제어 규칙 만들기** 옵션을 엽니다.

   [![애플리케이션 컨트롤 규칙](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. **VM 선택**에서 권장된 VM 목록을 검토하고 애플리케이션 허용 목록 정책을 적용하지 않을 항목을 선택 취소합니다. 다음으로, 다음과 같은 두 개의 목록이 표시됩니다.

   - **권장되는 애플리케이션**: 이 그룹 내 VM에서 자주 사용되는 애플리케이션 목록으로, 실행을 허용할 것을 권장합니다.
   - **더 많은 애플리케이션**: 이 그룹 내 VM에서 사용 빈도가 낮거나 악용 가능한(아래 참조) 것으로 알려진 애플리케이션 목록으로, 검토가 권장됩니다.

4. 각 목록의 애플리케이션을 검토하고 적용하지 않을 애플리케이션을 선택 취소하세요. 각 목록에는 다음과 같은 정보가 포함됩니다.

   - **이름**: 애플리케이션의 인증서 정보 또는 전체 경로입니다.
   - **파일 형식**: 애플리케이션 파일 형식. EXE, 스크립트, MSI 또는 이러한 형식의 모든 순열이 될 수 있습니다.
   - **악용 가능**: 경고 아이콘은 공격자가 응용 프로그램 허용 목록을 우회하기 위해 특정 응용 프로그램을 사용할 수 있는지 를 나타냅니다. 승인 전에 이러한 애플리케이션을 검토하는 것이 좋습니다.
   - **사용자**: 애플리케이션을 실행할 수 있도록 허용하는 것이 좋은 사용자

5. 선택이 완료되면 **만들기**를 선택합니다. <br>
   만들기를 선택하면 Azure 보안 센터에서 Windows 서버(AppLocker)에서 사용할 수 있는 기본 제공 응용 프로그램 허용 목록 솔루션 위에 적절한 규칙을 자동으로 만듭니다.

> [!NOTE]
> - Security Center는 기준을 만들고 VM의 그룹별로 고유한 권장 사항을 채우기 위해 최소 2주의 데이터를 필요로 합니다. Security Center 표준 계층의 새 고객은 먼저 VM의 그룹이 *권장 사항 없음* 탭 아래에 나타나는 동작을 예상해야 합니다.
> - Security Center의 적응형 애플리케이션 제어는 이미 GPO 또는 로컬 보안 정책을 통해 AppLocker 정책이 설정된 VM을 지원하지 않습니다.
> -  보안 모범 사례는 Security Center가 항상 허용할 것으로 선택된 애플리케이션에 대한 게시자 규칙을 만들려고 시도하고, 애플리케이션에 게시자 정보가 없는 경우에만(즉, 서명되지 않음) 특정 애플리케이션의 전체 경로에 대해 경로 규칙을 만드는 것입니다.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>애플리케이션 컨트롤을 사용하여 구성된 그룹의 편집 및 모니터링

1. 응용 프로그램 허용 목록 정책으로 구성된 그룹을 편집하고 모니터링하려면 **적응형 응용 프로그램 컨트롤** 페이지로 돌아가서 **VM 그룹**아래에서 **CONFIGURED를** 선택합니다.

   ![그룹](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   이 목록에는 다음과 같은 정보가 포함됩니다.

   - **그룹 이름**: 구독 및 그룹의 이름
   - **VM 및 컴퓨터**: 그룹의 가상 컴퓨터 수
   - **모드**: 감사 모드는 허용 목록에없는 응용 프로그램을 실행하기 위한 시도를 기록합니다. 적용은 허용 목록에 있지 않으면 응용 프로그램을 실행할 수 없습니다.
   - **경고**: 현재 위반

2. **애플리케이션 제어 정책 편집** 페이지에서 변경할 그룹을 선택합니다.

   ![보호](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. **보호 모드** 아래에서 다음 중 하나를 선택할 수 있습니다.

   - **감사**: 이 모드에서 애플리케이션 컨트롤 솔루션은 규칙을 적용하지 않고 보호되는 VM에서의 작업만 감사합니다. 이는 대상 VM에서 앱이 실행되는 것을 차단하기 전에 먼저 전반적인 동작을 관찰하려는 시나리오에 권장됩니다.
   - **적용**: 이 모드에서 애플리케이션 컨트롤 솔루션은 규칙을 적용하고 실행이 허용되지 않은 애플리케이션을 차단합니다.

   > [!NOTE]
   > -  **적용** 보호 모드는 추가 공지가 있을 때까지 비활성화됩니다.
   > - 앞서 언급한 것처럼 기본적으로 새 애플리케이션 컨트롤 정책은 항상 *감사* 모드에서 구성됩니다. 
   >

4. **정책 확장**에서 허용할 애플리케이션 경로를 추가합니다. 이러한 경로를 추가한 후 Security Center는 선택한 VMS 그룹 내의 VM에서 응용 프로그램 허용 목록 정책을 업데이트하고 이미 준비된 규칙 외에도 이러한 응용 프로그램에 대한 적절한 규칙을 만듭니다.

5. **최근 경고** 섹션에 나열된 현재 위반을 검토합니다. Azure Security Center 내 **경고**로 리디렉션할 각 줄을 클릭하고, 연결된 VM에서 Azure Security Center에 의해 검색된 모든 경고를 봅니다.
   - **경고**: 기록된 위반.
   - **VM의 수**: 이러한 경고 유형이 있는 가상 머신의 수입니다.

6. **게시자 허용 목록 규칙**, **허용 목록 규칙 경로** 및 **허용 목록 규칙 해시**에서 현재 그룹 내 VM에서 구성된 애플리케이션 허용 목록 규칙을 규칙 컬렉션 유형에 따라 확인할 수 있습니다. 각 규칙에 대해 다음을 확인할 수 있습니다.

   - **규칙**: AppLocker에서 어떤 애플리케이션을 검사하는지에 따라 애플리케이션의 실행이 허용되는지 여부를 검사하는 특정 매개 변수입니다.
   - **파일 형식**: 특정 규칙에서 다루는 파일 형식입니다. EXE, 스크립트, MSI 또는 이러한 파일 유형의 모든 순열일 수 있습니다.
   - **사용자**: 애플리케이션 허용 목록 규칙에서 다루는 애플리케이션의 실행이 허용된 사용자의 이름 또는 번호입니다.

   ![허용 목록 규칙](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. 각 줄의 끝에 있는 세 점을 클릭하면 특정 규칙을 삭제하거나 허용된 사용자를 편집할 수 있습니다.

8. **적응형 애플리케이션 제어** 정책을 변경한 후 **저장**을 클릭합니다.

### <a name="not-recommended-list"></a>권장되지 않음 목록

Security Center는 안정적인 애플리케이션의 집합을 실행하는 가상 머신을 위해 애플리케이션 허용 목록 정책만 권장합니다. 관련된 VM의 애플리케이션이 계속 변경되는 경우 권장 사항이 생성되지 않습니다.

![권장](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

목록에는 다음이 포함되어 있습니다.
- **그룹 이름**: 구독 및 그룹의 이름
- **VM 및 컴퓨터**: 그룹의 가상 컴퓨터 수

Azure Security Center를 사용하면 권장되지 않는 VM 그룹에서도 애플리케이션 허용 목록 정책을 정의할 수 있습니다. 해당 그룹에서도 애플리케이션 허용 목록 정책을 구성하려면 이전에 설명한 동일한 원칙을 따릅니다.

## <a name="move-a-vm-from-one-group-to-another"></a>VM을 한 그룹에서 다른 그룹으로 이동

 VM을 한 그룹에서 다른 그룹으로 이동하면 VM을 이동한 그룹의 설정이 변경됩니다. 또한 구성된 그룹에서 구성되지 않은 그룹으로 VM을 이동하여 이전에 VM에 적용된 응용 프로그램 제어 정책을 제거할 수 있습니다.

 1. 적응 **형 응용 프로그램 컨트롤** 페이지에서 **CONFIGURED** 탭에서 이동할 VM이 현재 속한 그룹을 클릭합니다.
1. **구성된 VM 및 컴퓨터를 클릭합니다.**
1. VM 줄에 있는 세 개의 점을 클릭하여 이동하고 **이동을**클릭합니다. **컴퓨터가 다른 그룹 창으로 이동이** 열립니다.

    ![보호](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. 그룹을 선택하여 VM을 이동할 수 있고 **컴퓨터 이동을**클릭하고 **에서 저장을**클릭합니다.

    ![보호](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> **컴퓨터 이동을**클릭한 후 **저장을** 클릭해야 합니다. **저장을**클릭하지 않으면 컴퓨터가 이동되지 않습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center에서 적응형 응용 프로그램 제어를 사용하여 Azure 및 비 Azure VM에서 실행 중인 응용 프로그램을 화이트리스트에 사용하는 방법을 배웠습니다. Azure Security Center에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 경고 관리 및 대응](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts) Security Center에서 경고를 관리하고 보안 인시던트에 대응하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md). Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 이해](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). 다양한 유형의 보안 경고에 대해 알아봅니다.
* [Azure 보안 센터 문제 해결 가이드.](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide) Security Center의 일반적인 문제를 해결하는 방법에 대해 알아봅니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/). Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.
