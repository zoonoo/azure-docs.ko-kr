---
title: Azure NFS 파일 공유 문제 해결 - Azure Files
description: Azure NFS 파일 공유 문제를 해결합니다.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 09/15/2020
ms.author: jeffpatt
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 6ba070f0ed04885b79a56284f08a2467f6fcf51b
ms.sourcegitcommit: f6b76df4c22f1c605682418f3f2385131512508d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108324080"
---
# <a name="troubleshoot-azure-nfs-file-shares"></a>Azure NFS 파일 공유 문제 해결

이 문서에서는 Azure NFS 파일 공유와 관련된 몇 가지 일반적인 문제를 나열합니다. 이러한 문제가 발생하는 경우 잠재적 원인과 해결 방법을 제공합니다.

## <a name="chgrp-filename-failed-invalid-argument-22"></a>chgrp "filename" 실패: 잘못된 인수(22)

### <a name="cause-1-idmapping-is-not-disabled"></a>원인 1: idmapping이 사용하지 않도록 설정되어 있지 않습니다.
Azure Files는 영숫자 UID/GID를 허용하지 않습니다. 따라서 idmapping을 사용하지 않도록 설정해야 합니다. 

### <a name="cause-2-idmapping-was-disabled-but-got-re-enabled-after-encountering-bad-filedir-name"></a>원인 2: idmapping이 사용하지 않도록 설정되었지만 잘못된 파일/디렉터리 이름을 발견한 후 다시 사용하도록 설정되었습니다.
idmapping을 올바르게 사용하지 않도록 설정했더라도 idmapping을 사용하지 않는 설정이 일부 경우에 재정의됩니다. 예를 들어 Azure Files가 잘못된 파일 이름을 발견하면 오류를 보냅니다. 이 특정 오류 코드가 표시되면 NFS v 4.1 Linux 클라이언트는 idmapping을 다시 사용하도록 설정하고 이후 요청은 영숫자 UID/GID를 사용하여 다시 보냅니다. Azure Files에서 지원되지 않는 문자 목록은 이 [문서](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)를 참조하세요. 콜론은 지원되지 않는 문자 중 하나입니다. 

### <a name="workaround"></a>해결 방법
Idmapping이 사용하지 않도록 설정되어 있으며 다시 사용하도록 설정하는 것이 없는지 확인한 후 다음을 수행합니다.

- 공유를 분리합니다.
- # echo Y > /sys/module/nfs/parameters/nfs4_disable_idmapping을 통해 ID 매핑을 사용하지 않도록 설정합니다.
- 공유를 다시 탑재합니다.
- rsync를 실행하는 경우 디렉터리/파일 이름이 잘못되지 않은 디렉터리에서 rsync를 “—numeric-ids” 인수와 함께 실행합니다.

## <a name="unable-to-create-an-nfs-share"></a>NFS 공유를 만들 수 없음

### <a name="cause-1-subscription-is-not-enabled"></a>원인 1: 구독이 사용하도록 설정되어 있지 않습니다.

구독이 Azure Files NFS 미리 보기용으로 등록되지 않았을 수 있습니다. 이 기능을 사용하도록 설정하려면 Cloud Shell 또는 로컬 터미널에서 몇 가지 추가 commandlet을 실행해야 합니다.

> [!NOTE]
> 등록이 완료될 때까지 최대 30분 정도 기다려야 할 수 있습니다.


#### <a name="solution"></a>해결 방법

다음 스크립트를 사용하여 기능 및 리소스 공급자를 등록하고 스크립트를 실행하기 전에 `<yourSubscriptionIDHere>`를 바꿉니다.

```azurepowershell
Connect-AzAccount

#If your identity is associated with more than one subscription, set an active subscription
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage

Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="cause-2-unsupported-storage-account-settings"></a>원인 2: 스토리지 계정 설정이 지원되지 않습니다.

NFS는 다음 구성을 사용하는 스토리지 계정에서만 사용할 수 있습니다.

- 계층 - 프리미엄
- 계정 종류 - FileStorage
- 지역 - [지원되는 지역 목록](./storage-files-how-to-create-nfs-shares.md?tabs=azure-portal#regional-availability)

#### <a name="solution"></a>해결 방법

[NFS 공유를 만드는 방법](storage-files-how-to-create-nfs-shares.md) 문서의 지침을 따르세요.

### <a name="cause-3-the-storage-account-was-created-prior-to-registration-completing"></a>원인 3: 등록을 완료하기 전에 스토리지 계정을 만들었습니다.

스토리지 계정이 기능을 사용할 수 있으려면 구독에서 NFS 등록을 완료한 후에 스토리지 계정을 만들어야 합니다. 등록을 완료하는 데 최대 30분이 소요될 수 있습니다.

#### <a name="solution"></a>해결 방법

등록이 완료되면 [NFS 공유를 만드는 방법](storage-files-how-to-create-nfs-shares.md) 문서의 지침을 따르세요.

## <a name="cannot-connect-to-or-mount-an-azure-nfs-file-share"></a>Azure NFS 파일 공유에 연결하거나 탑재할 수 없음

### <a name="cause-1-request-originates-from-a-client-in-an-untrusted-networkuntrusted-ip"></a>원인 1: 신뢰할 수 없는 네트워크/신뢰할 수 없는 IP의 클라이언트에서 요청이 시작되었습니다.

SMB와 달리 NFS는 사용자 기반 인증을 사용하지 않습니다. 공유에 대한 인증은 네트워크 보안 규칙 구성을 기반으로 합니다. 이로 인해 NFS 공유에 대한 보안 연결만 설정되도록 하려면 서비스 엔드포인트나 프라이빗 엔드포인트를 사용해야 합니다. 프라이빗 엔드포인트 외에도 온-프레미스에서 공유에 액세스하려면 VPN 또는 ExpressRoute를 설정해야 합니다. 방화벽에 대한 스토리지 계정의 허용 목록에 추가된 IP는 무시됩니다. NFS 공유에 대한 액세스를 설정하려면 다음 방법 중 하나를 사용해야 합니다.


- [서비스 엔드포인트](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - 공용 엔드포인트에서 액세스됨
    - 동일한 지역에서만 사용할 수 있습니다.
    - VNet 피어링은 공유에 대한 액세스 권한을 부여하지 않습니다.
    - 각 가상 네트워크 또는 서브넷은 허용 목록에 개별적으로 추가해야 합니다.
    - 온-프레미스 액세스의 경우 서비스 엔드포인트를 ExpressRoute, 지점과 사이트 간 VPN 및 사이트 간 VPN과 함께 사용할 수 있지만 더 안전한 프라이빗 엔드포인트를 사용하는 것이 좋습니다.

다음 다이어그램에서는 공용 엔드포인트를 사용하여 연결을 보여 줍니다.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg" alt-text="공용 엔드포인트 연결 다이어그램." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-public-endpoints.jpg":::

- [프라이빗 엔드포인트](storage-files-networking-endpoints.md#create-a-private-endpoint)
    - 액세스는 서비스 엔드포인트보다 더 안전합니다.
    - 프라이빗 링크를 통한 NFS 공유에 대한 액세스는 스토리지 계정의 Azure 지역 내부 및 외부(지역 간, 온-프레미스)에서 사용할 수 있습니다.
    - 프라이빗 엔드포인트에서 호스트되는 가상 네트워크를 사용하는 가상 네트워크 피어링은 피어링된 가상 네트워크의 클라이언트에 대한 NFS 공유 액세스를 제공합니다.
    - 프라이빗 엔드포인트는 ExpressRoute, 지점과 사이트 간 VPN 및 사이트 간 VPN과 함께 사용할 수 있습니다.

:::image type="content" source="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg" alt-text="프라이빗 엔드포인트 연결 다이어그램." lightbox="media/storage-troubleshooting-files-nfs/connectivity-using-private-endpoints.jpg":::

### <a name="cause-2-secure-transfer-required-is-enabled"></a>원인 2: 보안 전송 필요가 사용하도록 설정되어 있습니다.

NFS 공유에 대한 이중 암호화는 아직 지원되지 않습니다. Azure는 MACSec를 사용하여 Azure 데이터 센터 간에 전송 중인 모든 데이터에 대한 암호화를 제공합니다. NFS 공유는 신뢰할 수 있는 가상 네트워크와 VPN 터널을 통해서만 액세스할 수 있습니다. NFS 공유에서 추가 전송 계층 암호화를 사용할 수 없습니다.

#### <a name="solution"></a>해결 방법

스토리지 계정의 구성 블레이드에서 필요한 보안 전송을 사용하지 않도록 설정합니다.

:::image type="content" source="media/storage-files-how-to-mount-nfs-shares/storage-account-disable-secure-transfer.png" alt-text="보안 전송을 사용하지 않도록 설정해야 하는 스토리지 계정 구성 블레이드의 스크린샷":::

### <a name="cause-3-nfs-common-package-is-not-installed"></a>원인 3: nfs-common 패키지가 설치되어 있지 않습니다.
탑재 명령을 실행하기 전에 아래에서 배포별 명령을 실행하여 패키지를 설치합니다.

NFS 패키지가 설치되었는지 확인하려면 다음을 실행합니다. `rpm qa | grep nfs-utils`

#### <a name="solution"></a>해결 방법

패키지가 설치되어 있지 않으면 배포에 패키지를 설치합니다.

##### <a name="ubuntu-or-debian"></a>Ubuntu 또는 Debian

```
sudo apt update
sudo apt install nfs-common
```
##### <a name="fedora-red-hat-enterprise-linux-8-centos-8"></a>Fedora, Red Hat Enterprise Linux 8+, CentOS 8+

dnf 패키지 관리자(`sudo dnf install nfs-utils`)를 사용합니다.

이전 버전의 Red Hat Enterprise Linux 및 CentOS는 yum 패키지 관리자(`sudo yum install nfs-common`)를 사용합니다.

##### <a name="opensuse"></a>openSUSE

zypper 패키지 관리자(`sudo zypper install-nfscommon`)를 사용합니다.

### <a name="cause-4-firewall-blocking-port-2049"></a>원인 4: 방화벽 차단 포트 2049

NFS 프로토콜은 포트 2049을 통해 서버와 통신하고, 이 포트가 스토리지 계정(NFS 서버)에 열려 있는지 확인합니다.

#### <a name="solution"></a>해결 방법

다음 명령을 실행하여 클라이언트에서 포트 2049가 열려 있는지 확인합니다. `telnet <storageaccountnamehere>.file.core.windows.net 2049`. 포트가 열려 있지 않은 경우 엽니다.

## <a name="ls-list-files-shows-incorrectinconsistent-results"></a>ls(파일 나열)에 잘못된 결과 또는 일치하지 않는 결과가 표시됨

### <a name="cause-inconsistency-between-cached-values-and-server-file-metadata-values-when-the-file-handle-is-open"></a>원인: 파일 핸들이 열려 있는 경우 캐시된 값과 서버 파일 메타데이터 값이 일치하지 않습니다.
때때로 "파일 나열" 명령은 예상대로 0이 아닌 크기를 표시하고 그 대신 바로 다음의 파일 나열 명령에서는 크기가 0이거나 아주 오래된 타임스탬프를 표시합니다. 이는 파일이 열려 있는 동안 파일 메타데이터 값의 캐싱이 일관적이지 않아 발생하는 알려진 문제입니다. 다음 해결 방법 중 하나를 사용하여 이 문제를 해결할 수 있습니다.

#### <a name="workaround-1-for-fetching-file-size-use-wc--c-instead-of-ls--l"></a>해결 방법 1: 파일 크기 페치의 경우 ls -l 대신 wc -c를 사용합니다.
wc -c를 사용하면 항상 서버에서 최신 값이 페치되며 불일치가 발생하지 않습니다.

#### <a name="workaround-2-use-noac-mount-flag"></a>해결 방법 2: "noac" 탑재 플래그 사용
탑재 명령과 함께 "noac" 플래그를 사용하여 파일 시스템을 다시 탑재합니다. 그러면 서버에서 항상 모든 메타데이터 값이 페치됩니다. 이 해결 방법을 사용하는 경우 모든 메타데이터 작업에 대해 약간의 성능 오버헤드가 발생할 수 있습니다.


## <a name="unable-to-mount-an-nfs-share-that-is-restored-back-from-soft-deleted-state"></a>일시 삭제된 상태에서 다시 복원된 NFS 공유를 탑재할 수 없음
플랫폼이 완전히 지원하지 않더라도 NFS 공유에서 일시 삭제되는 미리 보기 중에는 알려진 문제가 있습니다. 이러한 공유는 만료될 때 정기적으로 삭제됩니다. "공유 삭제 취소 + 일시 삭제 사용 안 함 + 공유 삭제" 흐름을 통해 조기에 삭제할 수도 있습니다. 그러나 공유를 복구하고 사용하려고 하면 클라이언트에서 액세스 거부 또는 사용 권한 거부 또는 NFS I/O 오류가 발생합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원에 문의
도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
