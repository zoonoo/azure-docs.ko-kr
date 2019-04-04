---
title: Red Hat 예약 계획 할인 및 사용-Azure 이해 | Microsoft Docs
description: Virtual machines에서 Red Hat 소프트웨어 Red Hat 계획 할인이 적용 되는 방법에 대해 알아봅니다.
services: billing
documentationcenter: ''
author: yashesvi
manager: yashar
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2019
ms.author: cwatson
ms.openlocfilehash: fe0d0f0baa2b3d1c08e871541dce1511e00f7f87
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917059"
---
# <a name="understand-how-the-red-hat-linux-enterprise-software-reservation-plan-discount-is-applied-for-azure"></a>Azure 용 Red Hat Enterprise 소프트웨어 예약 계획 할인은 적용 하는 방법 이해

Red Hat Linux 계획을 구입 후 할인에 배포 된 Red Hat 가상 머신 (Vm) 예약 일치 하는 자동으로 적용 됩니다. Red Hat Linux 계획을 Azure VM에서 Red Hat 소프트웨어를 실행 하는 비용을 설명 합니다.

Red Hat Linux 플랜을 구입을 실행 하는 어떤 Red Hat Vm 및 해당 Vm의 Vcpu 수를 이해 해야 합니다. 다음 섹션은 사용량 CSV 파일에서 구입할 요금제를 식별하는 데 도움이 됩니다.

## <a name="discount-applies-to-different-vm-sizes"></a>다양 한 VM 크기에 할인이 적용 됩니다.

Reserved VM Instances와 같은 Red Hat 계획 구매 인스턴스 크기 유연성을 제공합니다. 즉, 다른 vCPU 수를 사용하는 VM을 배포할 때도 할인이 적용됩니다. 소프트웨어 요금제 내의 다른 VM 크기에도 할인이 적용됩니다.

할인 금액은 다음 표에 나열된 비율에 따라 달라집니다. 이 비율은 해당 그룹의 각 미터법에 대한 상대적 공간을 비교합니다. 이 비율은 VM vCPU에 따라 달라집니다. Red Hat Linux 계획 할인을 받을 하는 VM 인스턴스 수를 계산 하는 비율 값을 사용 합니다.

예를 들어, 3 또는 4 Vcpu 사용 하 여 VM에 대 한 Red Hat Linux Enterprise Server에 대 한 계획을 구입 하는 경우 해당 예약에 대 한 비율은 2입니다. 할인에 대 한 Red Hat 소프트웨어 비용을 다룹니다.

- 1~2개의 vCPU가 있는 배포된 VM 2개
- 3~4개의 vCPU가 있는 배포된 VM 1개
- 또는 5개 이상의 vCPU가 있는 VM의 0.77 또는 약 77%

5개 이상의 vCPU에 대한 비율은 2.6입니다. 따라서 5 개 이상의 Vcpu 사용 하 여 VM 사용 하 여 Red Hat에 대 한 예약만 부분 77% 정도 소프트웨어 비용을 설명 합니다.

## <a name="understand-red-hat-vm-usage-before-you-buy"></a>구입 하기 전에 Red Hat VM 사용량 이해

다음 표에는 예약을 구입할 수 있는 소프트웨어 요금제, 해당 관련 사용량 미터 및 각각의 비율이 나와 있습니다.

### <a name="red-hat-enterprise-linux"></a>Red Hat Enterprise Linux

Azure Portal Marketplace 이름:

- Red Hat Enterprise Linux 6.7
- Red Hat Enterprise Linux 6.8
- Red Hat Enterprise Linux 6.9
- Red Hat Enterprise Linux 6.10
- Red Hat Enterprise Linux 7.2
- Red Hat Enterprise Linux 7.3
- Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5
- Red Hat Enterprise Linux 7.6
- Red Hat Enterprise Linux 7 (최신 lvm)

|Red Hat VM | MeterId| 비율| 예제 VM 크기|
| -------| ------------------------| --- |--- |
|1-4 vCPU VM 라이선스|077a07bb-20f8-4bc6-b596-ab7211a1e247|1|D4s_v3|
|1-4 vCPU VM 라이선스|2f96d035-3bac-46d6-b2bc-c6daa0938536|1|D4s_v3|
|1-4 vCPU VM 라이선스|4831a7b4-bdd4-48a2-8e95-18d053971ede|1|D4s_v3|
|5 + vCPU VM 라이선스|291b2cbc-6c34-4e2b-a4e4-1ff8c106f672|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|3b6661c4-03dd-45e7-88c9-512fcb7906d5|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|037eddc0-fedd-4d73-b5d8-92fba9edb831|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|432cdeee-4034-4ddf-9ba4-9250a19b0d5f|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|794dcb90-0793-43e6-9909-70d29974e56d|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|86b5b0b4-3c19-4720-82e9-874f8c58b48e|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|86c35ec3-0a48-426a-9625-22d80e6ea55b|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|8b698c7a-47f1-4cba-8ae1-9853d5ad562d|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|a4daffb4-96f4-4fc5-b1e6-fd3a2cf3595e|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|a838cfb1-0bd3-4965-84f0-663f49afc2e2|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|99aed7b9-a0a9-4783-b90c-be7c2f3c7e30|2.166666667|D8s_v3|
|5 + vCPU VM 라이선스|d09f877e-03b4-48b2-b11a-782b965cff19|2.166666667|D8s_v3|
|44 vCPU VM 라이선스|6f44ae85-a70e-44be-83ec-153a0bc23979|2.166666667||
|60 vCPU VM 라이선스|b9edcc5b-a429-4778-bc5a-82e7fa07fe55|2.166666667||

### <a name="red-hat-enterprise-linux-for-sap-with-ha"></a>Red Hat Enterprise Linux for SAP HA 사용 하 여

Azure Portal Marketplace 이름:

|Red Hat VM | MeterId | 비율|예제 VM 크기|
| ------- | --- | ------------------------| --- | --- |
|1-4 vCPU VM 라이선스 |4d902611-eed7-4060-a33e-3c7fdbac6406|1|D4s_v3|
|5 + vCPU VM 라이선스|6dfb482b-23ea-487f-810c-e66360f025de|2.333333333|D8s_v3|

### <a name="red-hat-enterprise-linux-with-ha"></a>Red Hat Enterprise Linux(HA 포함)

Azure Portal Marketplace 이름:

|Red Hat VM | MeterId | 비율|예제 VM 크기|
| ------- |------------------------| --- | --- |
|1-4 vCPU VM 라이선스|e9711132-d9d9-450c-8203-25cfc4bce8de|1|D4s_v3|
|5 + vCPU VM 라이선스|93954aa4-b55f-4b7b-844d-a119d6bf3c4e|2|D8s_v3|

### <a name="rhel-for-sap-business-applications"></a>RHEL for SAP Business Applications

Azure Portal Marketplace 이름:

- SAP 비즈니스 앱에 대 한 Red Hat Enterprise Linux 6.8
- SAP 비즈니스 앱에 대 한 Red Hat Enterprise Linux 7.3
- SAP 용 Red Hat Enterprise Linux 7.4
- Red Hat Enterprise Linux 7.5 for SAP

|Red Hat VM | MeterId | 비율|예제 VM 크기|
| ------- |------------------------| --- |--- |
|1 개의 vCPU VM 라이선스|25889e91-c740-42ac-bc52-6b8f73b98575|1|D2s_v3|
|2 vCPU VM 라이선스|2a0c92c8-23a7-4dc9-a39c-c4a73a85b5da|1|D2s_v3|
|4 vCPU VM 라이선스|875898d3-3639-423c-82c1-38846281b7e8|1|D4s_v3|
|6 개 vCPU VM 라이선스|69a140fa-e08e-415c-85f2-48158e4c73a0|2.166666667||
|8 개 vCPU VM 라이선스|777a5a74-22d6-48c9-9705-ac38fe05a278|2.166666667|D8s_v3|
|vcpu 12 개 VM 라이선스|d6b8917a-5127-497a-9f48-1e959df98812|2.166666667||
|16 vCPU VM 라이선스|03667e82-e009-425a-83f7-8ebddbca5af4|2.166666667|D16s_v3|
|20 vCPU VM 라이선스|bbd65e5b-35f1-42be-b86d-6625fbc1f1a4|2.166666667||
|24 vCPU VM 라이선스|c2c07d3e-a7d0-400b-8832-b532bfd0be25|2.166666667|ND24s|
|32 vCPU VM 라이선스|633d1494-5ec1-46f0-a742-eaf58eeaec7e|2.166666667|D32s_v3|
|40 vCPU VM 라이선스|737142c3-8e4f-4fc1-aa41-05b1661edff8|2.166666667||
|44 vCPU VM 라이선스|722bda73-a8c8-4d04-b96b-541f0bb6c0c4|2.166666667||
|60 vCPU VM 라이선스|a22bb342-ba9a-4529-a178-39a92ce770b6|2.166666667||
|64 vCPU VM 라이선스|d37c8e17-e5f2-4060-881b-080dd4a8c4ce|2.166666667|64s_v3|
|72 vCPU VM 라이선스|14341b96-e92c-4dca-ba66-322c88a79aa6|2.166666667|F72s_v2|
|96 vCPU VM 라이선스|8b2e5cb8-0362-4cbf-a30a-115e8d6dbc49|2.166666667||
|128 vCPU VM 라이선스|9b198a68-974a-47a7-9013-49169ac0f2e9|2.166666667| M128ms|

### <a name="rhel-for-sap-hana"></a>RHEL for SAP HANA

Azure Portal Marketplace 이름:

- Red Hat Enterprise Linux 6.7 for SAP HANA
- Red Hat Enterprise Linux 7.2 for SAP HANA
- Red Hat Enterprise Linux 7.3 for SAP HANA

|Red Hat VM | MeterId | 비율|예제 VM 크기|
| ------- |------------------------| --- |--- |
|1 개의 vCPU VM 라이선스|be0a59d1-eed7-47ec-becd-453267753793|1|D2s_v3|
|2 vCPU VM 라이선스|3b97c9f5-f5d5-4fd3-a421-b78fca32a656|1|D2s_v3|
|4 vCPU VM 라이선스|b39feb58-57bf-40f2-8193-f4fe9ac3dda3|1|D4s_v3|
|6 개 vCPU VM 라이선스|a5963812-0f5a-4053-8ace-2b5babd15ed8|2.166666667||
|8 개 vCPU VM 라이선스|5460ab4d-ce9a-46af-8ad5-ca5e53d715b5|2.166666667|D8s_v3|
|vcpu 12 개 VM 라이선스|0e3bc72d-a888-4bcf-8437-119f763a3215|2.166666667||
|16 vCPU VM 라이선스|b40e95d8-3176-42f0-967c-497785c031b2|2.166666667|D16s_v3|
|20 vCPU VM 라이선스|81f34277-499d-40a3-a634-99adc08e2d45|2.166666667||
|24 vCPU VM 라이선스|e03f1906-d35d-4084-b2cd-63281869c8ee|2.166666667|ND24s|
|32 vCPU VM 라이선스|0a58c082-ceb8-4327-9b64-887c30dddb23|2.166666667|D32s_v3|
|40 vCPU VM 라이선스|a14225c0-04e6-4669-974f-e2ddd61a9c5b|2.166666667||
|44 vCPU VM 라이선스|378b8125-d8a5-4e09-99bc-c1462534ffb0|2.166666667||
|60 vCPU VM 라이선스|5d7db11a-54e9-404e-aaa8-509fac7c0638|2.166666667||
|64 vCPU VM 라이선스|3c8157b2-a57d-45ce-ba02-bd86e9209795|2.166666667|64s_v3|
|72 vCPU VM 라이선스|5e87a3ee-7afb-4040-b8d9-b109ddb38f31|2.166666667|F72s_v2|
|96 vCPU VM 라이선스|b13895fc-0d06-4de9-b860-627c471cd247|2.166666667||
|128 vCPU VM 라이선스|6e67ac0b-19d3-4289-96df-05d0093d4b3b|2.166666667| M128ms|

## <a name="next-steps"></a>다음 단계

예약에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [Azure에 대 한 예약 이란](billing-save-compute-costs-reservations.md)
- [Azure 예약을 사용 하 여 Red Hat 소프트웨어 계획에 대 한 요금을 선불합니다](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure에 대 한 예약 관리](billing-manage-reserved-vm-instance.md)
- [종 량 제 구독에 대 한 예약 사용량 이해](billing-understand-reserved-instance-usage.md)
- [Enterprise 등록의 예약 사용량 이해](billing-understand-reserved-instance-usage-ea.md)

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
