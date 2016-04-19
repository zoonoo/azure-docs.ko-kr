<properties
	pageTitle="Azure 자동화를 사용하여 Azure 가상 컴퓨터를 수직으로 확장 | Microsoft Azure"
	description="Azure 자동화를 사용하여 모니터링 경고에 대한 응답으로 가상 컴퓨터를 수직으로 확장하는 방법"
	services="virtual-machines"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="singhkay"/>

# Azure 자동화를 사용하여 Azure 가상 컴퓨터를 수직으로 확장

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]클래식 배포 모델

수직 확장은 워크로드에 응답하여 컴퓨터의 리소스를 늘리거나 줄이는 프로세스입니다. Azure에서는 가상 컴퓨터의 크기를 변경하여 이를 수행할 수 있습니다. 이는 다음과 같은 시나리오에 유용합니다.

- 가상 컴퓨터가 자주 사용되지 않는 경우 크기를 줄여 월별 비용을 절감할 수 있습니다.
- 가상 컴퓨터에서 최대 부하가 발생하는 경우 크기를 늘려 용량을 증가시킬 수 있습니다.

이 작업을 수행하는 개략적인 단계는 다음과 같습니다.

1. 가상 컴퓨터에 액세스하도록 Azure 자동화 설정
2. 구독으로 Azure 자동화 수직 확장 runbook 가져오기
3. Runbook에 Webhook 추가
4. 가상 컴퓨터에 경고 추가

> [AZURE.NOTE] 첫 번째 가상 컴퓨터의 크기로 인해 확장할 수 있는 크기가 제한될 수 있습니다. 이는 현재 가상 컴퓨터가 배포된 클러스터에서 다른 크기의 가용성 때문입니다. 이 문서에서 사용된 게시된 자동화 runbook에서는 이 점을 염두에 두고 VM 크기 쌍 이내에서만 확장합니다. 따라서 Standard\_D1v2 가상 컴퓨터가 갑자기 Standard\_G5로 확장되거나 Basic\_A0으로 축소되지 않습니다.

>| VM 크기 조정 쌍 | |
|---|---|
| Basic\_A0 | Basic\_A4 |
| Standard\_A0 | Standard\_A4 |
| Standard\_A5 | Standard\_A7 |
| Standard\_A8 | Standard\_A9 |
| Standard\_A10 | Standard\_A11 |
| Standard\_D1 | Standard\_D4 |
| Standard\_D11 | Standard\_D14 |
| Standard\_DS1 | Standard\_DS4 |
| Standard\_DS11 | Standard\_DS14 |
| Standard\_D1v2 | Standard\_D5v2 |
| Standard\_D11v2 | Standard\_D14v2 |
| Standard\_G1 | Standard\_G5 |
| Standard\_GS1 | Standard\_GS5 |

## 가상 컴퓨터에 액세스하도록 Azure 자동화 설정

이 섹션에서는 다음 작업을 수행합니다.

* Active Directory에서 사용자 만들기
* 사용자의 로그인 정보를 사용하여 AutomationPSCredential 만들기
* 구독의 리소스에 액세스하도록 사용자 설정

구독에서 Azure 자동화 runbook을 실행하려면 먼저 구독에 Azure 자동화 액세스 권한을 제공해야 합니다. 이렇게 하려면 Active Directory에서 다른 사용자를 만들어야 합니다. 그런 다음 사용자가 Azure에 인증할 수 있는 AutomationPSCredential을 만들고 가상 컴퓨터의 크기를 조정하는 PowerShell 명령을 실행해야 합니다.

사용자 및 AutomationPSCredential을 만드는 연습은 다음 문서에서 확인할 수 있습니다.

* [Azure 자동화 구성](../automation/automation-configuring.md)

사용자를 만든 후에는 이 사용자를 클래식 리소스의 공동 관리자로 지정하고 Azure Resource Manager 리소스에 대한 "소유자" 역할을 부여해야 합니다.

클래식 리소스에 액세스할 수 있도록 하려면 클래식 포털에서 사용자를 공동 관리자로 추가해야 합니다.

![이전 포털 공동 관리자](./media/virtual-machines-vertical-scaling-automation/old-portal-automation-user.png)

사용자가 Azure Resource Manager 가상 컴퓨터에 액세스할 수 있도록 하려면 Azure 포털을 사용해야 합니다.

![새 포털 관리자](./media/virtual-machines-vertical-scaling-automation/new-portal-automation-user.png)

## 구독으로 Azure 자동화 수직 확장 runbook 가져오기

가상 컴퓨터의 수직 확장에 필요한 runbook은 Azure 자동화 Runbook 갤러리에 이미 게시되어 있습니다. 이를 구독으로 가져와야 합니다. runbook을 가져오는 방법은 다음 문서에서 확인할 수 있습니다.

* [Azure 자동화용 Runbook 및 모듈 갤러리](../automation/automation-runbook-gallery.md)

가져와야 하는 runbook이 아래 이미지에 표시되어 있습니다.

![Runbook 가져오기](./media/virtual-machines-vertical-scaling-automation/scale-runbooks.png)

## Runbook에 Webhook 추가

Runbook을 가져온 후에는 가상 컴퓨터에서 경고를 통해 트리거될 수 있도록 runbook에 Webhook를 추가해야 합니다. Runbook에 대한 Webhook를 만드는 자세한 방법은 여기에서 확인할 수 있습니다.

* [Azure 자동화 Webhook](../automation/automation-webhooks.md)

다음 섹션에서 필요하므로 Webhook 대화 상자를 닫기 전에 Webhook를 복사해야 합니다.

## 가상 컴퓨터에 경고 추가

1. 가상 컴퓨터 설정 선택
2. "경고 규칙" 선택
3. "경고 추가" 선택
4. 경고를 실행할 메트릭 선택
5. 충족된 경우 경고를 실행할 조건 선택
6. 5단계의 조건을 충족하는 임계값 선택
7. 모니터링 서비스에서 5단계와 6단계의 조건 및 임계값을 확인할 기간 선택
8. 이전 섹션에서 복사한 Webhook에 붙여넣기

![가상 컴퓨터 1에 경고 추가](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-1.png)

![가상 컴퓨터 2에 경고 추가](./media/virtual-machines-vertical-scaling-automation/add-alert-webhook-2.png)

<!---HONumber=AcomDC_0406_2016-->