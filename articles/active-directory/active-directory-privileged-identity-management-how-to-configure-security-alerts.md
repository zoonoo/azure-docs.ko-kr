<properties
   pageTitle="보안 경고를 구성하는 방법 | Microsoft Azure"
   description="Azure Privileged Identity Management 확장에 대한 보안 경고를 구성하는 방법 배우기"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/02/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management 에서 보안 경고를 구성하는 방법

## 보안 경고
Azure Privileged Identity Management(PIM)은 사용자의 환경에 의심스럽거나 안전하지 않은 활동이 있을 때 경고를 생성합니다. 경고가 트리거될 때 PIM 대시보드에 표시됩니다. 경고를 선택하여 경고를 트리거하는 사용자 또는 역할을 나열하는 보고서를 확인합니다.

![PIM 대시보드 보안 경고 - 스크린 샷][1]



| 경고 | 트리거 | 권장 사항 |
| ----- | ------- | -------------- |
| **역할이 PIM 외부에서 할당됨** | 관리자가 PIM 인터페이스 외부에서 영구적으로 역할에 할당되었습니다. | 새 역할 할당을 검토합니다. 다른 서비스는 영구 관리자만 할당할 수 있으므로 필요한 경우 그것을 적격 할당으로 변경합니다. |
| **역할이 너무 자주 활성화됨** | 설정에 허용된 시간 내에 동일한 역할을 다시 활성화한 횟수가 너무 많습니다. | 사용자에게 연락하여 역할을 그처럼 많이 활성화한 이유를 알아봅니다. 어쩌면 해당 작업을 끝내기에는 시간 제한이 너무 짧거나 또는 역할을 자동으로 활성화하기 위해 스크립트를 사용할 수도 있습니다. |
| **역할이 활성화를 위해 Multi-Factor Authentication을 필요로 하지 않음** | MFA를 사용하도록 설정되지 않은 역할이 있습니다. | 가장 높은 권한을 가진 역할에 MFA가 필요하지만 모든 역할을 활성화하기 위해 MFA를 사용하도록 적극 권장합니다. |
| **관리자가 권한 있는 역할을 사용 하지 않음** | 최근에 해당 역할을 활성화하지 않은 적합한 관리자가 있습니다. | 더 이상 액세스가 필요 없는 사용자를 확인하기 위해 액세스 검토를 시작합니다. |
| **전역 관리자가 너무 많음** | 권장하는 것보다 전역 관리자가 많습니다. | 많은 수의 전역 관리자가 있을 경우, 사용자가 필요 이상으로 많은 사용 권한을 갖게 될 가능성이 있습니다. 사용자를 낮은 권한의 역할로 이동하거나, 그 중 일부를 영구 할당 대신에 적격 할당으로 역할을 변경합니다. |

## 보안 경고 설정 구성

PIM의 일부 보안 경고를 환경 및 보안 목표에 맞게 사용자 지정할 수 있습니다. 설정 블레이드에 도달하려면 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인하고 대시보드에서 **Azure AD Privileged Identity Management** 타일을 선택합니다.
2. **권한 있는 역할 관리** > **설정** > **경고 설정**을 선택합니다.

    ![보안 경고 설정으로 이동][2]

### "역할이 너무 자주 활성화됨" 경고

사용자가 지정된 기간 내에 동일한 권한 있는 역할을 여러 번 활성화한다면 이 경고가 트리거됩니다. 기간 및 활성화 횟수를 모두 구성할 수 있습니다.

- **활성화 갱신 기간**: 의심스러운 갱신을 추적하는 데 사용할 기간을 일, 시, 분, 초로 지정합니다.

- **활성화 갱신 회수**: 선택한 기간 내에 경고할만하다고 생각되는 활성화 회수를 2~100 사이로 지정합니다. 슬라이더를 이동하거나 텍스트 상자에 숫자를 입력하여 이 설정을 변경할 수 있습니다.


### "전역 관리자가 너무 많음" 경고

PIM은 서로 다른 두 조건이 충족되고, 그 두 조건을 모두 구성할 수 있을 때 이 경고를 트리거합니다. 첫째, 전역 관리자의 특정 임계값에 도달해야 합니다. 둘째, 총 역할 할당의 특정 비율이 전역 관리자여야 합니다. 이런 측정값의 하나만 충족된다면 경고가 표시되지 않습니다.

- **전역 관리자 최소 수**: 안전하지 않다고 생각하는 전역 관리자 수를 2와 100 사이에서 지정합니다.

- **전역 관리자 비율**: 사용자 환경에 안전하지 않는 전역 관리자 비율을 0%과 100% 사이에서 지정합니다.

### “관리자가 권한 있는 역할을 사용 하지 않음” 경고

사용자가 특정 시간 동안 역할을 활성화하지 않는다면 이 경고가 트리거됩니다.

- **일 수**: 사용자가 역할을 활성화하지 않고 지낼 수 있는 일 수를 0 과 100 사이에서 지정합니다.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 다음 단계
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_dash.png
[2]: ./media/active-directory-privileged-identity-management-how-to-configure-security-alerts/PIM_security_settings.png

<!---HONumber=AcomDC_0907_2016-->