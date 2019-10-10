---
title: 데이터 흐름 Azure Data Factory 문제 해결 | Microsoft Docs
description: Azure Data Factory에서 데이터 흐름 문제를 해결 하는 방법에 대해 알아봅니다.
services: data-factory
author: kromerm
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 10/08/2019
ms.author: makromer
ms.openlocfilehash: b5895b061426066d265d3ff68dc948014e641322
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242280"
---
# <a name="troubleshoot-azure-data-factory-data-flows"></a>데이터 흐름 Azure Data Factory 문제 해결

이 문서에서는 Azure Data Factory의 데이터 흐름에 대 한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="error-message-df-sys-01-shadeddatabricksorgapachehadoopfsazureazureexception-commicrosoftazurestoragestorageexception-the-specified-container-does-not-exist"></a>오류 메시지: DF-SYS-01: databricks: $. m s t. m s t. StorageException: 지정한 컨테이너가 없습니다.

- **증상**: 컨테이너가 없으므로 데이터 미리 보기, 디버그 및 파이프라인 데이터 흐름 실행이 실패 합니다.

- **원인**: 데이터 집합에 저장소에 없는 컨테이너가 포함 된 경우

- **해결 방법**: 데이터 집합에서 참조 하는 컨테이너가 있는지 확인 합니다.

### <a name="error-message-df-sys-01-javalangassertionerror-assertion-failed-conflicting-directory-structures-detected-suspicious-paths"></a>오류 메시지: DF-SYS-01: AssertionError: assertion failed: 충돌 하는 디렉터리 구조가 검색 되었습니다. 의심 스러운 경로

- **증상**: Parquet 파일을 사용 하 여 소스 변환에서 와일드 카드를 사용 하는 경우

- **원인**: 와일드 카드 구문이 잘못 되었거나 잘못 되었습니다.

- **해결 방법**: 원본 변환 옵션에서 사용 중인 와일드 카드 구문을 확인 합니다.

### <a name="error-message-df-src-002-container-container-name-is-required"></a>오류 메시지: 002: ' container ' (Container name)가 필요 합니다.

- **증상**: 컨테이너가 없으므로 데이터 미리 보기, 디버그 및 파이프라인 데이터 흐름 실행이 실패 합니다.

- **원인**: 데이터 집합에 저장소에 없는 컨테이너가 포함 된 경우

- **해결 방법**: 데이터 집합에서 참조 하는 컨테이너가 있는지 확인 합니다.

### <a name="error-message-df-uni-001-primarykeyvalue-has-incompatible-types-integertype-and-stringtype"></a>오류 메시지: DF-001: PrimaryKeyValue에 호환 되지 않는 형식 IntegerType 및 StringType이 있습니다.

- **증상**: 컨테이너가 없으므로 데이터 미리 보기, 디버그 및 파이프라인 데이터 흐름 실행이 실패 합니다.

- **원인**: 데이터베이스 싱크에 잘못 된 기본 키 유형을 삽입 하려고 할 때 발생 합니다.

- **해결 방법**: 파생 열을 사용 하 여 데이터 흐름의 기본 키에 사용 하는 열을 대상 데이터베이스의 데이터 형식과 일치 시킵니다.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-the-tcpip-connection-to-the-host-xxxxxdatabasewindowsnet-port-1433-has-failed-error-xxxxdatabasewindowsnet-verify-the-connection-properties-make-sure-that-an-instance-of-sql-server-is-running-on-the-host-and-accepting-tcpip-connections-at-the-port-make-sure-that-tcp-connections-to-the-port-are-not-blocked-by-a-firewall"></a>오류 메시지: DF-SYS-01: SQLServerException: 호스트 xxxxx.database.windows.net 포트 1433에 대 한 TCP/IP 연결이 실패 했습니다. 오류: "xxxx.database.windows.net. 연결 속성을 확인 합니다. SQL Server 인스턴스가 호스트에서 실행 되 고 포트에서 TCP/IP 연결을 허용 하는지 확인 합니다. 포트에 대 한 TCP 연결이 방화벽에 의해 차단 되지 않는지 확인 하십시오. "

- **증상**: 데이터베이스 원본 또는 싱크를 사용 하 여 데이터를 미리 보거나 파이프라인을 실행할 수 없습니다.

- **원인**: 데이터베이스가 방화벽으로 보호 됩니다.

- **해결 방법**: 데이터베이스에 대 한 방화벽 액세스를 엽니다.

### <a name="error-message-df-sys-01-commicrosoftsqlserverjdbcsqlserverexception-there-is-already-an-object-named-xxxxxx-in-the-database"></a>오류 메시지: DF-SYS-01: SQLServerException: 데이터베이스에 이름이 ' xxxxxx ' 인 개체가 이미 있습니다.

- **증상**: 싱크가 테이블을 만들지 못함

- **원인**: 원본 또는 데이터 집합에 정의 된 동일한 이름의 대상 데이터베이스에 기존 테이블 이름이 이미 있습니다.

- **해결 방법**: 만들려는 테이블의 이름을 변경 합니다.

## <a name="general-troubleshooting-guidance"></a>일반 문제 해결 지침

1. 데이터 집합 연결의 상태를 확인 합니다. 각 원본 및 싱크 변환에서 사용 중인 각 데이터 집합에 대 한 연결 된 서비스를 방문 하 고 연결을 테스트 합니다.
2. 데이터 흐름 디자이너에서 파일 및 테이블 연결의 상태를 확인 합니다. 디버그를 전환 하 고 원본 변환에서 데이터 미리 보기를 클릭 하 여 데이터에 액세스할 수 있는지 확인 합니다.
3. 모든 항목이 데이터 미리 보기에서 양호 하면 파이프라인 디자이너로 이동 하 여 파이프라인 활동에 데이터 흐름을 배치 합니다. 종단 간 테스트를 위한 파이프라인을 디버깅 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결에 대 한 자세한 내용은 다음 리소스를 참조 하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN 포럼](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Data Factory에 대 한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대 한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
*  [ADF 매핑 데이터 흐름 성능 가이드](concepts-data-flow-performance.md)
