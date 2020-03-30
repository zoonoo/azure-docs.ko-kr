---
title: 오프라인 백업 개요
description: 오프라인 백업의 구성 요소에 대해 알아봅니다. 여기에는 Azure 데이터 상자를 기반으로 하는 오프라인 백업과 Azure 가져오기/내보내기 서비스를 기반으로 하는 오프라인 백업이 포함됩니다.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 4dae68c46e0072a726bba13139e405b44be0f008
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78196169"
---
# <a name="overview-of-offline-backup"></a>오프라인 백업 개요

이 문서에서는 오프라인 백업에 대한 개요를 제공합니다.

Azure에 대한 초기 전체 백업은 일반적으로 대량의 데이터를 온라인으로 전송하며 증분 변경만 전송하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요합니다. 특정 지역의 원격 사무실이나 데이터 센터에 항상 충분한 네트워크 대역폭이 있는 것은 아닙니다. 이러한 이유로 이러한 초기 백업에는 며칠이 걸릴 수 있습니다. 이 시간 동안 백업은 온-프레미스 데이터 센터에서 실행되는 응용 프로그램에 대해 프로비전된 것과 동일한 네트워크를 계속 사용합니다.

Azure Backup은 네트워크 대역폭을 사용하지 않고 초기 백업 데이터를 오프라인으로 전송하는 오프라인 백업을 지원합니다. 백업 데이터를 물리적 저장 장치에 복사하는 메커니즘을 제공합니다. 그런 다음 장치는 근처의 Azure 데이터 센터로 배송되고 복구 서비스 자격 증명 모음에 업로드됩니다. 이 프로세스는 네트워크 대역폭을 사용하지 않고 백업 데이터를 강력하게 전송할 수 있도록 합니다.

## <a name="offline-backup-options"></a>오프라인 백업 옵션

오프라인 백업은 저장 장치의 소유권에 따라 두 가지 모드로 제공됩니다.

- Azure 데이터 상자를 기반으로 하는 오프라인 백업(미리 보기)
- Azure 가져오기/내보내기 서비스를 기반으로 하는 오프라인 백업

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Azure 데이터 상자를 기반으로 하는 오프라인 백업(미리 보기)

이 모드는 현재 미리 보기에서 Microsoft Azure 복구 서비스(MARS) 에이전트에서 지원됩니다. 이 옵션은 [Azure 데이터 박스를](https://azure.microsoft.com/services/databox/) 활용하여 USB 커넥터가 있는 Microsoft 독점, 보안 및 변조 방지 전송 어플라이언스를 데이터 센터 또는 원격 사무실로 제공합니다. 백업 데이터는 이러한 장치에 직접 기록됩니다. 이 옵션을 사용하면 Azure 호환 디스크 및 커넥터를 조달하거나 임시 저장소를 스테이징 위치로 프로비전하는 데 필요한 노력을 줄일 수 있습니다. Microsoft는 Azure 포털을 통해 추적할 수 있는 종단 간 전송 물류도 처리합니다. 

이 옵션을 사용하여 백업 데이터의 이동을 설명하는 아키텍처가 여기에 표시됩니다.

![Azure 백업 데이터 박스 아키텍처](./media/offline-backup-overview/azure-backup-databox-architecture.png)

다음은 아키텍처에 대한 요약입니다.

1. Azure Backup은 미리 구성된 장치에 백업 데이터를 직접 복사합니다.
2. 그런 다음 이러한 장치를 Azure 데이터 센터로 다시 발송할 수 있습니다.
3. Azure 데이터 상자는 데이터를 고객 소유 저장소 계정에 복사합니다.
4. Azure Backup은 저장소 계정에서 지정된 복구 서비스 자격 증명 모음으로 백업 데이터를 자동으로 복사합니다. 증분 온라인 백업이 예약됩니다.

Azure 데이터 상자를 기반으로 오프라인 백업을 사용하려면 [Azure 데이터 상자를 사용하여 오프라인 백업을](offline-backup-azure-data-box.md)참조하십시오.

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Azure 가져오기/내보내기 서비스를 기반으로 하는 오프라인 백업

이 옵션은 Microsoft Azure 백업 서버(MABS), 시스템 센터 데이터 보호 관리자(DPM) DPM-A 및 MARS 에이전트에서 지원됩니다. [Azure 가져오기/내보내기 서비스를](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)사용합니다. 자체 Azure 호환 디스크 및 커넥터를 사용하여 초기 백업 데이터를 Azure로 전송할 수 있습니다. 이 방법을 사용하려면 스테이징 위치로 알려진 임시 저장소를 프로비전하고 미리 빌드된 유틸리티를 사용하여 백업 데이터를 고객 소유 디스크에 포맷하고 복사해야 합니다. 

이 옵션을 사용하여 백업 데이터의 이동을 설명하는 아키텍처가 여기에 표시됩니다.

![Azure 백업 가져오기/내보내기 서비스 아키텍처](./media/offline-backup-overview/azure-backup-import-export.png)

다음은 아키텍처에 대한 요약입니다.

1. Azure Backup은 네트워크를 통해 백업 데이터를 보내는 대신 백업 데이터를 스테이징 위치에 씁니다.
2. 스테이징 위치의 데이터는 사용자 지정 유틸리티를 사용하여 하나 이상의 SATA 디스크에 기록됩니다.
3. 준비 작업의 일부로 유틸리티는 Azure 가져오기 작업을 만듭니다. SATA 드라이브는 가장 가까운 Azure 데이터 센터로 배송되고 가져오기 작업을 참조하여 활동을 연결합니다.
4. Azure 데이터 센터에서 디스크의 데이터는 Azure Storage 계정에 복사됩니다.
5. Azure Backup은 저장소 계정에서 복구 서비스 자격 증명 모음으로 백업 데이터를 복사합니다. 증분 백업이 예약됩니다.

MARS 에이전트와 함께 Azure 가져오기/내보내기 서비스를 기반으로 오프라인 백업을 사용하려면 [Azure Backup에서 오프라인 백업 워크플로를](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)참조하십시오.

MABS 또는 DPM-A와 함께 동일한 를 사용하려면 [DPM 및 Azure 백업 서버에 대한 오프라인 백업 워크플로를](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)참조하십시오.

## <a name="offline-backup-support-summary"></a>오프라인 백업 지원 요약

다음 표에서는 시나리오에 따라 적절한 옵션을 선택할 수 있도록 사용 가능한 두 옵션을 비교합니다.

| **고려 사항**                                            | **Azure 데이터 상자를 기반으로 하는 오프라인 백업**                     | **Azure 가져오기/내보내기 서비스를 기반으로 하는 오프라인 백업**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure 백업 배포 모델                              | 화성 에이전트 (미리보기)                                              | 화성 에이전트, MABS, DPM-A                                           |
| 서버당 최대 백업 데이터(MARS) 또는 보호 그룹당(MABS, DPM-A) | [Azure 데이터 상자 디스크](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2 TB <br> [Azure 데이터 상자](https://docs.microsoft.com/azure/databox/data-box-overview) - 80TB       | 80TB(각 8TB의 최대 10개의 디스크)                          |
| 보안(데이터, 장치 및 서비스)                           | [데이터](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256비트 암호화 <br> [장치](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - 데이터를 복사하는 견고한 케이스, 독점 자격 증명 기반 인터페이스 <br> [서비스](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Azure 보안 기능으로 보호 | 데이터 - 비트락커 암호화                                 |
| 임시 스테이징 위치 프로비저닝                     | 필요하지 않음                                                | 예상 백업 데이터 크기 이상 또는 같음        |
| 지원되는 지역                                           | [Azure 데이터 박스 디스크 영역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure 데이터 상자 지역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure 가져오기/내보내기 서비스 영역](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 크로스 컨트리 배송                                     | 지원되지 않음  <br>    원본 주소 및 대상 Azure 데이터 센터는 동일한 국가에 있어야 합니다.* | 지원됨                                                    |
| 물류 운송(배송, 운송, 픽업)           | 완전 마이크로 소프트 관리                                     | 고객 관리                                            |
| 가격 책정                                                      | [Azure 데이터 박스 가격 책정](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure 데이터 박스 디스크 가격 책정](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure 가져오기/내보내기 서비스 가격 책정](https://azure.microsoft.com/pricing/details/storage-import-export/) |

*국가에 Azure 데이터 센터가 없는 경우 디스크를 다른 국가의 Azure 데이터 센터로 보내야 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 상자를 사용 하 여 Azure 백업 오프라인 백업](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
* [Azure 백업의 오프라인 백업 워크플로우](backup-azure-backup-import-export.md) 
* [DPM 및 Azure 백업 서버에 대한 오프라인 백업 워크플로우](backup-azure-backup-server-import-export-.md)
