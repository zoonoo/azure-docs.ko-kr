---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: cbc9c1433b1be6337c9e967b1f8b7103be899566
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135671"
---
| 차원 이름 | Description |
| ------------------- | ----------------- |
| **GeoType** | 기본 또는 보조 클러스터에서 전송되는 트랜잭션입니다. 사용 가능한 값에는 **기본** 과 **보조** 가 있습니다. 이는 보조 테넌트에서 개체를 읽을 때 RA-GRS(Read Access Geo Redundant Storage)에 적용됩니다. |
| **ResponseType** | 트랜잭션 응답 형식입니다. 사용 가능한 값은 다음을 포함합니다. <br/><br/> <li>**ServerOtherError** : 설명한 것을 제외한 다른 모든 서버 쪽 오류입니다. </li> <li>**ServerBusyError** : HTTP 503 상태 코드를 반환한 인증된 요청입니다. </li> <li>**ServerTimeoutError** : HTTP 500 상태 코드를 반환한 시간 초과된 인증된 요청입니다. 서버 오류로 인해 시간 제한이 발생하였습니다. </li> <li>**AuthorizationError** : 무단 데이터 액세스 또는 인증 실패로 인해 실패한 인증된 요청입니다. </li> <li>**NetworkError** : 네트워크 오류로 인해 실패한 인증된 요청입니다. 가장 일반적으로 시간 제한이 만료하기 전에 클라이언트가 연결을 너무 일찍 닫은 경우에 발생합니다. </li><li>**ClientAccountBandwidthThrottlingError** : 요청이 [스토리지 계정 확장성 제한](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)을 초과하여 대역폭이 제한되었습니다.</li><li>**ClientAccountRequestThrottlingError** : 요청이 [스토리지 계정 확장성 제한](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json)을 초과하여 요청 속도가 제한되었습니다.<li>**Clientshareerror** : 다른 클라이언트 쪽 제한 오류입니다. ClientAccountBandwidthThrottlingError 및 ClientAccountRequestThrottlingError는 제외됩니다.</li><li>**ClientShareEgressThrottlingError** : 프리미엄 파일 공유에만 적용 됩니다. 다른 클라이언트 쪽 제한 오류입니다. 공유 제한을 초과 하는 송신 대역폭 제한으로 인해 요청이 실패 했습니다. ClientAccountBandwidthThrottlingError은 제외 됩니다.</li><li>**ClientShareIngressThrottlingError** : 프리미엄 파일 공유에만 적용 됩니다. 다른 클라이언트 쪽 제한 오류입니다. 공유 제한을 초과 하기 위한 수신 대역폭 제한으로 인해 요청이 실패 했습니다. ClientAccountBandwidthThrottlingError은 제외 됩니다.</li><li>**ClientShareIopsThrottlingError** : 프리미엄 파일 공유에만 적용 됩니다. 다른 클라이언트 쪽 제한 오류입니다. IOPS 제한으로 인해 요청이 실패 했습니다. ClientAccountRequestThrottlingError은 제외 됩니다.</li><li>**ClientTimeoutError** : HTTP 500 상태 코드를 반환한 시간 초과된 인증된 요청입니다. 클라이언트의 네트워크 시간 제한 또는 요청 시간 제한이 스토리지 서비스에서 예상한 것보다 낮은 값으로 설정된 경우 이는 예상된 시간 제한입니다. 그렇지 않은 경우 이는 ServerTimeoutError로 보고됩니다. </li> <li>**ClientOtherError** : 설명한 것을 제외한 다른 모든 클라이언트 쪽 오류입니다. </li> <li>**성공** : 성공한 요청입니다.</li> <li> **SuccessWithThrottling** : 첫 번째 시도에서 SMB 클라이언트가 제한되었지만 재시도 후에 성공하여 요청이 성공했습니다.</li><li> **SuccessWithShareEgressThrottling** : 프리미엄 파일 공유에만 적용 됩니다. 첫 번째 시도에서 송신 대역폭 제한으로 인해 SMB 클라이언트가 정체 되 고 다시 시도한 후에 성공한 요청이 성공 했습니다.</li><li> **SuccessWithShareIngressThrottling** : 프리미엄 파일 공유에만 적용 됩니다. 첫 번째 시도에서 수신 대역폭 제한으로 인해 SMB 클라이언트가 정체 되 고 다시 시도한 후에 성공한 요청이 성공 했습니다.</li><li> **SuccessWithShareIopsThrottling** : 프리미엄 파일 공유에만 적용 됩니다. 첫 번째 시도에서 IOPS 제한으로 인해 SMB 클라이언트가 정체 되 고 다시 시도한 후에 성공한 요청이 성공 했습니다.</li> |
| **ApiName** | 작업 이름입니다. 
| **인증** | 트랜잭션에 사용되는 인증 형식입니다. 사용 가능한 값은 다음을 포함합니다. <br/> <li>**AccountKey** : 트랜잭션이 스토리지 계정 키를 사용하여 인증됩니다.</li> <li>**SAS** : 트랜잭션이 공유 액세스 서명을 사용하여 인증됩니다.</li> <li>**OAuth** : 트랜잭션이 OAuth 액세스 토큰을 사용하여 인증됩니다.</li> <li>**Anonymous** : 트랜잭션이 익명으로 요청됩니다. 실행 전 요청은 포함되지 않습니다.</li> <li>**AnonymousPreflight** : 트랜잭션이 실행 전 요청입니다.</li> |
