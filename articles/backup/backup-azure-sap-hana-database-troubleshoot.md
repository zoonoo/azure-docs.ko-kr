---
title: SAP HANA 데이터베이스 백업 오류 문제 해결
description: Azure Backup을 사용하여 SAP HANA 데이터베이스를 백업할 때 발생할 수 있는 일반적인 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 6520f106011b632da2725f456aeb278c7748ddc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79459313"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure에서 SAP HANA 데이터베이스의 백업 문제 해결

이 문서에서는 Azure 가상 컴퓨터에서 SAP HANA 데이터베이스를 백업하기 위한 문제 해결 정보를 제공합니다. 현재 지원 중인 SAP HANA 백업 시나리오에 대한 자세한 내용은 [시나리오 지원을](sap-hana-backup-support-matrix.md#scenario-support)참조하십시오.

## <a name="prerequisites-and-permissions"></a>전제 조건 및 권한

백업을 구성하기 전에 [필수 구성 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 사전 등록 [스크립트가 수행하는 작업을](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 참조하십시오.

## <a name="common-user-errors"></a>일반적인 사용자 오류

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **오류 메시지**      | <span style="font-weight:normal">Azure 백업에 백업을 수행하는 데 필요한 역할 권한이 없습니다.</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **가능한 원인**    | 역할이 덮어쓰였을 수 있습니다.                          |
| **권장 작업** | 이 문제를 해결하려면 **Discover DB** 창에서 스크립트를 실행하거나 [여기에서](https://aka.ms/scriptforpermsonhana)다운로드합니다. 또는 워크로드 백업 사용자(AZUREWLBACKUPHANAUSER)에 'SAP_INTERNAL_HANA_SUPPORT' 역할을 추가합니다. |

### <a name="usererrorinopeninghanaodbcconnection"></a>사용자 오류인오프닝하나오브bc커넥션

| 오류 메시지      | <span style="font-weight:normal">HANA 시스템에 연결하지 못했습니다.</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 인스턴스가 다운될 수 있습니다.<br/>HANA 데이터베이스와 상호 작용하는 Azure 백업에 필요한 사용 권한이 설정되지 않았습니다. |
| **권장 작업** | SAP HANA 데이터베이스가 위업인지 확인합니다. 데이터베이스가 실행 중이면 필요한 모든 사용 권한이 설정되어 있는지 확인합니다. 사용 권한이 없는 경우 사전 [등록 스크립트를](https://aka.ms/scriptforpermsonhana) 실행하여 누락된 권한을 추가합니다. |

### <a name="usererrorhanainstancenameinvalid"></a>사용자 오류하나 인스턴스이름 유효하지 않음

| 오류 메시지      | <span style="font-weight:normal">지정된 SAP HANA 인스턴스가 잘못되었거나 찾을 수 없습니다.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 단일 Azure VM에서 여러 SAP HANA 인스턴스를 백업할 수 없습니다. |
| **권장 작업** | 백업하려는 SAP HANA 인스턴스에서 [사전 등록 스크립트를](https://aka.ms/scriptforpermsonhana) 실행합니다. 그래도 문제가 지속되면 Microsoft 지원에 문의하십시오. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 오류 메시지      | <span style="font-weight:normal">지정된 SAP HANA 작업이 지원되지 않습니다.</span>              |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA에 대한 Azure 백업은 SAP HANA 네이티브 클라이언트(스튜디오/ 조종석/DBA 조종석)에서 수행되는 증분 백업 및 작업을 지원하지 않습니다. |
| **권장 작업** | 자세한 내용은 [여기를](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)참조하십시오. |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>사용자 오류하나포도Not지원백업유형

| 오류 메시지      | <span style="font-weight:normal">이 SAP HANA 데이터베이스는 요청된 백업 유형을 지원하지 않습니다.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | Azure 백업은 스냅숏을 사용하여 증분 백업 및 백업을 지원하지 않습니다. |
| **권장 작업** | 자세한 내용은 [여기를](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)참조하십시오. |

### <a name="usererrorhanalsnvalidationfailure"></a>사용자 오류HANALSN유효성 검사실패

| 오류 메시지      | <span style="font-weight:normal">백업 로그 체인이 끊어졌습니다.</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 로그 백업 대상이 백인트에서 파일 시스템으로 업데이트되었거나 백인트 실행 파일이 변경되었을 수 있습니다. |
| **권장 작업** | 전체 백업을 트리거하여 문제 해결                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>사용자에 의한 인컴인컴SrcTargetSystsemsemSemSEmSEm복원

| 오류 메시지      | <span style="font-weight:normal">복원을 위한 소스 및 대상 시스템은 호환되지 않습니다.</span>    |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 복원 대상 시스템이 원본과 호환되지 않습니다. |
| **권장 작업** | 현재 지원되는 복원 유형에 대해 알아보려면 SAP Note [1642148을](https://launchpad.support.sap.com/#/notes/1642148) 참조하십시오. |

### <a name="usererrorsdctomdcupgradedetected"></a>사용자 오류SDCtoMDC업그레이드감지

| 오류 메시지      | <span style="font-weight:normal">SDC에서 MDC로의 업그레이드감지</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 인스턴스가 SDC에서 MDC로 업그레이드되었습니다. 업데이트 후 백업이 실패합니다. |
| **권장 작업** | [SAP HANA 1.0에서 2.0으로 업그레이드 단계에](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) 나와 문제를 해결하십시오. |

### <a name="usererrorinvalidbackintconfiguration"></a>사용자 오류유효백핀트 구성

| 오류 메시지      | <span style="font-weight:normal">잘못된 백인트 구성 이 검출</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | Azure 백업에 백업 매개 변수가 잘못 지정되었습니다. |
| **권장 작업** | 다음(backint) 매개 변수가 설정되어 있는지 확인합니다.<br/>\*[catalog_backup_using_backint:true]<br/>\*[enable_accumulated_catalog_backup:거짓]<br/>\*【parallel_data_backup_backint_channels:1】<br/>\*[log_backup_timeout_s:900)]<br/>\*[backint_response_timeout:7200]<br/>host에 백인트 기반 매개 변수가 있는 경우 제거합니다. 매개 변수가 HOST 수준에 없지만 데이터베이스 수준에서 수동으로 수정된 경우 앞서 설명한 대로 해당 값으로 되돌리십시오. 또는 차단 보호를 실행하고 Azure 포털에서 [백업 데이터를 유지한](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) 다음 **백업 백업 을**선택합니다. |

## <a name="restore-checks"></a>검사 복원

### <a name="single-container-database-sdc-restore"></a>단일 컨테이너 데이터베이스(SDC) 복원

HANA의 단일 컨테이너 데이터베이스(SDC)를 다른 SDC 컴퓨터로 복원하는 동안 입력을 처리합니다. 데이터베이스 이름은 소문자와 괄호에 "sdc"가 추가되어 있어야 합니다. HANA 인스턴스는 대문자로 표시됩니다.

SDC HANA 인스턴스 "H21"이 백업된다고 가정합니다. 백업 항목 페이지에는 백업 항목 이름이 **"h21(sdc)"로**표시됩니다. H11과 같은 다른 대상 SDC로 이 데이터베이스를 복원하려는 경우 다음 입력을 제공해야 합니다.

![복원된 SDC 데이터베이스 이름](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

다음 사항에 유의하세요.

- 기본적으로 복원된 db 이름은 백업 항목 이름으로 채워집니다. 이 경우 h21(sdc)을 입력합니다.
- 대상을 H11로 선택하면 복원된 db 이름이 자동으로 변경되지 않습니다. **h11(sdc)으로 편집해야 합니다.** SDC와 관련하여 복원된 db 이름은 소문자가 있는 대상 인스턴스 ID이고 괄호에 'sdc'가 추가됩니다.
- SDC에는 단일 데이터베이스만 있을 수 있으므로 확인란을 클릭하여 복구 지점 데이터로 기존 데이터베이스 데이터를 재정의할 수도 있습니다.
- 리눅스는 대/소문자를 구분합니다. 그래서 케이스를 보존하기 위해주의하십시오.

### <a name="multiple-container-database-mdc-restore"></a>여러 컨테이너 데이터베이스(MDC) 복원

HANA에 대한 여러 컨테이너 데이터베이스에서 표준 구성은 SYSTEMDB + 1 개 이상의 테넌트 DB입니다. 전체 SAP HANA 인스턴스를 복원한다는 것은 SYSTEMDB 및 테넌트 DB를 모두 복원하는 것을 의미합니다. 하나는 SYSTEMDB를 먼저 복원한 다음 테넌트 DB에 대해 진행합니다. 시스템 DB는 기본적으로 선택한 대상의 시스템 정보를 재정의하는 것을 의미합니다. 이 복원은 대상 인스턴스의 BackInt 관련 정보도 재정의합니다. 따라서 시스템 DB가 대상 인스턴스로 복원된 후 사전 등록 스크립트를 다시 실행합니다. 그런 다음에만 후속 테넌트 DB 복원이 성공합니다.

## <a name="upgrading-from-sap-hana-10-to-20"></a>SAP HANA 1.0에서 2.0으로 업그레이드

SAP HANA 1.0 데이터베이스를 보호하고 2.0으로 업그레이드하려면 다음 단계를 수행하십시오.

- 이전 SDC 데이터베이스에 대한 유지 데이터로 [보호를 중지합니다.](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 업그레이드를 수행합니다. 완료 후, HANA는 이제 시스템 DB및 테넌트 DB(들)와 함께 MDC가 되었습니다.
- (시드 및 mdc)의 정확한 세부 정보와 [사전 등록 스크립트를](https://aka.ms/scriptforpermsonhana) 다시 실행합니다.
- Azure 포털에서 동일한 컴퓨터에 대한 확장을 다시 등록합니다(백업 -> 보기 세부 정보 -> 관련 Azure VM -> 다시 등록).
- 동일한 VM에 대해 다시 검색 된 B를 클릭합니다. 이 작업은 2단계에서 새 DB에 올바른 세부 정보(SDC가 아닌 SYSTEMDB 및 테넌트 DB)가 표시됩니다.
- 이러한 새 데이터베이스에 대한 백업을 구성합니다.

## <a name="upgrading-without-an-sid-change"></a>SID 변경 없이 업그레이드

SID 변경을 일으키지 않는 OS 또는 SAP HANA로의 업그레이드는 아래에 설명된 대로 처리할 수 있습니다.

- 데이터베이스에 대한 유지 데이터로 [보호 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 업그레이드를 수행합니다.
- [사전 등록 스크립트를](https://aka.ms/scriptforpermsonhana)다시 실행합니다. 일반적으로 업그레이드 프로세스가 필요한 역할을 제거하는 것을 보았습니다. 사전 등록 스크립트를 실행하면 필요한 모든 역할을 확인하는 데 도움이 됩니다.
- 데이터베이스 보호를 다시 [다시 시작](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="re-registration-failures"></a>재등록 실패

다시 등록 작업을 트리거하기 전에 다음 증상 중 하나 이상을 확인합니다.

- 모든 작업(예: 백업, 복원 및 백업 구성)이 다음 오류 코드 중 하나를 사용 하 여 VM에서 실패: **워크로드ExtensionNotReachable, UserErrorWorkloadExtension설치, 워크로드ExtensionNotPresent, 워크로드ExtensionDidntDequeueMsg**.
- 백업 항목의 **백업 상태** 영역에 **연결할 수 없는**상태가 표시되면 동일한 상태가 될 수 있는 다른 모든 원인을 제외합니다.

  - VM에서 백업 관련 작업을 수행할 수 있는 권한이 부족합니다.
  - VM이 종료되므로 백업을 수행 할 수 없습니다.
  - 네트워크 문제

이러한 증상은 다음 이유 중 하나 이상 발생할 수 있습니다.

- 확장이 포털에서 삭제되었거나 제거되었습니다.
- VM은 현재 디스크 복원을 통해 복원되었습니다.
- VM이 오랜 기간 동안 종료되었기 때문에 VM의 확장 구성이 만료되었습니다.
- VM이 삭제되고 다른 VM이 동일한 이름으로 삭제된 VM과 동일한 리소스 그룹에서 만들어졌습니다.

앞의 시나리오에서는 VM에서 다시 등록 작업을 트리거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- Azure VM에서 SAP HANA 데이터베이스 백업에 대해 [자주 묻는 질문을](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) 검토합니다.
