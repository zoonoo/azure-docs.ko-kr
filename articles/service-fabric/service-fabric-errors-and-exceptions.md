---
title: 일반적인 FabricClient 예외가 발생 합니다.
description: 애플리케이션 및 클러스터 관리 작업을 수행하는 동안 FabricClient API에 의해 발생될 수 있는 일반적인 예외 및 오류를 설명합니다.
author: georgewallace
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: gwallace
ms.openlocfilehash: 7f3c3e072a3a2e4f7723f84b2c70ba0d0ddb9d03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "86258844"
---
# <a name="common-exceptions-and-errors-when-working-with-the-fabricclient-apis"></a>FabricClient API로 작업하는 경우 일반적인 예외 및 오류
[FabricClient](/dotnet/api/system.fabric.fabricclient) API를 사용하여 클러스터 및 애플리케이션 관리자가 Service Fabric 애플리케이션, 서비스 또는 클러스터에서 관리 작업을 수행할 수 있습니다. 예를 들어 애플리케이션 배포, 업그레이드 및 제거, 클러스터 상태 확인 또는 서비스를 테스트합니다. 애플리케이션 개발자 및 클러스터 관리자는 FabricClient API를 사용하여 Service Fabric 클러스터 및 애플리케이션을 관리하기 위한 도구를 개발할 수 있습니다.

FabricClient를 사용하여 수행할 수 있는 다양한 유형의 작업이 있습니다.  각 메서드는 잘못된 입력으로 인한 오류, 런타임 오류 또는 일시적인 인프라 문제에 대한 예외를 발생시킬 수 있습니다.  특정 메서드에 의해 발생되는 예외를 확인하려면 API 참조 설명서를 참조하세요. 그러나 여러 많은 [FabricClient](/dotnet/api/system.fabric.fabricclient) API에 의해 발생되는 몇몇 예외가 있습니다. 다음 테이블에는 FabricClient API에서 공통적으로 적용되는 예외가 나열되어 있습니다.

| 예외 | 발생 시점 |
| --- |:--- |
| [System.Fabric.FabricObjectClosedException](/dotnet/api/system.fabric.fabricobjectclosedexception) |[FabricClient](/dotnet/api/system.fabric.fabricclient) 개체가 닫힌 상태입니다. 사용 중인 [FabricClient](/dotnet/api/system.fabric.fabricclient) 개체를 삭제하고 새 [FabricClient](/dotnet/api/system.fabric.fabricclient) 개체를 인스턴스화합니다. |
| [System.TimeoutException](/dotnet/core/api/system.timeoutexception) |작업 시간이 초과 되었습니다. 작업을 완료 하는 데 MaxOperationTimeout 이상이 소요 되는 경우 [OperationTimedOut](/dotnet/api/system.fabric.fabricerrorcode) 이 반환 됩니다. |
| [System.UnauthorizedAccessException](/dotnet/core/api/system.unauthorizedaccessexception) |작업에 대한 액세스 검사에 실패했습니다. E_ACCESSDENIED가 반환됩니다. |
| [System.Fabric.FabricException](/dotnet/api/system.fabric.fabricexception) |작업을 수행하는 동안 런타임 오류가 발생했습니다. FabricClient 메서드 중 하나가 잠재적으로 [FabricException](/dotnet/api/system.fabric.fabricexception)을 발생시킬 수 있으며 [ErrorCode](/dotnet/api/system.fabric.fabricexception.errorcode) 속성이 예외의 정확한 원인을 나타냅니다. [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) 열거형에 정의된 오류 코드입니다. |
| [System.Fabric.FabricTransientException](/dotnet/api/system.fabric.fabrictransientexception) |일종의 일시적인 오류 조건으로 인해 작업이 실패했습니다. 예를 들어 복제본의 쿼럼에 일시적으로 연결할 수 없기 때문에 작업이 실패할 수 있습니다. 일시적인 예외는 다시 시도할 수 있는 실패한 작업에 해당합니다. |

일부 일반 [FabricErrorCode](/dotnet/api/system.fabric.fabricerrorcode) 오류는 [FabricException](/dotnet/api/system.fabric.fabricexception)으로 반환될 수 있습니다.

| Error | 조건 |
| --- |:--- |
| CommunicationError |통신 오류로 인해 작업이 실패했습니다. 작업을 다시 시도하세요. |
| InvalidCredentialType |자격 증명 유형이 올바르지 않습니다. |
| InvalidX509FindType |X509FindType이 올바르지 않습니다. |
| InvalidX509StoreLocation |X509 저장소 위치가 올바르지 않습니다. |
| InvalidX509StoreName |X509 저장소 이름이 올바르지 않습니다. |
| InvalidX509Thumbprint |X509 인증서 지문 문자열이 올바르지 않습니다. |
| InvalidProtectionLevel |보호 수준이 올바르지 않습니다. |
| InvalidX509Store |X509 인증서 저장소를 열 수 없습니다. |
| InvalidSubjectName |주체 이름이 올바르지 않습니다. |
| InvalidAllowedCommonNameList |일반 이름 목록 문자열의 형식이 올바르지 않습니다. 쉼표로 구분된 목록이어야 합니다. |
