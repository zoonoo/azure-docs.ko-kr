---
title: Azure Data Box, Azure Data Box Heavy 이벤트에 대한 감사 로그 | Microsoft Docs
description: Azure Data Box의 여러 단계에서 수집되는 Data Box에 대한 전체 감사 로그와 Azure Data Box Heavy 순서를 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "86209685"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 및 Azure Data Box Heavy에 대한 감사 로그

로그는 시간 경과에 따라 발생하는 불연속 이벤트의 레코드를 변경할 수 없으며 타임스탬프 레코드입니다. 로그에는 디바이스의 진단, 감사 및 보안 정보가 포함되어 있습니다.  

순서, 설정, 데이터 복사, 반환, Azure에 업로드 후 확인 및 데이터 지우기와 같은 작업을 수행하는 동안 Data Box 또는 Data Box Heavy 순서가 다음 단계를 진행합니다. 이러한 각 단계에 대해 모든 이벤트를 감사하고 기록합니다.

이 문서에는 로그 유형과 수집된 정보 및 로그 위치를 비롯하여 Data Box 감사 로그에 대한 정보가 포함되어 있습니다. 

이 문서의 정보는 Data Box 및 Data Box Heavy에 모두 적용됩니다. 이후 섹션에서는 Data Box에 대한 모든 참조가 Data Box Heavy에도 적용됩니다. Azure에서 실행되는 Data Box 서비스에서 수집된 로그는 이 문서에서 다루지 않습니다. 


## <a name="about-audit-logs"></a>감사 로그 정보 

Data Box에서 다음 로그가 수집됩니다.

- **시스템 로그** - Data Box는 Windows 기반 디바이스이며 모든 하드웨어, 소프트웨어 및 시스템 이벤트가 기록됩니다. 이러한 이벤트 집합은 시스템 감사 로그에서 수집되고 보고됩니다. 

- **보안** - Data Box는 Windows 기반 디바이스이므로 모든 보안 이벤트가 기록됩니다. 이러한 이벤트 집합은 보안 감사 로그에서 수집되고 보고됩니다. 

- **애플리케이션** - 이 로그는 Data Box에만 해당됩니다. 이러한 로그에는 실행 중인 Data Box 서비스에 대한 응답으로 디바이스에서 생성된 모든 이벤트가 포함됩니다.

이러한 각 로그는 다음 섹션에서 설명합니다.

### <a name="system-logs"></a>시스템 로그

다음 시스템 로그 이벤트 ID는 Data Box에서 시스템 감사 로그로 수집됩니다.

|이벤트 공급자 이름     |수집된 이벤트 ID   |이벤트 설명   |
|-------------------|----------|----------------|
|Microsoft-Windows-Kernel-General|12  |OS를 다시 부팅한 시간(UTC)입니다.   |
|                                |13  |OS가 종료된 시간(UTC)입니다. |
|    |                              |
|Microsoft-Windows-Kernel-Power  |41  |시스템이 완전히 종료되지 않고 다시 부팅됩니다.| 
|    |                              |
|Microsoft-Windows-BitLocker-Driver|모두|    |

### <a name="security-logs"></a>보안 로그

다음 보안 로그 이벤트 ID는 Data Box에 대한 보안 감사 로그로 수집됩니다.

|이벤트 공급자 이름                   |수집된 이벤트 ID    |이벤트 설명       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-Security-Auditing   |4624        |로그온에 성공했습니다. |
|                                      |4625        |계정에 로그온하지 못했습니다. 알 수 없는 사용자 이름이거나 암호가 잘못되었습니다. |
|                                     

### <a name="application-logs"></a>애플리케이션 로그 전송 사용

다음 애플리케이션 로그 이벤트 ID는 Data Box에 대한 패키지 감사 로그의 일부로 수집됩니다.     

- **Microsoft-Azure-DataBox-OOBE-Auditing** - 로컬 UI에서 발생하는 이벤트가 포함됩니다. 
- **Microsoft-Azure-DataBox-Reprovision-Audit** - Data Box 디바이스의 다시 프로비저닝과 관련된 이벤트가 포함됩니다. Data Box의 다시 프로비저닝은 로컬 UI를 통해 디바이스를 다시 설정할 때 발생합니다. 기존 공유를 제거하고 다시 프로비저닝의 일부로 공유를 다시 만들거나 디바이스를 다시 설정하여 복사한 데이터를 지우려면 이 옵션을 선택합니다.
- **Microsoft-Azure-DataBox-HcsMgmt-Audit** - 디바이스가 Azure 데이터 센터로 다시 배송되기 전의 **배송 준비** 단계와 관련된 이벤트만 포함됩니다. 
- **Microsoft-Azure-DataBox-IfxAudit** - 제품의 여러 엔터티에서 작업에 대해 기록한 메시지와 일부 흐름에서 발생하는 상황에 대한 자세한 정보를 나타내는 로그가 포함됩니다.

다음 표에서는 다양한 이벤트 공급자와 각 경우에 수집되는 해당 이벤트 ID를 요약합니다.

|이벤트 공급자 이름    |이벤트 ID    | 참고 |
|-----------------|-----------------|-------------------|
|Microsoft-Azure-DataBox-OOBE-Auditing |4624        |로그온에 성공했습니다.|
|                                      |4625        |계정에 로그온하지 못했습니다. 알 수 없는 사용자 이름이거나 암호가 잘못되었습니다.|
|                                     |4634        |로그오프 이벤트입니다.|
|                                   |  | |
|Microsoft-Azure-DataBox-Reprovision-Audit    |65001       |다시 구축 이벤트가 성공했습니다.|
|                                                  |65002       |이벤트를 다시 구축하지 못했습니다.|
|                                                  |                 |         |
|Microsoft-Azure-DataBox-HcsMgmt-Audit        |65003       |배송 준비 상태 이벤트     NotStarted,     InProgress,     실패,     취소,     성공,     ScanCompletedWithIssues,     SucceededWithWarnings          |
|                                                  |                 |     |
|Microsoft-Azure-DataBox-IfxAudit    |모두 |모든 이벤트는 코드의 감사 로그 API를 사용하여 기록됩니다. |

다음은 IFX(계측 프레임워크) 감사 로그의 예입니다.

|     태스크/작업/API                              |     기록되는 이벤트                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     정리                                   |     정리 작업의 시작, 완료 또는 오류와 관련된 이벤트가 기록됩니다. |                                              
|     고객 배송을 위한 디바이스 준비    |     디바이스를 배송하기 위해 시작, 완료 또는 실패와 관련된 이벤트가 기록됩니다. |
|     프로비저닝                                 |     디바이스 프로비저닝 작업의 시작, 완료 또는 오류와 관련된 이벤트가 기록됩니다.|
|     감사   패키지 작업                       |     관리 로그 체인을 만드는 감사 패키지 작업의 시작, 완료 또는 실패와 관련된 이벤트가 기록됩니다.|
|     디스크 덮어쓰기                          |     디스크 덮어쓰기 실패가 기록되었습니다.|
|     원격   PowerShell 사용 또는 사용 안 함     |     디바이스에서 원격 PowerShell을 사용하거나 사용하지 않도록 설정하는 것과 관련된 이벤트가 기록됩니다. |
|     설치   단계 세부 정보 가져오기               |     단계에 있는 디바이스의 소프트웨어 설치와 관련된 이벤트는 Azure 데이터 센터에 기록됩니다.|
|     BitLocker 볼륨 잠금 해제 또는 잠금           |     이벤트는 *basevolume* 및 *hcsdata* 볼륨의 BitLocker 상태를 나타내기 위해 기록됩니다.|
|     디스크 삭제                              |     지울 수 없는 실제 디스크의 오류와 관련된 이벤트 및 디바이스의 모든 실제 디스크를 성공적으로 지운 경우의 이벤트가 기록됩니다. |
|     로컬 사용자 사용 또는 사용 안 함               |     StorSimpleAdmin 및 PodSupportAdminUser에 대한 로컬 사용자 계정을 사용하거나 사용하지 않도록 설정하는 것과 관련된 이벤트가 기록됩니다.| 
|     암호 재설정                          |     로컬 StorSimpleAdmin에 대한 암호 다시 설정의 성공 또는 실패와 관련된 이벤트가 기록됩니다. |


IFX 감사 로그와는 별도로, 관리 연속성 감사 로그도 Data Box에 수집됩니다. 이러한 로그는 실시간으로 볼 수 없으며 작업이 완료된 후에만 Data Box 디스크에서 데이터가 지워집니다. 이러한 로그에는 IFX 감사 로그에 포함된 정보의 하위 집합이 포함되어 있습니다.

관리 연속성 감사 로그에 대한 자세한 내용은 [데이터 삭제 후 관리 연속성 로그 가져오기](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)를 참조하세요.

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>감사 로그 액세스

이러한 로그는 Azure에 저장되며 직접 액세스할 수 없습니다. 이러한 로그에 액세스해야 하는 경우 지원 티켓을 제출합니다. 자세한 내용은 [Microsoft 지원에 문의](data-box-disk-contact-microsoft-support.md)를 참조하세요. 

지원 티켓이 제출되면 Microsoft에서 다운로드하여 이러한 로그에 대한 액세스 권한을 제공합니다.


## <a name="next-steps"></a>다음 단계

- [Data Box 및 Data Box Heavy 문제 해결](data-box-troubleshoot.md) 방법에 대해 알아보세요.
