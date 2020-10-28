---
title: '빠른 시작: 배포 준비'
description: Azure Defender for IoT를 배포하기 위해 네트워크를 준비하는 방법을 알아봅니다.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: rkarlin
ms.openlocfilehash: 2de96920a354c7e7a26518ec87c7e78aa73e3999
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094643"
---
# <a name="quickstart-prepare-your-network-for-azure-defender-for-iot-deployment"></a>빠른 시작: Azure Defender for IoT 배포를 위한 네트워크 준비

## <a name="choose-your-hardware"></a>하드웨어 선택
Azure Defender for IoT에서 지원하는 다양한 인증 어플라이언스는 다음과 같습니다.
- 미리 구성된 공인 어플라이언스(소프트웨어가 이미 설치되어 있음)
- 구성되지 않은 공인 어플라이언스(센서 소프트웨어를 다운로드하고 설치할 수 있음)
- 온-프레미스 관리 콘솔 어플라이언스 사양. 온-프레미스 관리 콘솔은 미리 구성된 어플라이언스로 사용할 수 없습니다. 

자세한 내용은 [하드웨어 사양 가이드](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)를 참조하세요.


## <a name="install-components"></a>구성 요소 설치
하드웨어 구성 요소를 획득하고 Azure Defender for IoT 포털에서 소프트웨어 패키지를 다운로드하면 구성 요소 소프트웨어를 설치할 수 있습니다. 자세한 내용은 [설치 가이드](https://aka.ms/AzureDefenderforIoTInstallSensorISO)를 참조하세요.

## <a name="onboard-sensors-to-the-azure-defender-for-iot-portal"></a>Azure Defender for IoT 포털에 센서 온보딩 
 이 문서에서는 Azure Defender for IoT 센서와 온-프레미스 관리 콘솔의 배포, 온보딩 및 업데이트 프로세스에 대해 간략히 설명합니다. 이 프로세스는 Azure Defender for IoT 시작 페이지 및 후속 페이지의 지침에 따라 수행됩니다.

![Azure 시작 페이지 보기의 스크린샷](media/updates/image4.png)

[시작] 페이지를 열려면 다음을 수행합니다.

1. Azure 계정에 로그인하여 Azure Defender for IoT를 검색합니다.

1. **센서 배포** 를 사용하려면 Arrow에서 미리 구성된 하드웨어를 구입하거나 사용자 고유의 디바이스를 구입하여 센서 소프트웨어를 설치해야 합니다.
1. **온-프레미스 관리 콘솔 배포** 를 사용하려면 사용자 고유의 디바이스를 구입하여 온-프레미스 관리 콘솔 소프트웨어를 설치해야 합니다. 자세한 내용은




## <a name="network-setup"></a>네트워크 설정
Defender for IoT 시스템 구성 요소를 설치하면 최적의 네트워크 모니터링을 위해 네트워크 장비를 구성할 수 있습니다. 자세한 내용은 이 [네트워크 설정 가이드](https://aka.ms/AzureDefenderForIoTNetworkSetup)를 참조하세요.   



## <a name="next-steps"></a>다음 단계

이 문서에서는 Defender for IoT를 사용하도록 준비하는 방법을 알아보았습니다. 시작하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

- [시작](getting-started.md)