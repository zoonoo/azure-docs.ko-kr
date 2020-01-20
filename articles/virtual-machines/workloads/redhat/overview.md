---
title: Azure의 Red Hat 워크로드 개요 | Microsoft Docs
description: Azure에서 제공되는 Red Hat 제품에 대해 알아보기
services: virtual-machines-linux
author: asinn826
manager: borisb2015
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: overview
ms.date: 12/18/2019
ms.author: alsin
ms.openlocfilehash: 8ca249a5f6c300a39548e4e16927d7a20acae1a8
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942621"
---
# <a name="red-hat-workloads-on-azure"></a>Azure의 Red Hat 워크로드
Red Hat 워크로드는 Azure의 다양한 제품을 통해 지원됩니다. RHEL(Red Hat Enterprise Linux) 이미지는 RHUI(Red Hat Update Infrastructure)와 마찬가지로 RHEL 워크로드의 핵심입니다.

## <a name="red-hat-enterprise-linux-rhel-images"></a>RHEL(Red Hat Enterprise Linux) 이미지
Azure는 Azure에 대한 다양한 RHEL 이미지 제품을 제공합니다. 이러한 이미지는 종량제(PAYG)와 BYOS(bring-your-own-subscription)라는 두 가지 라이선스 모델을 통해 사용할 수 있습니다. Azure에 대한 새 RHEL 이미지는 새 RHEL 버전이 수명 주기 전체에 걸쳐 필요에 따라 출시되고 업데이트될 때 게시됩니다.

### <a name="pay-as-you-go-payg-images"></a>PAYG(종량제) 이미지
Azure는 다양한 RHEL PAYG 이미지를 제공합니다. 이러한 이미지는 RHEL에 대한 적절한 자격이 부여된 상태로 제공되며 업데이트 원본(Red Hat Update Infrastructure)에 연결됩니다. 이러한 이미지는 RHEL 자격 및 업데이트에 대한 프리미엄 요금이 적용됩니다. RHEL PAYG 이미지 변형에는 다음이 포함됩니다.
* 표준 RHEL
* RHEL for SAP
* 고가용성 및 업데이트 서비스를 갖춘 RHEL for SAP

적절한 수의 구독 요금을 별도로 결제하는 번거로움을 피하려면 PAYG 이미지를 사용하면 됩니다.

### <a name="red-hat-gold-images-rhel-byos"></a>Red Hat Gold 이미지(`rhel-byos`)
Azure는 Red Hat Gold 이미지도 제공합니다. 이러한 이미지는 기존 Red Hat 구독을 보유하 고 있으며 Azure에서 기존 구독을 사용하려는 고객에게 유용할 수 있습니다. Azure에서 사용하려면 먼저 Red Hat Cloud Access에 대한 기존 Red Hat 구독을 활성화해야 합니다. 이러한 이미지에 대한 액세스 권한은 Red Hat 구독을 Cloud Access에 사용하도록 설정하고 자격 요구 사항을 충족할 때 자동으로 부여됩니다. 이러한 이미지를 사용하면 고객은 PAYG 이미지를 사용할 때 발생할 수 있는 이중 청구를 방지할 수 있습니다.
* [Azure를 사용하여 Cloud Access에 Red Hat 구독을 사용하도록 설정하는 방법 알아보기](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)
* [Azure Portal, CLI 또는 PowerShell Cmdlet에서 Red Hat Gold 이미지를 찾는 방법 알아보기](./byos.md)

> [!NOTE]
> 이중 청구에 대한 참고 사항: 이중 청구는 사용자가 RHEL 구독 요금을 두 번 결제할 때 발생합니다. 고객이 구독 관리자를 사용하여 RHEL PAYG VM에 대한 자격을 연결할 때 주로 발생합니다. 예를 들어 구독 관리자를 사용하여 RHEL PAYG 이미지에 SAP 패키지에 대한 자격을 연결하는 고객은 PAYG 프리미엄 요금을 통해 한 번, SAP 구독을 통해 다시 한 번 요금을 결제하기 때문에 간접적으로 이중 청구됩니다. BYOS 이미지 사용자에게는 이 문제가 발생하지 않습니다.

## <a name="red-hat-update-infrastructure-rhui"></a>RHUI(Red Hat Update Infrastructure)
Azure는 PAYG RHEL VM(가상 머신)에만 Red Hat Update Infrastructure를 제공합니다. RHUI는 실제로는 Red Hat CDN의 미러이지만 Azure PAYG RHEL VM에만 액세스할 수 있습니다. 배포한 RHEL 이미지에 따라 적절한 패키지에 액세스할 수 있습니다. 예를 들어 RHEL for SAP 이미지는 기본 RHEL 패키지 외에도 SAP 패키지에 액세스할 수 있습니다.

### <a name="rhui-update-behavior"></a>RHUI 업데이트 동작
RHUI에 연결된 RHEL 이미지는 기본적으로 `yum update`가 실행되면 RHEL의 최신 부 버전으로 업데이트됩니다. 이 동작은 `yum update` 작업이 실행될 경우 RHEL 7.4 VM이 RHEL 7.7로 업그레이드될 수 있다는 것을 의미입니다. 이것은 RHUI의 설계 의도입니다. 하지만 일반 RHEL 리포지토리에서 [EUS(Extended Update Support) 리포지토리](./redhat-rhui.md#rhel-eus-and-version-locking-rhel-vms)로 전환하여 이 업그레이드 동작을 완화할 수 있습니다.

## <a name="next-steps"></a>다음 단계
* [Azure의 RHEL 이미지](./redhat-images.md)에 대한 자세한 정보
* [Red Hat Update Infrastructure](./redhat-rhui.md)에 대한 자세한 정보
* [Red Hat Gold 이미지(`rhel-byos`) 제품](./byos.md)에 대한 자세한 정보
