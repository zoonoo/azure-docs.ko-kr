---
title: 인프라 백업 서비스를 사용 하 여 Azure Stack에 대 한 백업 및 데이터 복구 | Microsoft Docs
description: 백업 하 고 구성 및 인프라 Backup 서비스를 사용 하 여 서비스 데이터를 복원할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
mss.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 01/25/2019
ms.openlocfilehash: 607356bb59550626c9fe5f71d4609b9c3e2b2fe0
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251302"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>인프라 백업 서비스를 사용 하 여 Azure Stack에 대 한 백업 및 데이터 복구

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

백업 하 고 구성 및 인프라 Backup 서비스를 사용 하 여 서비스 데이터를 복원할 수 있습니다. 각 Azure Stack 설치 서비스의 인스턴스를 포함합니다. Id, 보안 및 Azure Resource Manager 데이터를 복원 하려면 Azure Stack 클라우드에 다시 배포를 위해 서비스에서 생성 된 백업을 사용할 수 있습니다.

프로덕션 환경에 클라우드를 적용할 준비가 되 면 backup을 사용할 수 있습니다. 테스트를 수행 하려는 경우 백업 및 오랜 기간에 대 한 유효성 검사를 사용 하지 마십시오.

백업 서비스를 사용 하기 전에 했는지 [위치에 대 한 요구 사항](#verify-requirements-for-the-infrastructure-backup-service)합니다.

> [!Note]  
> 사용자 데이터 및 응용 프로그램 인프라 Backup 서비스가 포함 되지 않습니다. <!-- See the following articles for instructions on backing up and restore [App Services](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), and [MySQL](https://aka.ms/azure-stack-mysql) resource providers and associated user data. -->

## <a name="the-infrastructure-backup-service"></a>인프라 백업 서비스

서비스에는 다음과 같은 기능을 포함 합니다.

| 기능                                            | 설명                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 백업 인프라 서비스                     | Azure Stack에서 인프라 서비스의 하위 집합에서 backup을 조정 합니다. 재해의 경우 재배포의 일부로 데이터를 복원할 수 있습니다. |
| 압축 및 내보낸된 백업 데이터의 암호화 | 백업 데이터를 압축 하 고 관리자가 제공한 외부 저장소 위치로 내보낸 전에 시스템에 의해 암호화.                |
| 백업 작업 모니터링                              | 백업 작업이 실패 하 고 수정 단계 시스템에 알립니다.                                                                                                |
| 백업 관리 환경                       | 백업 RP 백업 활성화를 지원합니다.                                                                                                                         |
| 클라우드 복구                                     | 치명적인 데이터 손실로 인 경우 백업은 배포의 일환으로 핵심 Azure Stack 정보 복원에 사용할 수 있습니다.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>인프라 Backup 서비스에 대 한 요구 사항 확인

- **스토리지 위치**  
  필요한 파일 공유 7 백업을 포함할 수 있는 Azure Stack에서 액세스할 수 있습니다. 각 백업은 약 10GB입니다. 공유에 백업 140 GB를 저장할 수 있어야 합니다. Azure Stack 인프라 백업 서비스에 대 한 저장소 위치를 선택 하는 방법에 대 한 자세한 내용은 참조 하십시오 [백업 컨트롤러 요구 사항](azure-stack-backup-reference.md#backup-controller-requirements)합니다.
- **자격 증명**  
  도메인 사용자 계정 및 자격 증명 필요, 예를 들어, Azure Stack 관리자 자격 증명을 사용할 수 있습니다.
- **암호화 키**  
  백업 파일이이 키를 사용 하 여 암호화 됩니다. 이 키는 안전한 위치에 저장 해야 합니다. 을 처음으로이 키를 설정 하거나 나중에 키를 회전 되 면이 키는이 인터페이스에서 확인할 수 없습니다. 미리 공유한 키를 생성 하는 자세한 지침은, 스크립트의 따릅니다 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.

## <a name="next-steps"></a>다음 단계

- 설명 하는 방법 [관리 포털에서 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-console.md)합니다.
- 설명 하는 방법 [PowerShell 사용 하 여 Azure Stack에 대 한 백업을 사용 하도록 설정](azure-stack-backup-enable-backup-powershell.md)합니다.
- 자세한 방법 [Azure Stack 백업](azure-stack-backup-back-up-azure-stack.md )
- 자세한 방법 [치명적인 데이터 손실 로부터 복구](azure-stack-backup-recover-data.md)
