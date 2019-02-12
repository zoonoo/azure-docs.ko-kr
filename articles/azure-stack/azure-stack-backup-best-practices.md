---
title: Azure Stack 인프라 백업 서비스 모범 사례 | Microsoft Docs
description: 배포 하 고 치명적인 오류가 발생 하는 경우 데이터 손실 위험을 완화 하려면 데이터 센터에서 Azure Stack을 관리 하는 경우 몇 가지 모범 사례를 따르면 됩니다.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: d2568a4dfc4fefe9628fc63dcc0526b0876fde00
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993880"
---
# <a name="infrastructure-backup-service-best-practices"></a>인프라 Backup 서비스에 대 한 유용한 정보

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

배포 하 고 치명적인 오류가 발생할 경우 데이터 손실 위험을 완화 하려면 데이터 센터에서 Azure Stack을 관리 하는 경우 모범 사례를 따르면 됩니다.

작업 흐름에 변경 사항이 있을 경우 규정 준수에 설치 되었는지 확인 하려면 일정 한 간격으로 모범 사례를 검토 해야 합니다. 이러한 모범 사례를 구현 하는 동안 문제가 발생 하거나, 도움말에 대 한 Microsoft 지원에 문의 합니다.

## <a name="configuration-best-practices"></a>구성 모범 사례

### <a name="deployment"></a>배포

각 Azure Stack 클라우드에 배포 된 후 인프라 Backup을 사용 합니다. Azure Stack PowerShell을 사용 하 여 연산자 관리 API 끝점에 대 한 액세스를 사용 하 여 모든 클라이언트/서버에서 백업을 예약할 수 있습니다.

### <a name="networking"></a>네트워킹

경로 대 한 범용 명명 규칙 (UNC) 문자열 정규화 된 도메인 이름 (FQDN)을 사용 해야 합니다. IP 주소는 이름 확인 가능 하지 않은 경우에 발생 합니다. UNC 문자열로 공유 파일 또는 장치와 같은 리소스의 위치를 지정합니다.

### <a name="encryption"></a>암호화

#### <a name="version-1901-and-newer"></a>1901 이상 버전

암호화 인증서는 외부 저장소에 내보내지는 백업 데이터를 암호화에 사용 됩니다. 인증서 키를 전송 하는 인증서만 사용 되므로 자체 서명 된 인증서를 수 있습니다. 인증서를 만드는 방법에 대 한 자세한 내용은 New-selfsignedcertificate를 가리킵니다.  
키를 안전한 위치 (예: 전역 Azure Key Vault 인증서)에 저장 되어야 합니다. 데이터를 암호화 하는 인증서의 CER 형식으로 사용 됩니다. 백업 데이터를 해독 하는 PFX 형식으로 Azure Stack 클라우드 복구 배포 중 사용 되어야 합니다.

![안전한 위치에 인증서를 저장 합니다.](media/azure-stack-backup/azure-stack-backup-encryption-store-cert.png)

#### <a name="1811-and-older"></a>1811 및 이전

암호화 키를 외부 저장소에 내보내지는 백업 데이터를 암호화에 사용 됩니다. 키의 일부로 생성 됩니다 [PowerShell 사용 하 여 Azure Stack에 백업을 사용 하도록](azure-stack-backup-enable-backup-powershell.md)합니다.

키를 안전한 위치 (예: 전역 Azure Key Vault 비밀)에 저장 되어야 합니다. Azure Stack 다시 배포 하는 동안이 키를 사용 해야 합니다. 

![키를 안전한 위치를 저장 합니다.](media/azure-stack-backup/azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>운영 모범 사례

### <a name="backups"></a>Backup

 - 백업 작업에는 관리 환경 또는 사용자 응용 프로그램에 가동 중지 시간 없이 이므로 시스템이 실행 되는 동안 실행 합니다. 20-40 분는 적절 한 부하가 있는 솔루션에 대 한 백업 작업을 예상 합니다.
 - 지침을 제공 하는 OEM을 사용 하 여 수동으로 백업 네트워크 스위치와 하드웨어 수명 주기 호스트 (HLH) 저장 되어야 합니다는 인프라 백업 컨트롤러 저장소 제어 평면 백업 데이터 같은 백업 공유에. 스위치 및 HLH 구성 영역 폴더에 저장 하는 것이 좋습니다. Azure Stack의 여러 인스턴스를 동일한 지역에 있는 경우에 배율 단위에 속하는 각 구성에 대 한 식별자를 사용 하는 것이 좋습니다.

### <a name="folder-names"></a>폴더 이름

 - 인프라는 MASBACKUP 폴더를 자동으로 만듭니다. Microsoft 관리 공유입니다. MASBACKUP와 동일한 수준에 공유를 만들 수 있습니다. 폴더를 만들거나 Azure Stack을 만들지 않는 MASBACKUP 내부 데이터 저장소는 권장 되지 않습니다. 
 -  사용자 FQDN 및 폴더 이름에는 지역에서 다른 클라우드 백업 데이터를 구분 하기 위해. Azure Stack 배포 및 끝점의 정규화 된 도메인 이름 (FQDN)에 영역 매개 변수 및 외부 도메인 이름 매개 변수 조합입니다. 자세한 내용은 [Azure Stack 데이터 센터 통합-DNS](azure-stack-integrate-dns.md)합니다.

예를 들어 백업 공유가 AzSBackups fileserver01.contoso.com에서 호스트 합니다. 파일 공유를 사용 하는 외부 도메인 이름 및 영역 이름을 사용 하는 하위 폴더를 사용 하 여 Azure Stack 배포 당 폴더 있을 수 있습니다. 

FQDN: contoso.com  
지역: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup 폴더는 Azure Stack 백업 데이터를 저장 하는 위치입니다. 사용자 고유의 데이터를 저장 하려면이 폴더를 사용 하지 마십시오. OEM 하거나 모든 백업 데이터를 저장 하려면이 폴더를 사용 해야 합니다. 

Oem 지역 폴더 아래에 있는 해당 구성 요소에 대 한 백업 데이터를 저장 하는 것이 좋습니다. 각 네트워크 스위치, 하드웨어 수명 주기 호스트 (HLH) 등에 해당 하위 폴더에 저장할 수 있습니다. 예: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>모니터링

다음 경고는 시스템에서 지원 됩니다.

| 경고                                                   | 설명                                                                                     | 재구성                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| 파일 공유 용량이 부족 하기 때문에 백업 실패 | 파일 공유 용량이 부족 이며 백업 컨트롤러 위치에 백업 파일을 내보낼 수 없습니다. | 더 많은 저장소 용량을 추가 하 고 백업을 다시 시도 하세요. 공간 확보 (부터 가장 오래 된에서) 기존 백업을 삭제 합니다.                    |
| 연결 문제로 인해 백업 하지 못했습니다.             | 네트워크 사이의 Azure Stack 및 파일 공유 문제가 발생 했습니다.                          | 네트워크 문제를 해결 하 고 백업을 다시 시도 합니다.                                                                                            |
| 경로의 오류로 인해 백업이 실패 했습니다.                | 파일 공유 경로 확인할 수 없습니다.                                                          | 공유에 액세스할 수 있도록 다른 컴퓨터에서 공유를 매핑하십시오. 더 이상 유효 하는 경우 경로 업데이트 해야 합니다.       |
| 인증 문제로 인해 백업 하지 못했습니다.               | 자격 증명을 사용 하 여 문제 또는 인증에 영향을 주는 네트워크 문제일 수 있습니다.    | 공유에 액세스할 수 있도록 다른 컴퓨터에서 공유를 매핑하십시오. 더 이상 유효 하지 않은 경우 자격 증명을 업데이트 해야 합니다. |
| 일반 오류로 인해 백업이 실패 했습니다.                    | 실패 한 요청은 일시적인 문제 때문일 수 있습니다. 백업 다시 시도 합니다.                    | 지원 호출                                                                                                                               |

## <a name="next-steps"></a>다음 단계

에 대 한 참조 자료를 검토 합니다 [인프라 Backup 서비스](azure-stack-backup-reference.md)

사용 하도록 설정 된 [인프라 백업 서비스](azure-stack-backup-enable-backup-console.md)
