---
title: PowerShell 샘플
description: 몇 가지 일반적인 App Service 시나리오에 대한 Azure PowerShell 샘플을 찾습니다. App Service 배포 또는 관리 작업을 자동화하는 방법을 알아봅니다.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 07/07/2020
ms.custom: mvc
ms.openlocfilehash: a1577d42de9a4452467a448a0de5cd5f9575a55f
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86169428"
---
# <a name="powershell-samples-for-azure-app-service"></a>Azure App Service에 대한 PowerShell 샘플

다음 테이블은 Azure PowerShell을 사용하여 빌드된 PowerShell 스크립트에 대한 링크를 포함합니다.

| 스크립트 | Description |
|-|-|
|**앱 만들기**||
| [GitHub의 배포를 사용하여 앱 만들기](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| GitHub에서 코드를 끌어오는 App Service 앱을 만듭니다. |
| [GitHub의 지속적인 배포를 사용하여 앱 만들기](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 연속적으로 GitHub의 코드를 배포하는 App Service 앱을 만듭니다. |
| [FTP를 사용하여 앱 만들기 및 코드 배포](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | FTP를 사용하여 로컬 디렉터리에서 App Service 앱을 만들고 파일을 업로드합니다. |
| [앱 만들기 및 로컬 Git 리포지토리의 코드 배포](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service 앱을 만들고 로컬 Git 리포지토리의 코드 푸시를 구성합니다. |
| [앱 만들기 및 스테이징 환경에 코드 배포](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 코드 변경 내용을 준비하기 위해 배포 슬롯을 사용하여 App Service 앱을 만듭니다. |
|  [프라이빗 엔드포인트를 사용하여 앱 만들기 및 앱 노출](./scripts/powershell-deploy-private-endpoint.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 프라이빗 엔드포인트를 사용하여 App Service 앱을 만듭니다. |
|**앱 구성**||
| [사용자 지정 도메인을 앱에 매핑](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service 앱을 만들고 여기에 사용자 지정 도메인 이름을 매핑합니다. |
| [앱에 사용자 지정 TLS/SSL 인증서 바인딩](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service 앱을 만들고 여기에 사용자 지정 도메인 이름의 TLS/SSL 인증서를 바인딩합니다. |
|**앱 크기 조정**||
| [수동으로 앱 크기 조정](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service 앱을 만들고 2개의 인스턴스로 확장합니다. |
| [전 세계에 고가용성 아키텍처를 가진 앱 확장](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 두 개의 지역에 두 개의 App Service 앱을 만들고 Azure Traffic Manager를 사용하여 단일 엔드포인트를 통해 사용할 수 있습니다. |
|**리소스에 앱 연결**||
| [SQL Database에 앱 연결](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service 앱 및 Azure SQL Database의 데이터베이스를 만든 다음, 앱 설정에 데이터베이스 연결 문자열을 추가합니다. |
| [스토리지 계정에 앱 연결](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| App Service 앱 및 스토리지 계정을 만든 다음, 앱 설정에 스토리지 연결 문자열을 추가합니다. |
|**백업 및 복원 앱**||
| [앱 백업](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service 앱을 만들고 이에 대한 일회성 백업을 만듭니다. |
| [앱에 대한 예약된 백업 만들기](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service 앱을 만들고 이에 대한 예약된 백업을 만듭니다. |
| [앱에 대한 백업 삭제](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 앱에 대한 기존 백업을 삭제합니다. |
| [백업으로 앱 복원](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 이전에 완료된 백업에서 앱을 복원합니다. |
| [구독 간에 백업 복원](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | 다른 구독에 있는 백업에서 웹앱을 복원합니다. |
|**앱 모니터링**||
| [웹 서버 로그로 앱 모니터링](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | App Service 앱을 만들고 로깅을 사용하도록 설정하고 로그를 로컬 머신에 다운로드합니다. |
| | |
