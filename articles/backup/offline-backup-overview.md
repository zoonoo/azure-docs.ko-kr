---
title: 오프라인 백업 개요
description: 오프라인 백업의 구성 요소에 대해 알아봅니다. 여기에는 Azure Data Box 기반의 오프라인 백업과, Azure Import/Export 서비스를 기반의 오프라인 백업이 포함됩니다.
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 84f79efe10f867b37d1e3bb21363be4b12156615
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84628342"
---
# <a name="overview-of-offline-backup"></a>오프라인 백업 개요

이 문서는 오프라인 백업의 개요를 제공합니다.

초기 전체 백업은 일반적으로 많은 양의 데이터를 전송하며 증분 변경 내용만 전송하는 후속 백업에 비해 네트워크 대역폭이 더 많이 필요합니다. 특정 지역의 원격 사무실 또는 데이터 센터에는 네트워크 대역폭이 항상 충분한 것은 아닙니다. 이러한 이유로 이러한 초기 백업은 며칠 정도 걸립니다. 이 시간 동안 백업은 온-프레미스 데이터 센터에서 실행되는 애플리케이션에 대해 프로비저닝된 동일한 네트워크를 계속 사용합니다.

Azure Backup은 네트워크 대역폭을 사용하지 않고 오프라인으로 초기 백업 데이터를 전송하는 오프라인 백업을 지원합니다. 백업 데이터를 실제 스토리지 디바이스에 복사하는 메커니즘을 제공합니다. 그런 다음, 디바이스가 가까운 Azure 데이터 센터에 배송되어 Recovery Services 자격 증명 모음에 업로드됩니다. 이 프로세스를 통해 네트워크 대역폭을 사용하지 않고도 백업 데이터를 강력하게 전송할 수 있습니다.

## <a name="offline-backup-options"></a>오프라인 백업 옵션

오프라인 백업은 스토리지 디바이스의 소유권에 따라 다음과 같은 두 가지 모드로 제공됩니다.

- Azure Data Box 기반 오프라인 백업(미리 보기)
- Azure Import/Export 서비스 기반 오프라인 백업

## <a name="offline-backup-based-on-azure-data-box-preview"></a>Azure Data Box 기반 오프라인 백업(미리 보기)

이 모드는 현재 MARS(Microsoft Azure Recovery Services) 에이전트에서 미리 보기로 지원됩니다. 이 옵션은 [Azure Data Box](https://azure.microsoft.com/services/databox/)를 활용하여 USB 커넥터가 있는 Microsoft 고유의 안전하고 변조 방지된 전송 어플라이언스를 데이터 센터 또는 원격 사무실에 보냅니다. 백업 데이터는 이 디바이스에 직접 기록됩니다. 이 옵션에서는 자체 Azure 호환 디스크 및 커넥터를 마련하거나 임시 스토리지를 준비 위치로 프로비저닝하는 작업이 필요하지 않습니다. 또한 Microsoft가 엔드투엔드 전송 물류를 처리하므로 Azure Portal을 통해 추적할 수 있습니다.

이 옵션을 사용한 백업 데이터의 이동을 설명하는 아키텍처가 여기에 표시됩니다.

![Azure Backup Data Box 아키텍처](./media/offline-backup-overview/azure-backup-databox-architecture.png)

아키텍처의 요약은 다음과 같습니다.

1. Azure Backup은 백업 데이터를 이렇게 미리 구성된 디바이스에 복사합니다.
2. 그런 다음, 이 디바이스를 Azure 데이터 센터에 다시 보낼 수 있습니다.
3. Azure Data Box는 고객 소유의 스토리지 계정에 데이터를 복사합니다.
4. Azure Backup은 스토리지 계정의 백업 데이터를 지정된 Recovery Services 자격 증명 모음에 자동으로 복사합니다. 증분 온라인 백업이 예약됩니다.

Azure Data Box 기반의 오프라인 백업을 사용하려면 [Azure Data Box를 사용한 오프라인 백업](offline-backup-azure-data-box.md)을 참조하세요.

## <a name="offline-backup-based-on-the-azure-importexport-service"></a>Azure Import/Export 서비스 기반 오프라인 백업

이 옵션은 MABS(Microsoft Azure Backup Server), System Center DPM(Data Protection Manager) DPM-A 및 MARS 에이전트에서 지원됩니다. [Azure Import/Export서비스](https://docs.microsoft.com/azure/storage/common/storage-import-export-service)를 사용합니다. 사용자 고유의 Azure 호환 디스크 및 커넥터를 사용하여 초기 백업 데이터를 Azure에 전송할 수 있습니다. 이 접근 방식에서는 준비 위치라고 하는 임시 스토리지를 프로비저닝하고, 미리 작성된 유틸리티를 사용하여 포맷하고 백업 데이터를 고객 보유의 디스크에 복사해야 합니다.

이 옵션을 사용한 백업 데이터의 이동을 설명하는 아키텍처가 여기에 표시됩니다.

![Azure Backup Import/Export 서비스 아키텍처](./media/offline-backup-overview/azure-backup-import-export.png)

아키텍처의 요약은 다음과 같습니다.

1. 네트워크를 통해 백업 데이터를 보내는 대신 Azure Backup이 준비 위치에 백업 데이터를 기록합니다.
2. 준비 위치의 데이터는 사용자 지정 유틸리티를 통해 하나 이상의 SATA 디스크에 기록됩니다.
3. 준비 작업의 일환으로 유틸리티가 Azure 가져오기 작업을 만듭니다. SATA 드라이브를 가장 가까운 Azure 데이터 센터로 보내고 가져오기 작업을 참조하여 작업을 연결합니다.
4. Azure 데이터 센터에서 디스크의 데이터는 Azure Storage 계정에 복사됩니다.
5. Azure Backup은 스토리지 계정의 백업 데이터를 Recovery Services 자격 증명 모음에 복사합니다. 증분 백업이 예약됩니다.

MARS 에이전트를 통해 Azure Import/Export 서비스 기반의 오프라인 백업을 사용하려면 [Azure Backup의 오프라인 백업 워크플로](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)를 참조하세요.

같은 내용을 MABS 또는 DPM-A에 사용하려면 [DPM 및 Azure Backup Server용 오프라인 백업 워크플로](https://docs.microsoft.com/azure/backup/backup-azure-backup-server-import-export)를 참조하세요.

## <a name="offline-backup-support-summary"></a>오프라인 백업 지원 요약

다음 표에서는 시나리오에 따라 적절하게 선택할 수 있는 두 가지 사용 가능한 옵션을 비교합니다.

| **고려 사항**                                            | **Azure Data Box 기반 오프라인 백업**                     | **Azure Import/Export 서비스 기반 오프라인 백업**                |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Azure Backup 배포 모델                              | MARS 에이전트(미리 보기)                                              | MARS 에이전트, MABS, DPM-A                                           |
| 서버당(MARS) 또는 보호 그룹당(MABS, DPM-A) 최대 백업 데이터 | [Azure Data Box 디스크](https://docs.microsoft.com/azure/databox/data-box-disk-overview) - 7.2TB <br> [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) - 80TB       | 80TB(최대 10개 디스크, 디스크 각각 8TB)                          |
| 보안(데이터, 디바이스 및 서비스)                           | [데이터](https://docs.microsoft.com/azure/databox/data-box-security#data-box-data-protection) - AES 256비트 암호화 <br> [디바이스](https://docs.microsoft.com/azure/databox/data-box-security#data-box-device-protection) - 데이터 복사를 위해 견고한 케이스, 독점적, 자격 증명 기반 인터페이스 <br> [서비스](https://docs.microsoft.com/azure/databox/data-box-security#data-box-service-protection) - Azure 보안 기능으로 보호 | 데이터 - BitLocker로 암호화                                 |
| 임시 준비 위치 프로비저닝                     | 필요하지 않음                                                | 예상 백업 데이터 크기보다 크거나 같음        |
| 지원되는 지역                                           | [Azure Data Box 디스크 지역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) <br> [Azure Data Box 지역](https://docs.microsoft.com/azure/databox/data-box-disk-overview#region-availability) | [Azure Import/Export 서비스 지역](https://docs.microsoft.com/azure/storage/common/storage-import-export-service#region-availability) |
| 국가 간 배송                                     | 지원되지 않음  <br>    원본 주소와 대상 Azure 데이터 센터는 동일한 국가/지역에 있어야 함 * | 지원됨                                                    |
| 전송 물류(배달, 전송, 픽업)           | Microsoft 완전 관리                                     | 고객 관리                                            |
| 가격 책정                                                      | [Azure Data Box 가격 책정](https://azure.microsoft.com/pricing/details/databox/) <br> [Azure Data Box 디스크 가격 책정](https://azure.microsoft.com/pricing/details/databox/disk/) | [Azure Import/Export 서비스 가격 책정](https://azure.microsoft.com/pricing/details/storage-import-export/) |

\* 해당 국가/지역에 Azure 데이터 센터가 없는 경우 다른 국가/지역에 있는 Azure 데이터 센터에 디스크를 보내야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Box를 사용한 Azure Backup 오프라인 백업](offline-backup-azure-data-box.md#backup-data-size-and-supported-data-box-skus)
- [Azure Backup의 오프라인 백업 워크플로](backup-azure-backup-import-export.md)
- [DPM 및 Azure Backup Server에 대한 오프라인 백업 워크플로](backup-azure-backup-server-import-export.md)
