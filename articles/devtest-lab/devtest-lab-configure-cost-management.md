<properties
	pageTitle="월간 예상 비용 추세 | Microsoft Azure"
	description="DevTest 랩의 월간 예상 비용 추세 차트에 대해 알아봅니다."
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/01/2016"
	ms.author="tarcher"/>

# Monthly estimated cost trend(월간 예상 비용 추세)

## 개요

DevTest Labs의 비용 관리 기능은 랩의 비용을 추적하는 데 도움이 됩니다. 이 문서는 **Monthly Estimated Cost Trend**(월간 예상 비용 추세) 차트를 사용하여 이번 달의 현재 예상 비용 합계 뿐만 아니라 이번 달의 월말 추정 비용을 보는 방법을 보여줍니다.

## Monthly Estimated Cost Trend(월간 예상 비용 추세) 차트 보기

Monthly Estimated Cost Trend(월간 예상 비용 추세) 차트를 보려면 다음 단계를 수행합니다.

1. [Azure 포털](http://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. 선택 **찾아보기**, 를 선택한 다음 **DevTest 실습** 목록에서 합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. **설정**을 선택합니다.

	![설정](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. 랩의 **설정** 블레이드의 **Cost Management**(비용 관리) 아래에서 **Thresholds**(임계값)를 누릅니다.

	![메뉴](./media/devtest-lab-configure-cost-management/menu.png)
 
1. 다음 스크릿샷은 비용 차트의 예를 보여줍니다.

    ![비용 차트](./media/devtest-lab-configure-cost-management/graph.png)

**Estimated Cost**(예상 비용) 값은 이번 달의 현재 예상 비용이며 **Projected Cost**(추정 비용)는 지난 5일 동안의 랩 비용을 사용하여 계산한 이번 달 전체에 대한 예상 비용입니다.
 
금액은 다음 정수로 올림됩니다. 예:

- 5\.01은 6으로 올림됩니다. 
- 5\.50은 6으로 올림됩니다.
- 5\.99는 6으로 올림됩니다.

차트에 언급되어 있듯이, 차트에 보이는 비용은 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 제안 요율을 사용하여 *예상된* 비용입니다. 또한 다음 항목은 비용 계산에 포함되지 *않습니다*.

- DevTest 랩에서는 [Azure 청구 API](../billing-usage-rate-card-overview.md)를 사용하여 랩 비용을 계산하며 Azure 청구 API는 CSP 또는 Dreamspark 구독을 지원하지 않으므로 CSP 및 Dreamspark 구독은 현재 지원되지 않습니다.
- 사용자에 대한 제안 요율. 현재는 Microsoft 또는 Microsoft 파트너와 협의된 사용자에 대한 제안 요율(구독 아래 표시되는)을 사용할 수 없습니다. 종량제 요율을 사용합니다.
- 사용자 세금
- 사용자 할인
- 사용자의 청구 통화. 현재는, 랩 비용이 USD 통화로만 표시됩니다.

## 다음 단계

다음으로 수행해 볼 작업은 아래와 같습니다.

- [랩 정책 정의](./devtest-lab-set-lab-policy.md) – 랩 및 그에 속한 VM이 사용되는 방식을 관리하는 데 사용되는 다양한 정책을 설정하는 방법을 알아봅니다. 
- [사용자 지정 이미지 만들기](./devtest-lab-create-template.md) - VM을 만들 때는 사용자 지정 이미지 또는 마켓플레이스 이미지 중에서 기준을 지정합니다. 이 문서에는 VHD 파일에서 사용자 지정 이미지를 만드는 방법이 나와 있습니다.
- [마켓플레이스 이미지 구성](./devtest-lab-configure-marketplace-images.md) - DevTest Lab에서는 Azure 마켓플레이스 이미지를 기준으로 새 VM을 만들 수 있습니다. 이 문서에서는 랩에서 새 VM을 만들 때 사용할 수 있는 Azure 마켓플레이스 이미지(있는 경우)를 지정하는 방법을 보여 줍니다.
- [랩에서 VM 만들기](./devtest-lab-add-vm-with-artifacts.md) - 기본 이미지에서 새 VM을 만드는 방법(사용자 지정 또는 마켓플레이스) 및 VM의 아티팩트 작업 방법에 대해 설명합니다.

<!---HONumber=AcomDC_0608_2016-->