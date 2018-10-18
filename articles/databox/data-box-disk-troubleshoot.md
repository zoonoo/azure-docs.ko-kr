---
title: Azure Data Box Disk 문제 해결 | Microsoft Docs
description: Azure Data Box Disk에서 발견된 문제 해결 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.topic: overview
ms.date: 10/09/2018
ms.author: alkohli
ms.openlocfilehash: 776108b109bc27e0f8059d287e87c67aeca9fbd2
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091853"
---
# <a name="troubleshoot-issues-in-azure-data-box-disk-preview"></a>Azure Data Box Disk에서 문제 해결(미리 보기)

이 아티클은 미리 보기 릴리스를 실행하는 Microsoft Azure Data Box에 적용됩니다. 이 아티클에서는 Data Box 및 Data Box Disk에서 수행할 수 있는 복잡한 워크플로 및 관리 작업 중 일부를 설명합니다. 

Azure Portal을 통해 Data Box Disk를 관리할 수 있습니다. 이 아티클은 Azure Portal을 사용하여 수행할 수 있는 작업에 중점을 둡니다. Azure Portal을 사용하여 주문을 관리하고, 장치를 관리하고, 완료 단계가 진행될 때 주문 상태를 추적합니다.

이 문서에는 다음 자습서가 포함되어 있습니다.

- 진단 로그 다운로드
- 활동 로그 쿼리


> [!IMPORTANT]
> Data Box는 미리 보기로 제공됩니다. 이 솔루션을 배포하기 전에 [미리 보기에 대한 Azure 서비스 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 검토하세요.

## <a name="download-diagnostic-logs"></a>진단 로그 다운로드

데이터 복사 프로세스 중 오류가 발생한 경우 포털에서는 진단 로그가 위치한 폴더에 대한 경로를 표시합니다. 

진단 로그는 다음 항목일 수 있습니다.
- 오류 로그
- 자세한 정보 표시 로그  

복사 로그에 대한 경로로 이동하려면 Data Box 주문과 연결된 저장소 계정으로 이동합니다. 

1.  **일반 > 주문 세부 정보**로 이동하고 주문과 연결된 저장소 계정을 기록해둡니다.
 

2.  **모든 리소스**로 이동하고 이전 단계에서 식별된 저장소 계정을 검색합니다. 저장소 계정을 선택하고 클릭합니다.

    ![복사 로그 1](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs1.png)

3.  **Blob service > Blob 찾아보기**로 이동하고 저장소 계정에 해당하는 Blob을 찾습니다. **diagnosticslogcontainer > waies**로 이동합니다. 

    ![복사 로그 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

    데이터 복사본에 대한 오류 로그 및 자세한 정보 표시 로그가 모두 표시됩니다. 각 파일을 선택하고 클릭한 다음, 로컬 복사본을 다운로드합니다.

## <a name="query-activity-logs"></a>활동 로그 쿼리

활동 로그를 사용하여 문제를 해결할 때 오류를 찾거나 조직의 사용자가 리소스를 수정한 방법을 모니터링합니다. 활동 로그를 통해 다음 사항을 확인할 수 있습니다.

- 구독의 리소스에서 수행된 작업
- 작업을 시작한 사람입니다. 
- 작업이 발생한 시간
- 작업의 상태.
- 작업을 조사하는 데 도움이 될 수 있는 기타 속성 값

활동 로그에는 리소스에서 수행된 모든 쓰기 작업(예: PUT, POST, DELETE)이 포함되지만 읽기 작업(예: GET)은 포함되지 않습니다. 

활동 로그는 90일 동안 유지됩니다. 시작 날짜가 90보다 더 오래되지 않은 경우 날짜 범위에 대해 쿼리할 수 있습니다. 인사이트에서 기본 제공 쿼리 중 하나로 필터링할 수도 있습니다. 예를 들어 오류를 클릭한 다음, 특정 오류를 선택하고 클릭하여 근본 원인을 이해합니다.

## <a name="data-box-disk-unlock-tool-errors"></a>Data Box Disk 잠금 해제 도구 오류


| 오류 메시지/도구 동작      | 권장 사항                                                                                               |
|-------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------|
| 없음<br><br>Data Box Disk 잠금 해제 도구가 충돌합니다.                                                                            | Bitlocker가 설치되어 있지 않습니다. Data Box Disk 잠금 해제 도구를 실행하는 호스트 컴퓨터에는 BitLocker가 설치되어야 합니다.                                                                            |
| 현재 .Net Framework는 지원되지 않습니다. 지원되는 버전은 4.5 이상입니다.<br><br>도구는 메시지를 표시하며 종료됩니다.  | .NET 4.5가 설치되어 있지 않습니다. Data Box Disk 잠금 해제 도구를 실행하는 호스트 컴퓨터에서 .NET 4.5 이상을 설치합니다.                                                                            |
| 잠금을 해제하거나 볼륨을 확인할 수 없습니다. Microsoft 지원에 문의하세요.  <br><br>도구가 잠긴 드라이브의 잠금을 해제하거나 확인하지 못했습니다. | 도구는 제공된 암호를 사용하여 잠긴 드라이브의 잠금을 해제할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다.                                                |
| 다음 볼륨의 잠금을 해제하고 확인합니다. <br>볼륨 드라이브 문자: E:<br>werwerqomnf, qwerwerqwdfda 암호를 사용하여 볼륨의 잠금을 해제할 수 없습니다. <br><br>도구는 일부 드라이브의 잠금을 해제하고 성공하거나 실패한 드라이브 문자를 나열합니다.| 부분적으로 성공 제공된 암호를 사용하여 일부 드라이브의 잠금을 해제할 수 없습니다. Microsoft 지원에 다음 단계를 문의합니다. |
| 잠긴 볼륨을 찾을 수 없습니다. Microsoft에서 받은 디스크가 제대로 연결되어 있고 잠긴 상태인지를 확인합니다.          | 도구는 잠긴 드라이브를 찾지 못했습니다. 드라이브는 이미 잠금 해제되거나 검색되지 않습니다. 드라이브가 연결되어 있고 잠겨 있는지 확인합니다.                                                           |
| 치명적인 오류: 잘못된 매개 변수<br>매개 변수 이름: invalid_arg<br>사용량:<br>DataBoxDiskUnlock /PassKeys:<passkey_list_separated_by_semicolon><br><br>예: DataBoxDiskUnlock /PassKeys:passkey1;passkey2;passkey3<br>예: DataBoxDiskUnlock /SystemCheck<br>예: DataBoxDiskUnlock /Help<br><br>/PassKeys: Azure Data Box Disk 순서에서 이 암호를 가져옵니다. 암호는 디스크를 잠금 해제합니다.<br>/Help: 이 옵션은 cmdlet 사용량 및 예제에 대한 도움말을 제공합니다.<br>/SystemCheck: 이 옵션은 시스템이 도구를 실행하기 위한 요구 사항을 충족하는지 확인합니다.<br><br>종료하려면 아무 키나 누르세요. | 잘못된 매개 변수가 입력되었습니다. 유일하게 허용된 매개 변수는 /SystemCheck, /PassKey 및 /Help입니다.                                                                            |

## <a name="data-box-disk-split-copy-tool-errors"></a>Data Box Disk 분할 복사 도구 오류

|오류 메시지/경고  |권장 사항 |
|---------|---------|
|[정보] 볼륨: m에 대한 BitLocker 암호 검색 중 <br>[오류] 볼륨: m에 대한 BitLocker 키를 검색하는 동안 예외가 발생함<br> 시퀀스에 요소가 없습니다.|이 오류는 대상 Data Box Disk가 오프라인인 경우 발생합니다. <br> 온라인 디스크에 `diskmgmt.msc` 도구를 사용합니다.|
|[오류] 예외가 발생함: WMI 작업 실패:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=제공된 복구 암호의 형식이 잘못되었습니다. <br>BitLocker 복구 암호는 48자릿수입니다. <br>복구 암호 형식이 올바른지 확인하고 다시 시도하십시오.|Data Box Disk 잠금 해제 도구를 사용하여 먼저 디스크 잠금을 해제하고 명령을 다시 시도합니다. 자세한 내용은 다음을 참조하세요. <li> [Windows 클라이언트에 대한 Data Box Disk 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) </li><li> [Linux 클라이언트에 대한 Data Box Disk 잠금 해제](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client) </li>|
|[오류] 예외가 발생함: 대상 드라이이브에 DriveManifest.xml 파일이 존재합니다. <br> 이는 다른 저널 파일을 사용하여 대상 드라이브가 준비되었을 수 있음을 나타냅니다. <br>동일한 드라이브에 데이터를 더 추가하려면 이전 저널 파일을 사용합니다. 기존 데이터를 삭제하고 새 가져오기 작업의 대상 드라이브를 다시 사용하려면 드라이브에서 DriveManifest.xml을 삭제합니다. 이 명령을 새 저널 파일을 사용하여 다시 실행합니다.| 이 오류는 다수의 가져오기 세션에 대해 동일한 드라이브 집합을 사용하려고 할 때 수신됩니다. <br> 하나의 드라이브 집합은 하나의 분할 및 복사 세션에만 사용하십시오.|
|[오류] 예외가 발생함: CopySessionId importdata-sept-test-1은 이전 복사 세션을 참조하며 새 복사 세션에 재사용 할 수 없습니다.|이 오류는 새 작업에 대해 이전에 성공적으로 완료된 직업과 동일한 작업 이름을 사용하려고 시도하면 보고됩니다.<br> 새 작업에 대해 고유한 이름을 제공하십시오.|
|[정보] 대상 파일 또는 디렉터리 이름이 NTFS 길이 한도를 초과합니다. |이 메시지는 파일 경로가 길어서 대상 파일의 이름이 바뀐 경우 보고됩니다.<br> 이 동작을 제어하려면 `config.json` 파일에서 disposition 옵션을 수정하십시오.|
|[오류] 예외가 발생함: JSON 이스케이프 시퀀스가 잘못되었습니다. |이 메시지는 config.json에 유효하지 않은 형식이 있을 때 보고됩니다. <br> 파일을 저장하기 전에 [JSONlint](https://jsonlint.com/)를 사용하여 `config.json`의 유효성을 검사하십시오.|



## <a name="next-steps"></a>다음 단계

- [Azure Portal을 통해 Data Box Disk를 관리](data-box-portal-ui-admin.md)하는 방법을 알아봅니다.
