---
title: '참조: 알려진 문제 & 문제 해결'
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine에 대 한 알려진 문제, 해결 방법 및 문제 해결 목록 가져오기
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: b83effa95b17d712d4019f8ab5bf13c4f02a7d2b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "78206523"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>알려진 문제 및 Azure Data Science Virtual Machine 문제 해결

이 문서는 Azure Data Science Virtual Machine를 사용 하는 경우 발생할 수 있는 오류 또는 오류를 찾고 수정 하는 데 도움이 됩니다.

## <a name="python-package-installation-issues"></a>Python 패키지 설치 문제

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Linux에서 pip 중단 종속성이 있는 패키지 설치

패키지 `sudo pip install` 를 설치할 `pip install` 때 대신를 사용 합니다.

## <a name="disk-encryption-issues"></a>디스크 암호화 문제

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM에서 디스크 암호화가 실패 합니다.

Azure Disk Encryption (ADE)는 Ubuntu DSVM에서 현재 지원 되지 않습니다. 이 문제를 해결 하려면 [Azure managed disks의 서버 쪽 암호화](../../virtual-machines/windows/disk-encryption.md)를 구성 하는 것이 좋습니다.

## <a name="tool-appears-disabled"></a>도구 표시 안 함

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Windows DSVM에서 hyper-v가 작동 하지 않음

Hyper-v가 처음에 Windows에서 작동 하지 않는 것이 예상 된 동작입니다. 부팅 성능을 위해 일부 서비스를 사용 하지 않도록 설정 했습니다. Hyper-v를 사용 하도록 설정 하려면:

1. Windows DSVM에서 검색 창 열기
1. "서비스"를 입력 합니다.
1. 모든 Hyper-v 서비스를 "수동"으로 설정
1. "Hyper-v 가상 컴퓨터 관리"를 "자동"으로 설정 합니다.

최종 화면은 다음과 같습니다.

   ![Hyper-V 사용](./media/workaround/hyperv-enable-dsvm.png)

