---
title: Azure IoT Central 관리자 가이드
description: Azure IoT Central은 IoT 솔루션 만들기를 간소화하는 IoT 애플리케이션 플랫폼입니다. 이 문서에서는 IoT Central의 관리자 역할에 대 한 개요를 제공 합니다.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/25/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 16a8aecae70d73399acb3878d7088e5086c053a1
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110494"
---
# <a name="iot-central-administrator-guide"></a>IoT Central 관리자 가이드

*이 문서는 관리자에 게 적용 됩니다.*

IoT Central 애플리케이션을 사용하면 수백만 대의 디바이스를 수명 주기 전체 동안 모니터링하고 관리할 수 있습니다. 이 가이드는 IoT Central 응용 프로그램을 관리 하는 관리자를 위한 것입니다.

IoT Central 관리자는 다음과 같습니다.

- 응용 프로그램의 사용자 및 역할을 관리 합니다.
- 장치 인증 등의 보안을 관리 합니다.
- 응용 프로그램 설정을 구성 합니다.
- 응용 프로그램을 업그레이드 합니다.
- 응용 프로그램을 내보내고 공유 합니다.
- 응용 프로그램 상태를 모니터링 합니다.

## <a name="users-and-roles"></a>사용자 및 역할

IoT Central는 역할 기반 액세스 제어 시스템을 사용 하 여 응용 프로그램 내에서 사용자 권한을 관리 합니다. IoT Central에는 관리자, 솔루션 빌더 및 연산자에 대 한 세 가지 기본 제공 역할이 있습니다. 관리자는 특정 권한 집합을 사용 하 여 사용자 지정 역할을 만들 수 있습니다. 관리자는 응용 프로그램에 사용자를 추가 하 고 역할에 할당 하는 일을 담당 합니다.

자세히 알아보려면 [IoT Central 응용 프로그램에서 사용자 및 역할 관리](howto-manage-users-roles.md)를 참조 하세요.

## <a name="application-security"></a>애플리케이션 보안

IoT Central 응용 프로그램에 연결 하는 장치는 일반적으로 x.509 인증서 또는 SAS (공유 액세스 서명)를 자격 증명으로 사용 합니다. 관리자는 장치 자격 증명이 파생 된 그룹 인증서 또는 키를 관리 합니다.

자세히 알아보려면 [x.509 그룹 등록](concepts-get-connected.md#x509-group-enrollment), [SAS 그룹 등록](concepts-get-connected.md#sas-group-enrollment)및 [x.509 장치 인증서를 롤 하는 방법](how-to-roll-x509-certificates.md)을 참조 하세요.

또한 관리자는 클라이언트 응용 프로그램이 IoT Central 응용 프로그램으로 인증 하는 데 사용 하는 API 토큰을 만들고 관리할 수 있습니다. 클라이언트 응용 프로그램은 REST API를 사용 하 여 IoT Central와 상호 작용 합니다.

## <a name="configure-an-application"></a>애플리케이션 구성

관리자는 IoT Central 응용 프로그램의 동작과 모양을 구성할 수 있습니다. 자세한 내용은 다음을 참조하세요.

- [애플리케이션 이름 및 URL 변경](howto-administer.md#change-application-name-and-url)
- [UI 사용자 지정](howto-customize-ui.md)
- [다른 가격 책정 계획으로 응용 프로그램 이동](howto-view-bill.md)
- [파일 업로드 구성](howto-configure-file-uploads.md)

## <a name="export-an-application"></a>응용 프로그램 내보내기

관리자는 다음을 수행할 수 있습니다.

- 응용 프로그램의 복사본을 복제 해야 하는 경우 응용 프로그램의 복사본을 만듭니다. 예를 들어 테스트용 중복 복사본이 필요할 수 있습니다.
- 여러 복사본을 만들 계획인 경우 기존 응용 프로그램에서 응용 프로그램 템플릿을 만듭니다.

자세히 알아보려면 [Azure IoT 응용 프로그램 내보내기](howto-use-app-templates.md)를 참조 하세요.

## <a name="migrate-to-a-new-version"></a>새 버전으로 마이그레이션

관리자는 응용 프로그램을 최신 버전으로 마이그레이션할 수 있습니다. 현재 새로 만든 응용 프로그램은 V3 응용 프로그램입니다. 관리자가 V2 응용 프로그램을 V3 응용 프로그램으로 마이그레이션해야 할 수 있습니다.

자세히 알아보려면 [V2 IoT Central 응용 프로그램을 V3로 마이그레이션](howto-migrate.md)을 참조 하세요.

## <a name="monitor-application-health"></a>애플리케이션 상태 모니터링

관리자는 IoT Central 메트릭을 사용 하 여 연결 된 장치의 상태와 실행 중인 데이터 내보내기의 상태를 평가할 수 있습니다.

메트릭을 보려면 관리자가 Azure Portal, REST API 또는 PowerShell 또는 Azure CLI 쿼리의 차트를 사용할 수 있습니다.

자세히 알아보려면 [IoT Central 응용 프로그램의 전반적인 상태 모니터링](howto-monitor-application-health.md)을 참조 하세요.

## <a name="tools"></a>도구

관리자 권한으로 사용 하는 대부분의 도구는 각 IoT Central 응용 프로그램의 **관리** 섹션에서 사용할 수 있습니다. 또한 다음 도구를 사용 하 여 몇 가지 관리 작업을 완료할 수 있습니다.

- [Azure CLI](howto-manage-iot-central-from-cli.md)
- [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
- [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>다음 단계

이제 Azure IoT Central 응용 프로그램을 관리 하는 방법을 배웠으므로 제안 된 다음 단계는 Azure IoT Central에서 [사용자 및 역할 관리](howto-manage-users-roles.md) 에 대해 알아보는 것입니다.
