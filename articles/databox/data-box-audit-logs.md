---
title: Azure Data Box, Azure Data Box Heavy 이벤트에 대 한 감사 로그 | Microsoft Docs
description: Azure Data Box의 여러 단계에서 수집 되는 Data Box에 대 한 전체 감사 로그와 Azure Data Box Heavy 순서를 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/10/2020
ms.author: alkohli
ms.openlocfilehash: 50dbe9ab649a708fb36b1c9e4fe89bccadc7ea90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86209685"
---
# <a name="audit-logs-for-your-azure-data-box-and-azure-data-box-heavy"></a>Azure Data Box 및 Azure Data Box Heavy에 대 한 감사 로그

로그는 시간 경과에 따라 발생 하는 불연속 이벤트의 레코드를 변경할 수 없으며 타임 스탬프 레코드입니다. 로그에는 장치의 진단, 감사 및 보안 정보가 포함 되어 있습니다.  

순서, 설정, 데이터 복사, 반환, Azure에 업로드 및 확인 및 데이터 지우기와 같은 작업을 수행 하는 동안 Data Box 또는 Data Box Heavy 순서가 다음 단계를 진행 합니다. 이러한 각 단계에 대해 모든 이벤트를 감사 하 고 기록 합니다.

이 문서에는 로그 유형과 수집 된 정보 및 로그 위치를 비롯 하 여 Data Box 감사 로그에 대 한 정보가 포함 되어 있습니다. 

이 문서의 정보는, Data Box 및 Data Box Heavy에 모두 적용 됩니다. 이후 섹션에서는 Data Box에 대 한 모든 참조가 Data Box Heavy에도 적용 됩니다. Azure에서 실행 되는 Data Box 서비스에서 수집 된 로그는이 문서에서 다루지 않습니다. 


## <a name="about-audit-logs"></a>감사 로그 정보 

Data Box에서 다음 로그가 수집 됩니다.

- **시스템 로그** -Windows 기반 장치 Data Box 모든 하드웨어, 소프트웨어 및 시스템 이벤트가 기록 됩니다. 이러한 이벤트 집합은 시스템 감사 로그에서 수집 되 고 보고 됩니다. 

- **보안** Data Box Windows 기반 장치 이므로 모든 보안 이벤트가 기록 됩니다. 이러한 이벤트 집합은 보안 감사 로그에서 수집 되 고 보고 됩니다. 

- **응용 프로그램** -이러한 로그는 Data Box에만 해당 됩니다. 이러한 로그에는를 실행 하는 Data Box 서비스에 대 한 응답으로 장치에 생성 된 모든 이벤트가 포함 됩니다.

이러한 각 로그에 대해서는 다음 섹션에서 설명 합니다.

### <a name="system-logs"></a>시스템 로그

다음 시스템 로그 이벤트 Id는 Data Box에서 시스템 감사 로그로 수집 됩니다.

|이벤트 공급자 이름     |수집 된 이벤트 ID   |이벤트 설명   |
|-------------------|----------|----------------|
|Microsoft-Windows-커널-일반|12  |OS를 다시 부팅 한 UTC 시간입니다.   |
|                                |13  |OS가 종료 된 UTC 시간입니다. |
|    |                              |
|Microsoft-커널-전원  |41  |시스템이 완전히 종료 되지 않고 재부팅 됩니다.| 
|    |                              |
|Microsoft-Windows-BitLocker-드라이버|모두|    |

### <a name="security-logs"></a>보안 로그

다음 보안 로그 이벤트 Id는 Data Box에 대 한 보안 감사 로그로 수집 됩니다.

|이벤트 공급자 이름                   |수집 된 이벤트 ID    |이벤트 설명       |
|--------------------------------------|------------|----------|
|Microsoft-Windows-보안-감사   |4624        |로그온에 성공 했습니다. |
|                                      |4625        |계정에 로그온 하지 못했습니다. 알 수 없는 사용자 이름 또는 잘못 된 암호입니다. |
|                                     

### <a name="application-logs"></a>애플리케이션 로그 전송 사용

다음 응용 프로그램 로그 이벤트 Id는 Data Box에 대 한 패키지 감사 로그의 일부로 수집 됩니다.     

- **DataBox** -로컬 UI에서 발생 하는 이벤트를 포함 합니다. 
- **DataBox-다시 구축** -Data Box 장치의 다시 프로 비전 관련 된 이벤트를 포함 합니다. Data Box 다시 프로 비전는 로컬 UI를 통해 장치를 다시 설정할 때 발생 합니다. 기존 공유를 제거 하 고 다시 프로 비전의 일부로 공유를 다시 만들거나 장치를 다시 설정 하 여 복사한 데이터를 지우려면이 옵션을 선택 합니다.
- **DataBox-HcsMgmt** -장치가 Azure 데이터 센터에 다시 배송 되기 전에 **출시 준비** 단계에만 관련 된 이벤트를 포함 합니다. 
- **DataBox-IfxAudit** -작업에 대 한 제품의 여러 엔터티에 의해 기록 된 메시지, 일부 흐름에서 발생 하는 상황에 대 한 자세한 정보를 나타내는 로그를 포함 합니다.

다음 표에서는 다양 한 이벤트 공급자와 각 경우에 수집 되는 해당 이벤트 Id를 요약 합니다.

|이벤트 공급자 이름    |이벤트 ID    | 메모 |
|-----------------|-----------------|-------------------|
|DataBox-OOBE-감사 |4624        |로그온에 성공 했습니다.|
|                                      |4625        |계정에 로그온 하지 못했습니다. 알 수 없는 사용자 이름 또는 잘못 된 암호입니다.|
|                                     |4634        |로그 오프 이벤트입니다.|
|                                   |  | |
|DataBox-다시 구축-감사    |65001       |다시 구축 이벤트가 성공 했습니다.|
|                                                  |65002       |이벤트를 다시 구축 못했습니다.|
|                                                  |                 |         |
|DataBox-HcsMgmt-감사        |65003       |배송 준비 상태 이벤트 NotStarted, InProgress, 실패, 취소 됨, 성공, ScanCompletedWithIssues, SucceededWithWarnings          |
|                                                  |                 |     |
|DataBox-IfxAudit    |모두 |모든 이벤트는 코드의 감사 로그 API를 사용 하 여 기록 됩니다. |

다음은 IFX (계측 프레임 워크) 감사 로그의 예입니다.

|     작업/작업/a p i                              |     기록 되는 이벤트                                                                                                              | 
|-----------------------------------------------|------------------------------------------------------------------------------------------------------------------------------|
|     정리                                   |     정리 작업의 시작, 완료 또는 오류와 관련 된 이벤트가 기록 됩니다. |                                              
|     고객 배송을 위한 장치 준비    |     장치를 배송 하기 위해 시작, 완료 또는 실패와 관련 된 이벤트가 기록 됩니다. |
|     프로비저닝                                 |     장치 프로 비전 작업의 시작, 완료 또는 오류와 관련 된 이벤트가 기록 됩니다.|
|     패키지 작업 감사                       |     관리 권의 로그 체인을 만드는 감사 패키지 작업의 시작, 완료 또는 오류와 관련 된 이벤트가 기록 됩니다.|
|     디스크 덮어쓰기                          |     디스크를 덮어쓰지 못했습니다.|
|     원격 PowerShell 사용 또는 사용 안 함     |     장치에서 원격 PowerShell을 사용 하거나 사용 하지 않도록 설정 하는 것과 관련 된 이벤트가 로깅됩니다. |
|     설치 단계 세부 정보 가져오기               |     단계에 있는 장치의 소프트웨어 설치와 관련 된 이벤트는 Azure 데이터 센터에 기록 됩니다.|
|     BitLocker 볼륨 잠금 해제 또는 잠금           |     이벤트는 *basevolume* 및 *Hcsdata* 볼륨의 BitLocker 상태를 나타내기 위해 기록 됩니다.|
|     디스크 삭제                              |     지울 수 없는 실제 디스크의 오류와 관련 된 이벤트 및 장치의 모든 실제 디스크를 성공적으로 지운 경우의 이벤트는 로깅됩니다. |
|     로컬 사용자 사용 또는 사용 안 함               |     Storsimpleadmin으로 로그인 및 PodSupportAdminUser에 대 한 로컬 사용자 계정 사용 또는 사용 안 함과 관련 된 이벤트가 기록 됩니다.| 
|     암호 재설정                          |     로컬 Storsimpleadmin으로 로그인 사용자에 대 한 성공 또는 실패 한 암호 재설정과 관련 된 이벤트가 기록 됩니다. |


IFX 감사 로그 외에도 Data Box에 대 한 관리 권의 감사 로그 체인이 수집 됩니다. 이러한 로그는 실시간으로 볼 수 없으며 작업이 완료 된 후에만 Data Box 디스크에서 데이터가 지워집니다. 이러한 로그에는 IFX 감사 로그에 포함 된 정보의 하위 집합이 포함 되어 있습니다.

관리 권의 감사 로그 체인에 대 한 자세한 내용은 [데이터 지우기 후 관리 권의 로그 체인 가져오기](data-box-logs.md#get-chain-of-custody-logs-after-data-erasure)를 참조 하세요.

<!-- write a few lines about order history and link out to the detailed section on order history-->

## <a name="access-audit-logs"></a>감사 로그 액세스

이러한 로그는 Azure에 저장 되며 직접 액세스할 수 없습니다. 이러한 로그에 액세스 해야 하는 경우 지원 티켓을 제출 합니다. 자세한 내용은 [Microsoft 지원에 문의](data-box-disk-contact-microsoft-support.md)를 참조하세요. 

지원 티켓이 작성 되 면 Microsoft에서 다운로드 하 여 이러한 로그에 대 한 액세스 권한을 제공 합니다.


## <a name="next-steps"></a>다음 단계

- [Data Box 및 Data Box Heavy에 대 한 문제를 해결](data-box-troubleshoot.md)하는 방법을 알아봅니다.
