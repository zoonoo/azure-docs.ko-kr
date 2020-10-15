---
title: 배포 옵션
description: IoT 기능 및 서비스에 대 한 Defender의 기본 워크플로 이해를 시작 하세요.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.openlocfilehash: e3de253ee6f45f9296d6b09189fe4bc488be36ad
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92090066"
---
# <a name="getting-started-with-azure-defender-for-iot"></a>IoT 용 Azure Defender 시작

이 문서에서는 IoT 용 Azure Defender를 실행 하는 데 필요한 배포 및 온 보 딩 프로세스에 대해 설명 합니다. 추가 단계도 필요 합니다. 이러한 단계를 이해 하 고 함께 제공 되는 문서의 정보를 숙지 하는 것이 좋습니다.

모든 단계를 완료 하면 IoT 센서 용 Azure Defender에서 네트워크를 모니터링 합니다. 솔루션을 설정 하는 방법에 따라 검색을 온-프레미스 관리 콘솔 또는 IoT Hub로 보낼 수도 있습니다.

IoT 용 Azure Defender를 시작 하려면 다음 단계를 완료 합니다.

## <a name="1-set-up-azure"></a>1. Azure 설정

- Azure 계정을 설정 합니다. 자세한 내용은 [Azure 계정 만들기](https://docs.microsoft.com/learn/modules/create-an-azure-account/)를 참조 하세요.

- 방화벽 또는 프록시: 특정 연결을 허용 하도록 구성 된 방화벽 또는 유사한 중개 네트워크 장치가 있는 경우 *. azure-devices.net:443이 방화벽 또는 프록시에 열려 있는지 확인 합니다. 와일드 카드가 지원 되지 않거나 더 많은 제어를 원하는 경우에는 FW 또는 프록시에서 특정 IoT Hub FQDN을 열어야 합니다. 자세한 내용은 [참조 IoT Hub 끝점](/azure/iot-hub/iot-hub-devguide-endpoints)을 참조 하세요.

## <a name="2-deploy-hardware-software-and-onboard-to-sensor"></a>2. 하드웨어 및 소프트웨어를 배포 하 고 센서에 등록

- 센서 하드웨어를 구입 하 고 소프트웨어를 설치 합니다. 여기에 설명 된 단계를 수행 하 고 자세한 내용은이 문서 및 [IoT 용 Defender 하드웨어 가이드](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) 및 [설치 가이드](https://aka.ms/AzureDefenderforIoTInstallSensorISO)를 참조 하세요.

  - 센서를 설치한 후 센서 로그인 자격 증명을 안전 하 게 기록 합니다. 인증 파일을 센서에 업로드 하려면 자격 증명이 필요 합니다.

  - 로컬로 관리 되는 센서를 사용 하 여 작업 하는 경우에는 설치에 정의 된 센서 이름 또는 센서의 IP 주소를 안전 하 게 기록 합니다. IoT 포털 용 Defender에서 센서 등록 중에 센서 이름을 만들 때이를 사용할 수 있습니다. 나중에이를 사용 하 여 Azure Defender for IoT 포털의 등록 이름과 센서 콘솔에 표시 되는 배포 된 센서의 IP 주소 간에 더 쉽게 추적 하 고 일관 된 이름을 지정할 수 있습니다.

- IoT 포털 용 Defender에 센서를 등록 하 고 센서 활성화 파일을 다운로드 합니다.

- 활성화 파일을 센서에 업로드 합니다.

## <a name="3-perform-network-setup-for-sensor-monitoring-and-management"></a>3. 센서 모니터링 및 관리를 위해 네트워크 설치를 수행 합니다.

- 센서를 네트워크에 연결 합니다. [네트워크 설정 가이드](https://aka.ms/AzureDefenderForIoTNetworkSetup)에 설명 되어 있습니다.

## <a name="4-start-discovering-your-network"></a>4. 네트워크 검색 시작

- 센서 콘솔에서 시스템 설정 조정.

- 센서를 온-프레미스 관리 콘솔에 연결 합니다.

자세한 내용은 [iot 용 Azure Defender 센서 사용자 가이드](https://aka.ms/AzureDefenderforIoTUserGuide) 및 [iot 용 defender 온-프레미스 관리 콘솔 사용자 가이드](https://aka.ms/DefenderForIoTManagementConsole)를 참조 하세요.

## <a name="5-populate-azure-sentinel-with-alert-information"></a>5. 경고 정보를 사용 하 여 Azure 센티널 채우기

- Azure 센티널에 경고 정보를 전송 하려면 Azure 센티널 구성: [IoT 용 Defender의 데이터를 Azure 센티널에 연결](how-to-configure-with-sentinel.md)합니다.
 

## <a name="next-steps"></a>다음 단계

- [IoT에 Defender](quickstart-onboard-iot-hub.md) 사용
- [솔루션](quickstart-configure-your-solution.md) 구성
- [보안 모듈 만들기](quickstart-create-security-twin.md)
- [사용자 지정 경고](quickstart-create-custom-alerts.md) 구성
- [보안 에이전트 배포](how-to-deploy-agent.md)
