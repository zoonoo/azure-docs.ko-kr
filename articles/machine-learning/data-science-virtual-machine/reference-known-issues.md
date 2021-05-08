---
title: '참조: 알려진 문제 및 문제 해결'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine에 대해 알려진 문제, 해결 방법 및 문제 해결의 목록을 가져옵니다.
services: machine-learning
ms.service: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: bcd75347f91109967ac48427ca0b8855c11b7d9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "101656859"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>Azure Data Science Virtual Machine에 대해 알려진 문제 및 문제 해결

본 문서는 Azure Data Science Virtual Machine을 사용할 때 발생할 수 있는 오류나 실패를 찾아 수정하는 데 도움이 됩니다.

## <a name="python-package-installation-issues"></a>Python 패키지 설치 문제

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Linux에서 pip 중단 종속성이 있는 패키지 설치

패키지 설치 시 `pip install` 대신 `sudo pip install`을 사용합니다.

## <a name="disk-encryption-issues"></a>디스크 암호화 문제

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM에서 디스크 암호화가 실패합니다.

ADE(Azure Disk Encryption)는 현재 Ubuntu DSVM에서 지원되지 않습니다. 문제를 해결하려면 [Azure 관리 디스크의 서버측 암호화](../../virtual-machines/disk-encryption.md)를 구성하는 것이 좋습니다.

## <a name="tool-appears-disabled"></a>도구 표시 사용하지 않음

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Windows DSVM에서 Hyper-V가 작동하지 않음

Hyper-V가 처음에 Windows에서 작동하지 않는 것은 예상된 동작입니다. 부팅 성능을 위해 일부 서비스를 사용하지 않도록 설정했습니다. Hyper-V를 사용하도록 설정하려면 다음을 수행합니다.

1. Windows DSVM에서 검색 창 열기
1. "서비스"를 입력
1. 모든 Hyper-V 서비스를 "수동"으로 설정
1. "Hyper-V 가상 머신 관리"를 "자동"으로 설정

최종 화면이 다음과 같아야 합니다.

   ![Hyper-V 사용](./media/workaround/hyperv-enable-dsvm.png)