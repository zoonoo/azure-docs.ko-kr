---
title: 오프 라인 백업 개요
description: 오프 라인 백업 구성 요소에 대해 알아봅니다. 여기에는 Azure Import/Export 서비스를 기반으로 하는 Azure Data Box 및 오프 라인 백업을 기반으로 하는 오프 라인 백업이 포함 됩니다.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: a33810c633d8c7ca79c559355935d3c11853bc0a
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/26/2020
ms.locfileid: "82160924"
---
# <a name="overview-of-offline-backup"></a>오프 라인 백업 개요

이 문서는 오프 라인 백업에 대 한 개요를 제공 합니다.

Azure에 대 한 초기 전체 백업은 일반적으로 많은 양의 데이터를 온라인으로 전송 하며 증분 변경 내용만 전송 하는 후속 백업과 비교할 때 더 많은 네트워크 대역폭이 필요 합니다. 특정 지역의 원격 사무실 또는 데이터 센터에는 항상 네트워크 대역폭이 충분 하지 않습니다. 이러한 이유로 이러한 초기 백업은 며칠 정도 걸립니다. 이 시간 동안 백업은 온-프레미스 데이터 센터에서 실행 되는 응용 프로그램에 대해 프로 비전 된 동일한 네트워크를 계속 사용 합니다.

Azure Backup는 네트워크 대역폭을 사용 하지 않고 오프 라인으로 초기 백업 데이터를 전송 하는 오프 라인 백업을 지원 합니다. 백업 데이터를 실제 저장 장치에 복사 하는 메커니즘을 제공 합니다. 그러면 장치가 가까운 Azure 데이터 센터에 배송 되어 Recovery Services 자격 증명 모음에 업로드 됩니다. 이 프로세스를 통해 네트워크 대역폭을 사용 하지 않고 백업 데이터를 강력 하 게 전송할 있습니다.

## <a name="offline-backup-options"></a>오프 라인 백업 옵션

오프 라인 백업은 다음과 같은 두 가지 모드로 저장 장치의 소유권을 기반으로 제공 됩니다.

- Azure Data Box 기반 오프 라인 백업 (미리 보기)
- Azure Import/Export 서비스를 기반으로 하는 오프 라인 백업

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Azure Data Box 기반 오프 라인 백업 (미리 보기)

이 모드는 현재 Microsoft Azure Recovery Services (MARS) 에이전트와 미리 보기에서 지원 됩니다. 이 옵션은 [Azure Data Box](https://azure.microsoft.com/services/databox/) 를 활용 하 여 데이터 센터 또는 원격 사무실에 대 한 USB 커넥터를 통해 Microsoft 독점, 안전 하 고 변조 방지 전송 어플라이언스를 제공 합니다. 백업 데이터는 이러한 장치에 직접 기록 됩니다. 이 옵션은 Azure 호환 디스크 및 커넥터를 제공 하거나 임시 저장소를 스테이징 위치로 프로 비전 하는 데 필요한 노력을 절감 합니다. 또한 Microsoft는 Azure Portal를 통해 추적할 수 있는 종단 간 전송 물류를 처리 합니다.

이 옵션을 사용 하 여 백업 데이터 이동을 설명 하는 아키텍처가 여기에 표시 됩니다.

![Azure Backup Data Box 아키텍처](./media/offline-backup-overview/azure-backup-databox-architecture.png)

아키텍처에 대 한 요약은 다음과 같습니다.

1. Azure Backup는 백업 데이터를 미리 구성 된 장치에 직접 복사 합니다.
2. 그런 다음 이러한 장치를 Azure 데이터 센터에 다시 제공할 수 있습니다.
3. Azure Data Box 고객 소유의 저장소 계정에 데이터를 복사 합니다.
4. Azure Backup는 저장소 계정에서 지정 된 Recovery Services 자격 증명 모음으로 백업 데이터를 자동으로 복사 합니다. 증분 온라인 백업이 예약 되어 있습니다.

Azure Data Box 기반으로 하는 오프 라인 백업을 사용 하려면 [Azure Data Box를 사용 하 여 오프 라인 백업](offline-backup-azure-data-box.md)을 참조 하세요.

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Azure Import/Export 서비스를 기반으로 하는 오프 라인 백업

이 옵션은 MABS (Microsoft Azure Backup 서버), System Center Data Protection Manager (DPM) DPM-A 및 MARS 에이전트에서 지원 됩니다. [Azure Import/Export 서비스](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)를 사용 합니다. 사용자 고유의 Azure 호환 디스크 및 커넥터를 사용 하 여 초기 백업 데이터를 Azure에 전송할 수 있습니다. 이 접근 방식을 사용 하려면 스테이징 위치로 알려진 임시 저장소를 프로 비전 하 고 미리 작성 된 유틸리티를 사용 하 여 백업 데이터를 포맷 하 고 고객 소유의 디스크에 복사 해야 합니다.

이 옵션을 사용 하 여 백업 데이터 이동을 설명 하는 아키텍처가 여기에 표시 됩니다.

![Azure Backup 가져오기/내보내기 서비스 아키텍처](./media/offline-backup-overview/azure-backup-import-export.png)

아키텍처에 대 한 요약은 다음과 같습니다.

1. Azure Backup는 네트워크를 통해 백업 데이터를 전송 하는 대신 백업 데이터를 준비 위치에 기록 합니다.
2. 준비 위치의 데이터는 사용자 지정 유틸리티를 사용 하 여 하나 이상의 SATA 디스크에 기록 됩니다.
3. 이 유틸리티는 준비 작업의 일부로 Azure 가져오기 작업을 만듭니다. SATA 드라이브는 가장 가까운 Azure 데이터 센터로 배송 되며 작업을 연결 하는 가져오기 작업을 참조 합니다.
4. Azure 데이터 센터에서 디스크의 데이터는 Azure Storage 계정에 복사됩니다.
5. Azure Backup는 저장소 계정에서 Recovery Services 자격 증명 모음으로 백업 데이터를 복사 합니다. 증분 백업이 예약 되어 있습니다.

MARS 에이전트를 사용 하 여 Azure Import/Export 서비스를 기반으로 하는 오프 라인 백업을 사용 하려면 [Azure Backup의 오프 라인 백업 워크플로](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)를 참조 하세요.

MABS 또는 DPM과 함께 동일한를 사용 하려면 [dpm 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export-)를 참조 하세요.

## <a name="offline-backup-support-summary"></a>오프 라인 백업 지원 요약

다음 표에서는 시나리오에 따라 적절 한 선택을 가능 하 게 하는 두 가지 옵션을 비교 합니다.

| **고려 사항**                                            | **Azure Data Box 기반 오프 라인 백업**                     | **Azure Import/Export 서비스를 기반으로 하는 오프 라인 백업**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup 배포 모델                              | MARS 에이전트 (미리 보기)                                              | MARS 에이전트, MABS, DPM-A                                           |
| 서버당 최대 백업 데이터 (MARS) 또는 보호 그룹당 (MABS, DPM-A) | [Azure Data Box 디스크](https://docs.microsoft.com/azure/databox/data-box-disk-overview) -7.2 TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) -80 TB       | 80 TB (각각 8tb의 최대 10 개 디스크)                          |
| 보안 (데이터, 장치 및 서비스)                           | [데이터](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) -AES 256 비트 암호화 됨 <br> 데이터를 복사 하기 위한 [장치](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) 견고한 사례, 소유, 자격 증명 기반 인터페이스 <br> Azure 보안 기능으로 [서비스](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) 보호 | 데이터-BitLocker 암호화                                 |
| 임시 스테이징 위치 프로 비전                     | 필요하지 않음                                                | 예상 백업 데이터 크기 보다 크거나 같음        |
| 지원되는 지역                                           | [Azure Data Box 디스크 영역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box 지역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure Import/Export 서비스 지역](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 국가 간 배송                                     | 지원되지 않음  <br>    원본 주소와 대상 Azure 데이터 센터는 동일한 국가에 있어야 합니다. * | 지원 여부                                                    |
| 전송 물류 (배달, 전송, 픽업)           | 완전 한 Microsoft 관리                                     | 고객 관리                                            |
| 가격 책정                                                      | [Azure Data Box 가격 책정](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box 디스크 가격 책정](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure Import/Export 서비스 가격 책정](https://azure.microsoft.com/pricing/details/storage-import-export/) |

* 사용자의 국가에 Azure 데이터 센터가 없으면 다른 국가의 Azure 데이터 센터에 디스크를 배송 해야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Box를 사용 하 여 오프 라인 백업 Azure Backup](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure Backup의 오프 라인 백업 워크플로](backup-azure-backup-import-export.md)
- [DPM 및 Azure Backup Server에 대 한 오프 라인 백업 워크플로](backup-azure-backup-server-import-export-.md)
