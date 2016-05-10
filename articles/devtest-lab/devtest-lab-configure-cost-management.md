<properties
	pageTitle="비용 관리 구성 | Microsoft Azure"
	description="DevTest Lab 비용 관리 기능을 구성하는 방법을 알아봅니다."
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
	ms.date="04/18/2016"
	ms.author="tarcher"/>

# 비용 관리 구성

## 개요

DevTest Labs의 비용 관리 기능은 랩의 비용을 추적하는 데 도움이 됩니다. 이 문서는 **Monthly Estimated Cost Trend**(월간 예상 비용 추세) 차트를 사용하여 이번 달의 현재 예상 비용 합계 뿐만 아니라 이번 달의 월말 추정 비용을 보는 방법을 보여줍니다.

## Monthly Estimated Cost Trend(월간 예상 비용 추세) 차트 활성화

Monthly Estimated Cost Trend(월간 예상 비용 추세) 차트를 활성화하려면, 다음 단계를 수행합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. **찾아보기**를 탭한 다음 목록에서 **DevTest Lab**을 탭합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.

1. **설정**을 탭합니다.

	![설정](./media/devtest-lab-configure-cost-management/lab-blade-settings.png)

1. 랩의 **설정** 블레이드의 **Cost Policies**(비용 정책) 아래에서 **Cost Thresholds**(비용 임계값)을 탭합니다.

	![메뉴](./media/devtest-lab-configure-cost-management/menu.png)
 
1. **Cost Thresholds**(비용 임계값) 블레이드에서 이 기능을 활성화하려면 **On**(켜기)을, 비활성화하려면 **Off**(끄기)를 탭합니다.

1. **저장**을 탭합니다.

이 기능을 활성화하고 나면, 차트에 예상 비용과 추정 비용이 표시되기까지 몇 시간 정도가 소요될 수 있습니다. 이 정보를 모으는 서비스는 매 시간 실행되지만, 라이브 데이터가 수집되고 몇 시간 뒤에 실행되기 때문입니다. 예를 들어, 오전 1시에 VM을 시작했다고 가정하겠습니다. 이 VM과 관련된 비용은 아마도 한두 시간 동안 차트에 포함되지 않을 것입니다.
 
다음 스크릿샷은 비용 차트의 예를 보여줍니다.

![비용 차트](./media/devtest-lab-configure-cost-management/graph.png)

**Estimated Cost**(예상 비용) 값은 이번 달의 현재 예상 비용이며 **Projected Cost**(추정 비용)는 이번 달 전체에 대한 예상 비용입니다.

차트에 언급되어 있듯이, 차트에 보이는 비용은 [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 제안 요율을 사용하여 *예상된* 비용입니다. 또한 다음 항목은 비용 계산에 포함되지 *않습니다*.

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

<!---HONumber=AcomDC_0427_2016-->