---
title: '참조: 알려진 문제 & 문제 해결'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure 데이터 과학 가상 머신에 대한 알려진 문제, 해결 및 문제 해결 목록 보기
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206523"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>알려진 문제 및 Azure 데이터 과학 가상 컴퓨터 문제 해결

이 문서에서는 Azure 데이터 과학 가상 컴퓨터를 사용할 때 발생할 수 있는 오류 또는 오류를 찾아 수정하는 데 도움이 됩니다.

## <a name="python-package-installation-issues"></a>파이썬 패키지 설치 문제

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Pip 나누기 와 패키지를 설치 리눅스에 종속성

패키지를 `sudo pip install` 설치할 때 대신 `pip install` 사용하십시오.

## <a name="disk-encryption-issues"></a>디스크 암호화 문제

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>우분투 DSVM에서 디스크 암호화가 실패

Azure 디스크 암호화(ADE)는 현재 우분투 DSVM에서 지원되지 않습니다. 해결 방법을 사용하여 [Azure 관리 디스크의 서버 측 암호화를](../../virtual-machines/windows/disk-encryption.md)구성하는 것이 좋습니다.

## <a name="tool-appears-disabled"></a>도구가 비활성화된 것으로 나타납니다.

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>하이퍼 V는 Windows DSVM에서 작동하지 않습니다.

처음에 Hyper-V가 Windows에서 작동하지 않는 것은 예상되는 동작입니다. 부팅 성능을 위해 일부 서비스를 비활성화했습니다. 하이퍼-V를 사용하려면 다음을 수행하십시오.

1. Windows DSVM에서 검색 창 열기
1. "서비스"를 입력합니다.
1. 모든 Hyper-V 서비스를 "수동"으로 설정
1. "하이퍼 V 가상 시스템 관리"를 "자동"으로 설정

최종 화면은 다음과 같아야 합니다.

   ![Hyper-V 사용](./media/workaround/hyperv-enable-dsvm.png)

