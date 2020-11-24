---
title: Red Hat Enterprise Linux 연장된 수명 주기 지원
description: Red Hat Enterprise Extended 수명 주기 지원 추가 기능을 추가 하는 방법에 대해 알아봅니다.
author: mathapli
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: 1f31cc982e5e7f92cfe7e8adf588dd96628f2c6f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95744044"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>RHEL (Red Hat Enterprise Linux) 확장 된 수명 주기 지원
이 문서에서는 Red Hat Enterprise 이미지에 대 한 연장 된 수명 주기 지원 정보를 제공 합니다.
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Red Hat Enterprise Linux 6 수명 주기
2020 년 11 월 30 일부 터 Red Hat Enterprise Linux 6은 유지 관리 단계의 끝에 도달 합니다. 유지 관리 단계 다음에는 확장 된 수명 단계가 적용 됩니다. Red Hat Enterprise Linux 6 전체/유지 관리 단계에서 전환 하는 것 처럼 Red Hat Enterprise Linux 7 또는 8로 업그레이드 하는 것이 좋습니다. 고객이 Red Hat Enterprise Linux 6을 유지 해야 하는 경우 Red Hat Enterprise Linux의 ELS (확장 수명 주기 지원) 추가 기능을 추가 하는 것이 좋습니다.

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Marketplace 종 량 제 Vm에서 연장 된 수명 주기 지원을 추가 하는 단계
1. 여기에서 [사용할 수 있는 els 양식을](https://aka.ms/els-form) 고객의 연락처 세부 정보 및 구독 정보로 채웁니다. 추가 가격 책정 세부 정보는 형식 에서도 사용할 수 있습니다.
1. Azure Red Hat Enterprise Linux 팀은 1-2 영업일 이내에 ELS 지원 추가 기능에 대 한 Vm 목록을 사용자에 게 연락 합니다. 목록을 검토 하 고 추가 가격 책정에 동의 하세요.
1. Azure Red Hat Enterprise Linux 팀은 ELS 클라이언트 패키지를 Vm에 추가 하는 단계를 공유 합니다. 전자 메일에 제공 되는 단계에 따라 소프트웨어 유지 관리 (버그 및 보안 수정)를 계속 받고 Red Hat Enterprise Linux 6을 지원 합니다.

> [!Note]
> RHEL ELS를 사용 하는 단계는 조직 외부의 누구와도 공유 하지 마세요. 에 연락 AzureRedHatELS@microsoft.com 하 여 지원 또는 추가 질문을 받으세요.

## <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Red Hat Enterprise Linux 6을 실행 중 이며 지금은 이후 버전으로 마이그레이션할 수 없습니다. 어떤 방법이 있습니까?
* 계속 해 서 Red Hat Enterprise Linux 6을 실행 하 고, 계속 해 서 제한 된 소프트웨어 유지 관리 및 기술 지원을 받을 수 있도록 (확장 수명 주기 지원) Add-On 리포지토리를 구매 합니다 (업그레이드 프로세스 및 가격 정보 참조).
* 가능 하면 즉시 Red Hat Enterprise Linux 7 또는 8로 마이그레이션합니다.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Red Hat Enterprise Linux 확장 된 ELS (수명 주기 지원) 추가 기능을 사용 하기 위한 추가 요금은 무엇 인가요?
연장 된 수명 주기 지원과 관련 된 비용은 [ELS 형식](https://aka.ms/els-form) 으로 찾을 수 있습니다.


## <a name="next-steps"></a>다음 단계

* Azure에서 RHEL 이미지의 전체 목록을 보려면 [azure에서 사용할 수 있는 Red Hat Enterprise Linux (RHEL) 이미지](./redhat-imagelist.md)를 참조 하세요.
* Azure Red Hat 업데이트 인프라에 대해 자세히 알아보려면 [azure에서 주문형 RHEL vm에 대 한 Red Hat 업데이트 인프라](./redhat-rhui.md)를 참조 하세요.
* RHEL BYOS 제품에 대 한 자세한 내용은 [Azure에서 사용자 고유의 구독 골드 이미지 Red Hat Enterprise Linux](./byos.md)를 참조 하세요.
* 모든 버전의 RHEL에 대 한 Red Hat 지원 정책에 대 한 자세한 내용은 [Red Hat Enterprise Linux 수명 주기](https://access.redhat.com/support/policy/updates/errata)를 참조 하세요.