---
title: 알려진 문제 및 문제 해결
titleSuffix: Azure Data Science Virtual  Machine
description: Azure Data Science Virtual Machine에 대 한 알려진 문제, 해결 방법 및 문제 해결 목록 가져오기
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: reference
ms.date: 10/10/2019
ms.openlocfilehash: a0b22326a429edfa2f2b8741453215b42910891c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301920"
---
# <a name="known-issues-and-troubleshooting-the-azure-data-science-virtual-machine"></a>알려진 문제 및 Azure Data Science Virtual Machine 문제 해결

이 문서는 Azure Data Science Virtual Machine를 사용할 때 발생 하는 오류 또는 오류를 찾고 수정 하는 데 도움이 됩니다.

## <a name="python-package-installation-issues"></a>Python 패키지 설치 문제

### <a name="installing-packages-with-pip-breaks-dependencies-on-linux"></a>Linux에서 pip 중단 종속성이 있는 패키지 설치

패키지를 설치 하는 경우에는 `pip install` 대신 `sudo pip install`을 사용 합니다.

## <a name="disk-encryption-issues"></a>디스크 암호화 문제

### <a name="disk-encryption-fails-on-the-ubuntu-dsvm"></a>Ubuntu DSVM에서 디스크 암호화가 실패 합니다.

Azure Disk Encryption (ADE)는 Ubuntu DSVM에서 현재 지원 되지 않습니다. 해결 방법으로, [고객 관리 키를 사용 하 여 Azure Storage 암호화](../../storage/common/storage-encryption-keys-portal.md)를 구성 하는 것이 좋습니다.

## <a name="tool-appears-disabled"></a>도구 표시 안 함

### <a name="hyper-v-does-not-work-on-the-windows-dsvm"></a>Windows DSVM에서 hyper-v가 작동 하지 않음

이는 예상 된 동작입니다. 부팅 성능에서 일부 서비스를 사용 하지 않도록 설정 했습니다. 다시 사용 하도록 설정 하려면 Windows DSVM에서 검색 창을 열고 "서비스"를 입력 한 다음 모든 Hyper-v 서비스를 "수동"으로 설정 하 고 "Hyper-v 가상 컴퓨터 관리"를 "자동"으로 설정 합니다.

최종 화면은 다음과 같습니다.

   ![Hyper-V 사용](./media/workaround/hyperv-enable-dsvm.png)

