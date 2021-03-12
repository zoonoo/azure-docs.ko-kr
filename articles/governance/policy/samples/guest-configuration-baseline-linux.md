---
title: Linux에 대 한 참조 Azure Policy 게스트 구성 기준
description: Azure Policy 게스트 구성을 통해 구현 되는 Azure의 Linux 기준에 대 한 세부 정보입니다.
ms.date: 02/26/2021
ms.topic: reference
ms.custom: generated
ms.openlocfilehash: 20f589b02f2a43bb3feb954dc4eac2dd6a9c3925
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2021
ms.locfileid: "103235030"
---
# <a name="azure-policy-guest-configuration-baseline-for-linux"></a>Linux에 대 한 Azure Policy 게스트 구성 기준

다음 문서에서는 **\[ Preview \] Linux 컴퓨터가 Azure 보안 기준 게스트 구성 정책 이니셔티브 감사에 대 한 요구 사항을 충족 해야** 하는 항목을 자세히 설명 합니다. 자세한 내용은 [Azure Policy 게스트 구성](../concepts/guest-configuration.md) 및 [Azure 보안 벤치 마크 (V2) 개요](../../../security/benchmarks/overview.md)를 참조 하세요.

## <a name="general-security-controls"></a>일반 보안 컨트롤

|이름<br /><sub>A-ID</sub> |세부 정보 |재구성 확인 |
|---|---|---|
|/Home partition에서 nodev 옵션을 설정 했는지 확인 합니다.<br /><sub>1.1.4</sub> |설명: 공격자가/home 파티션에 특수 장치 (예: 차단 또는 문자 장치)를 탑재할 수 있습니다. |/Etc/fstab 파일을 편집 하 고/home 파티션의 네 번째 필드 (탑재 옵션)를 편집 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|/Tmp partition에서 nodev 옵션을 설정 했는지 확인 합니다.<br /><sub>1.1.5</sub> |설명: 공격자가/tmp 파티션에 특수 장치 (예: 차단 또는 문자 장치)를 탑재할 수 있습니다. |/Etc/fstab 파일을 편집 하 고/tmp 파티션의 네 번째 필드 (탑재 옵션)를 편집 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|/Var/tmp partition에서 nodev 옵션을 설정 했는지 확인 합니다.<br /><sub>1.1.6</sub> |설명: 공격자가/var/tmp 파티션에 특수 장치 (예: 차단 또는 문자 장치)를 탑재할 수 있습니다. |/Etc/fstab 파일을 편집 하 고/var/tmp 파티션의 네 번째 필드 (탑재 옵션)를 편집 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|/Tmp 파티션에 nosuid 옵션이 설정 되어 있는지 확인 하세요.<br /><sub>1.1.7</sub> |설명:/tmp 파일 시스템은 임시 파일 저장소에만 사용 되므로 사용자가/var/tmp.에서 setuid 파일을 만들 수 없도록 하려면이 옵션을 설정 합니다. |/Etc/fstab 파일을 편집 하 고/tmp 파티션에 대 한 네 번째 필드 (탑재 옵션)를 nosuid 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|/Var/tmp 파티션에 nosuid 옵션이 설정 되어 있는지 확인 하세요.<br /><sub>1.1.8</sub> |설명:/var/tmp 파일 시스템은 임시 파일 저장소에만 사용 되므로 사용자가/var/tmp.에서 setuid 파일을 만들 수 없도록 하려면이 옵션을 설정 합니다. |/Etc/fstab 파일을 편집 하 고/var/tmp 파티션에 대 한 네 번째 필드 (탑재 옵션)를 nosuid 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|/Var/tmp 파티션에 noexec 옵션이 설정 되어 있는지 확인 하세요.<br /><sub>1.1.9</sub> |설명: 파일 `/var/tmp` 시스템은 임시 파일 저장소에만 사용 되므로 사용자가에서 실행 파일을 실행할 수 없도록 하려면이 옵션을 설정 `/var/tmp` 합니다. |/Etc/fstab 파일을 편집 하 고/var/tmp 파티션의 네 번째 필드 (탑재 옵션)에 noexec를 추가 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|/Dev/svm 파티션에 noexec 옵션이 설정 되어 있는지 확인 합니다.<br /><sub>(1.1.16)</sub> |설명: 파일 시스템에 대해이 옵션을 설정 하면 사용자가 공유 메모리에서 프로그램을 실행할 수 없습니다. 이렇게 하면 사용자가 시스템에 잠재적으로 악성 소프트웨어를 deters 수 있습니다. |/Etc/fstab 파일을 편집 하 고/sv/svm 파티션의 네 번째 필드 (탑재 옵션)에 noexec를 추가 합니다. 자세한 내용은 fstab (5) 수동 페이지를 참조 하세요. |
|Automounting 사용 안 함<br /><sub>(1.1.21)</sub> |설명: automounting를 사용 하는 경우 물리적 액세스 권한이 있는 모든 사용자가 USB 드라이브 또는 디스크를 연결 하 고 해당 콘텐츠를 탑재할 권한이 부족 하더라도 시스템에서 해당 콘텐츠를 사용할 수 있습니다. |Autofs 서비스를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r disable-autofs '를 실행 합니다. |
|USB 저장 장치의 탑재를 사용 하지 않도록 설정<br /><sub>(1.1.21.1)</sub> |설명: USB 저장 장치에 대 한 지원을 제거 하면 서버의 로컬 공격 노출 영역을 줄일 수 있습니다. |디렉터리에서 파일을 편집 하거나 만든 `/etc/modprobe.d/` 다음, 해당 파일을 추가 하 고 `install usb-storage /bin/true` usb 저장소 모듈을 언로드하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r mods '를 실행 합니다. |
|코어 덤프가 제한 되어 있는지 확인 하세요.<br /><sub>1.5.1</sub> |설명: 코어 덤프에 대 한 하드 한도를 설정 하면 사용자가 소프트 변수를 재정의 하지 않습니다. 코어 덤프가 필요한 경우 사용자 그룹에 대 한 제한을 설정 하는 것이 좋습니다 (참조 `limits.conf(5)` ). 또한 `fs.suid_dumpable` 변수를 0으로 설정 하면 setuid 프로그램에서 core를 덤프할 수 없습니다. |`hard core 0`/Etc/security/limits.conf 또는 sysctl 디렉터리에 있는 파일에를 추가 하 고 `fs.suid_dumpable = 0` '/opt/microsoft/omsagent/plugin/omsremediate-r e n t e r s e r s e '를 실행 합니다. |
|Prelink를 사용 하지 않도록 설정 했는지 확인 합니다.<br /><sub>(1.5.4)</sub> |설명: 사전 연결 기능은 이진 파일을 변경 하기 때문에 AIDE의 작업을 방해할 수 있습니다. 또한 사전 연결은 악의적인 사용자가 libc와 같은 공통 라이브러리를 손상 시킬 수 있는 경우 시스템의 취약성을 높일 수 있습니다. |`prelink`패키지 관리자를 사용 하 여 제거 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r prelink '를 실행 합니다. |
|/Etc/motd에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(1.7.1.4)</sub> |설명: 파일에 `/etc/motd` 올바른 소유권이 없으면 권한이 없거나 잘못 된 정보를 사용 하 여 권한이 없는 사용자가 수정할 수 있습니다. |/Etc/motd의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0644로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r 파일-사용 권한 '을 실행 합니다. |
|/Etc/issue에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(1.7.1.5)</sub> |설명: 파일에 `/etc/issue` 올바른 소유권이 없으면 권한이 없거나 잘못 된 정보를 사용 하 여 권한이 없는 사용자가 수정할 수 있습니다. |/Etc/issue의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0644로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r 파일-사용 권한 '을 실행 합니다. |
|/Etc/issue.net에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(1.7.1.6)</sub> |설명: 파일에 `/etc/issue.net` 올바른 소유권이 없으면 권한이 없거나 잘못 된 정보를 사용 하 여 권한이 없는 사용자가 수정할 수 있습니다. |/Etc/issue.net의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0644로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r 파일-사용 권한 '을 실행 합니다. |
|모든 이동식 미디어에 대해 nodev 옵션을 사용 하도록 설정 해야 합니다.<br /><sub>(2.1)</sub> |설명: 공격자가 이동식 미디어를 통해 특수 장치 (예: 차단 또는 문자 장치)를 탑재할 수 있습니다. |/Etc/fstab의 네 번째 필드 (탑재 옵션)에 nodev 옵션 추가 |
|모든 이동식 미디어에 대해 noexec 옵션을 사용 하도록 설정 해야 합니다.<br /><sub>(2.2)</sub> |설명: 공격자가 이동식 미디어를 통해 실행 파일을 로드할 수 있습니다. |/Etc/fstab의 네 번째 필드 (탑재 옵션)에 noexec 옵션을 추가 합니다. |
|모든 이동식 미디어에 대해 nosuid 옵션을 사용 하도록 설정 해야 합니다.<br /><sub>(2.3)</sub> |설명: 공격자가 이동식 미디어를 통해 관리자 권한으로 실행 되는 파일을 로드할 수 있습니다. |/Etc/fstab의 네 번째 필드 (탑재 옵션)에 nosuid 옵션을 추가 합니다. |
|통신 클라이언트가 설치 되어 있지 않은지 확인 합니다.<br /><sub>2.3.3</sub> |설명: 통신에 암호화 되지 않은 프로토콜을 사용 하므로 소프트웨어에서 보안 위험을 표시 합니다. |`talk`'/Opt/microsoft/omsagent/plugin/omsremediate-r '을 제거 하거나 실행 합니다. |
|/Etc/hosts.allow에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>3.4.4</sub> |설명: `/etc/hosts.allow` 파일이 무단 쓰기 액세스 로부터 보호 되는지 확인 하는 것이 중요 합니다. 기본적으로 보호 되지만 파일 사용 권한은 실수로 또는 악의적인 작업을 통해 변경할 수 있습니다. |/Etc/hosts.allow의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0644로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r 파일-사용 권한 '을 실행 합니다. |
|/Etc/hosts.deny에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(3.4.5)</sub> |설명: `/etc/hosts.deny` 파일이 무단 쓰기 액세스 로부터 보호 되는지 확인 하는 것이 중요 합니다. 기본적으로 보호 되지만 파일 사용 권한은 실수로 또는 악의적인 작업을 통해 변경할 수 있습니다. |/Etc/hosts.deny의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0644로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r 파일-사용 권한 '을 실행 합니다. |
|기본 방화벽 정책 거부 확인<br /><sub>3.6.2</sub> |설명: 기본 수락 정책을 사용 하면 거부 하도록 구성 되지 않은 모든 패킷이 방화벽에 적용 됩니다. 기본 허용 정책을 사용 하는 것 보다 기본 삭제 정책으로 보안 방화벽을 유지 관리 하는 것이 더 쉽습니다. |`deny` `reject` 방화벽 소프트웨어를 사용 하 여 들어오고 나가는 트래픽에 대 한 기본 정책을 또는 적절 하 게 설정 합니다. |
|모든 NFS 탑재에 대해 nodev/nosuid 옵션을 사용 하도록 설정 해야 합니다.<br /><sub>(5)</sub> |설명: 공격자가 원격 파일 시스템을 통해 관리자 권한 보안 컨텍스트 또는 특수 장치를 사용 하 여 실행 되는 파일을 로드할 수 있습니다. |/Etc/fstab의 네 번째 필드 (탑재 옵션)에 nosuid 및 nodev 옵션 추가 |
|암호 만들기 요구 사항이 구성 되어 있는지 확인 합니다.<br /><sub>5.3.1</sub> |설명: 강력한 암호를 통해 무차별 암호 대입 방법을 통해 시스템이 해킹 되지 않도록 보호 합니다. |배포판에 대 한 적절 한 PAM에서 다음 키/값 쌍을 설정 합니다. minlen = 14, minlen = 4, lcredit =-1, ucredit =-1, ocredit =-1, lcredit =-1 또는 run '/opt/microsoft/omsagent/plugin/omsremediate |
|실패 한 암호 시도의 잠금이 구성 되어 있는지 확인 합니다.<br /><sub>5.3.2</sub> |설명: 연속 로그인 실패 후 사용자 Id를 잠그면 `n` 시스템에 대 한 무차별 암호 대입 공격을 완화 합니다. |Ubuntu 및 Debian의 경우 pam_tally 및 pam_deny 모듈을 적절 하 게 추가 합니다. 다른 모든 배포판의 경우 배포판 설명서를 참조 하세요. |
|필요 하지 않은 파일 시스템의 설치 및 사용을 사용 하지 않도록 설정 (cramfs)<br /><sub>(6.1)</sub> |설명: 공격자가 cramfs의 취약성을 사용 하 여 권한을 상승 시킬 수 있습니다. |Cramfs를 사용 하지 않도록 설정 하는/etc/modprob.d 디렉터리에 파일을 추가 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r-mods '를 실행 합니다. |
|필요 하지 않은 파일 시스템의 설치 및 사용을 사용 하지 않도록 설정 (freevxfs)<br /><sub>(6.2)</sub> |설명: 공격자가 freevxfs의 취약성을 사용 하 여 권한을 상승 시킬 수 있습니다. |Freevxfs를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r/etc/modprob.d '를 실행 하는 파일을 디렉터리에 추가 합니다. |
|모든 사용자의 홈 디렉터리가 있는지 확인<br /><sub>(6.2.7)</sub> |설명: 사용자의 홈 디렉터리가 없거나 할당 되지 않은 경우 사용자가 '/'에 배치 되 고 파일을 작성 하거나 로컬 환경 변수를 설정할 수 없습니다. |사용자의 홈 디렉터리가 없는 경우 해당 디렉터리를 만들고 해당 사용자가 디렉터리를 소유 하 고 있는지 확인 합니다. 할당 된 홈 디렉터리가 없는 사용자를 제거 하거나 적절 하 게 홈 디렉터리를 할당 해야 합니다. |
|사용자가 자신의 홈 디렉터리를 소유 하 고 있는지 확인<br /><sub>(6.2.9)</sub> |설명: 사용자가 사용자 홈 디렉터리에 저장 된 파일에 대 한 책임이 있으므로 사용자는 디렉터리의 소유자 여야 합니다. |정의 된 사용자가 소유 하지 않은 홈 디렉터리의 소유권을 올바른 사용자로 변경 합니다. |
|사용자의 점 파일이 그룹이 나 전 세계 쓰기가 가능 하지 않은지 확인 합니다.<br /><sub>(6.2.10)</sub> |설명: 그룹 또는 세계에서 쓰기 가능한 사용자 구성 파일을 사용 하 여 악의적인 사용자가 다른 사용자의 데이터를 도용 또는 수정 하거나 다른 사용자의 시스템 권한을 얻을 수 있습니다. |사용자 커뮤니티에 경고 하지 않고 사용자 파일에 대 한 전역 수정을 수행 하면 예기치 않은 중단 및 사용자에 게 불만이 발생할 수 있습니다. 따라서 사용자 도트 파일 사용 권한을 보고 하 고 사이트 정책에 따라 수행할 작업을 결정 하는 모니터링 정책을 설정 하는 것이 좋습니다. |
|사용자에 게 전달 파일이 있는지 확인<br /><sub>(6.2.11)</sub> |설명: 파일을 사용 하면 `.forward` 중요 한 데이터가 조직 외부로 실수로 전송 될 수 있으므로 보안상 위험할 수 있습니다. `.forward`또한 의도 하지 않은 작업을 수행할 수 있는 명령을 실행 하는 데 사용할 수 있으므로이 파일은 위험을 초래 합니다. |사용자 커뮤니티에 경고 하지 않고 사용자 파일에 대 한 전역 수정을 수행 하면 예기치 않은 중단 및 사용자에 게 불만이 발생할 수 있습니다. 따라서 보고서 사용자 파일에 대 한 모니터링 정책을 설정 하 `.forward` 고 사이트 정책에 따라 수행할 작업을 결정 하는 것이 좋습니다. |
|사용자에 게 netrc 파일이 있는지 확인<br /><sub>(6.2.12)</sub> |설명: `.netrc` 암호화 되지 않은 형식으로 암호를 저장 하기 때문에이 파일은 심각한 보안 위험을 나타냅니다. FTP를 사용 하지 않도록 설정한 경우에도 사용자 계정이 `.netrc` 해당 시스템에 대 한 위험을 초래할 수 있는 다른 시스템의 파일을 가져올 수 있습니다. |사용자 커뮤니티에 경고 하지 않고 사용자 파일에 대 한 전역 수정을 수행 하면 예기치 않은 중단 및 사용자에 게 불만이 발생할 수 있습니다. 따라서 보고서 사용자 파일에 대 한 모니터링 정책을 설정 하 `.netrc` 고 사이트 정책에 따라 수행할 작업을 결정 하는 것이 좋습니다. |
|사용자에 게 rhosts 파일이 있는지 확인 합니다.<br /><sub>(6.2.14)</sub> |설명:이 작업은 `.rhosts` 파일에서 지원이 허용 되는 경우에만 의미가 있습니다 `/etc/pam.conf` . `.rhosts`에서 지원이 사용 하지 않도록 설정 된 경우 파일이 비효율적 이더라도 `/etc/pam.conf` 다른 시스템에서 가져온 것 이며 공격자가 다른 시스템에 유용한 정보를 포함할 수 있습니다. |사용자 커뮤니티에 경고 하지 않고 사용자 파일에 대 한 전역 수정을 수행 하면 예기치 않은 중단 및 사용자에 게 불만이 발생할 수 있습니다. 따라서 보고서 사용자 파일에 대 한 모니터링 정책을 설정 하 `.rhosts` 고 사이트 정책에 따라 수행할 작업을 결정 하는 것이 좋습니다. |
|/Etc/passwd의 모든 그룹이/etc/group에 존재 하는지 확인 합니다.<br /><sub>(6.2.15)</sub> |설명:/etc/passwd 파일에 정의 되어 있지만/etc/group 파일에는 정의 되지 않은 그룹의 경우 그룹 권한이 제대로 관리 되지 않기 때문에 시스템 보안 위협에 노출 됩니다. |/Etc/passwd에 정의 된 각 그룹에 대해/etc/group에 해당 그룹이 있는지 확인 합니다. |
|중복 된 Uid가 없는지 확인<br /><sub>(6.2.16)</sub> |설명: 사용자에 게 책임을 위한 고유한 Uid를 할당 하 고 적절 한 액세스 보호를 보장 해야 합니다. |고유한 Uid를 설정 하 고 공유 Uid가 소유한 모든 파일을 검토 하 여 속할 UID를 결정 합니다. |
|중복 된 Gid가 없는지 확인<br /><sub>(6.2.17)</sub> |설명: 그룹에는 책임이 있는 고유한 Gid를 할당 하 고 적절 한 액세스 보호를 보장 해야 합니다. |고유 gid를 설정 하 고 공유 Gid가 소유한 모든 파일을 검토 하 여 속할 GID를 결정 합니다. |
|중복 된 사용자 이름이 존재 하지 않는지 확인<br /><sub>(6.2.18)</sub> |설명: 사용자에 게 중복 사용자 이름이 할당 된 경우에서 해당 사용자 이름에 대 한 첫 번째 UID를 가진 파일에 대 한 액세스 권한이 생성 됩니다 `/etc/passwd` . 예를 들어, ' test4 '의 UID가 1000이 고 후속 ' test4 ' 항목의 UID가 2000 인 경우 ' test4 '로 로그인 하면 UID 1000를 사용 합니다. 사실상 UID가 공유 되며 보안 문제입니다. |사용자의 고유한 사용자 이름을 설정 합니다. 사용자에 게 고유한 Uid가 있는 한 소유권이 파일에 변경 내용이 자동으로 반영 됩니다. |
|중복 그룹이 존재 하지 않는지 확인<br /><sub>(6.2.19)</sub> |설명: 그룹에 중복 그룹 이름이 할당 된 경우에서 해당 그룹의 첫 번째 GID를 사용 하 여 파일에 액세스할 수 `/etc/group` 있습니다. 효과적으로 GID가 공유 됩니다 .이는 보안 문제입니다. |사용자 그룹에 대 한 고유한 이름을 설정 합니다. 그룹에 고유한 Gid가 있는 경우 파일 그룹 소유권이에 변경 내용이 자동으로 반영 됩니다. |
|섀도 그룹이 비어 있는지 확인<br /><sub>(6.2.20)</sub> |설명: 섀도 그룹에 할당 된 모든 사용자에 게는/etc/shadow 파일에 대 한 읽기 권한이 부여 됩니다. 공격자는 파일에 대 한 읽기 권한을 얻을 수 있는 경우 `/etc/shadow` 해시 된 암호에 대해 암호 해독 프로그램을 쉽게 실행 하 여 암호를 나눌 수 있습니다. 파일에 저장 되는 기타 보안 정보 `/etc/shadow` (예: 만료)는 추가 사용자 계정을 방해할 하는 데에도 유용할 수 있습니다. |섀도 그룹의 모든 사용자를 제거 합니다. |
|필요 하지 않은 파일 시스템의 설치 및 사용을 사용 하지 않도록 설정 (hfs)<br /><sub>(6.3)</sub> |설명: 공격자가 hfs의 취약성을 사용 하 여 권한을 상승 시킬 수 있습니다. |/Etc/modprob.d 디렉터리에 hfs를 사용 하지 않도록 설정 하는 파일을 추가 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r-mods '를 실행 합니다. |
|필요 하지 않은 파일 시스템의 설치 및 사용을 사용 하지 않도록 설정 (hfsplus)<br /><sub>(6.4)</sub> |설명: 공격자가 hfsplus의 취약성을 사용 하 여 권한을 상승 시킬 수 있습니다. |Hfsplus를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r/etc/modprob.d '를 실행 하는 파일을 디렉터리에 추가 합니다. |
|필요 하지 않은 파일 시스템의 설치 및 사용을 사용 하지 않도록 설정 (jffs2)<br /><sub>(6.5)</sub> |설명: 공격자가 jffs2의 취약성을 사용 하 여 권한을 상승 시킬 수 있습니다. |Jffs2를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r/etc/modprob.d '를 실행 하는 파일을 디렉터리에 추가 합니다. |
|커널은 승인 된 원본 에서만 컴파일해야 합니다.<br /><sub>5-10</sub> |설명: 승인 되지 않은 소스의 커널은 공격자에 게 액세스 권한을 부여 하는 취약성 또는 백도어를 포함할 수 있습니다. |배포판 공급 업체에서 제공 하는 커널을 설치 합니다. |
|/etc/shadow 파일 사용 권한은 0400로 설정 되어야 합니다.<br /><sub>(11.1)</sub> |설명: 보안이 올바르게 설정 되지 않은 경우/etc/shadow에서 해시 된 암호를 검색 하거나 조작할 수 있는 공격자입니다. |/Etc/shadow *에 대 한 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r-perms '를 실행 합니다. |
|/etc/shadow-파일 사용 권한은 0400로 설정 되어야 합니다.<br /><sub>(11.2)</sub> |설명: 보안이 올바르게 설정 되지 않은 경우/etc/shadow-에서 해시 된 암호를 검색 하거나 조작할 수 있는 공격자입니다. |/Etc/shadow *에 대 한 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r-perms '를 실행 합니다. |
|/etc/gshadow 파일 사용 권한은 0400로 설정 되어야 합니다.<br /><sub>(11.3)</sub> |설명:이 파일이 적절 하 게 보호 되지 않는 경우 공격자가 보안 그룹에 가입할 수 있습니다. |/Etc/gshadow-의 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r e r-perms '를 실행 합니다. |
|/etc/gshadow-파일 사용 권한은 0400로 설정 되어야 합니다.<br /><sub>(11.4)</sub> |설명:이 파일이 적절 하 게 보호 되지 않는 경우 공격자가 보안 그룹에 가입할 수 있습니다. |/Etc/gshadow의 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r e r-perms '를 실행 합니다. |
|/etc/passwd 파일 사용 권한은 0644 이어야 합니다.<br /><sub>(12.1)</sub> |설명: 공격자가 사용자 id 및 로그인 셸을 수정할 수 있습니다. |/Etc/passwd의 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-perms '를 실행 합니다. |
|/etc/group 파일 사용 권한은 0644 이어야 합니다.<br /><sub>(12.2)</sub> |설명: 공격자가 그룹 멤버 자격을 수정 하 여 권한을 상승 시킬 수 있습니다. |/Etc/group의 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-perms-를 실행 합니다. |
|/etc/passwd-파일 사용 권한은 0600로 설정 되어야 합니다.<br /><sub>(12.3)</sub> |설명:이 파일이 적절 하 게 보호 되지 않는 경우 공격자가 보안 그룹에 가입할 수 있습니다. |/Etc/passwd-의 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-perms를 실행 합니다. |
|/etc/group-파일 사용 권한은 0644 이어야 합니다.<br /><sub>(12.4)</sub> |설명: 공격자가 그룹 멤버 자격을 수정 하 여 권한을 상승 시킬 수 있습니다. |/Etc/group-의 사용 권한 및 소유권을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-perms-를 실행 합니다. |
|Su를 통한 루트 계정에 대 한 액세스는 ' root ' 그룹으로 제한 되어야 합니다.<br /><sub>일</sub> |설명: 공격자가 루트 그룹의 사용자 에게만 su를 제한 하지 않는 경우 암호 추측을 통해 권한을 상승 시킬 수 있습니다. |'/Opt/microsoft/omsagent/plugin/omsremediate ' 명령을 실행 합니다. 이렇게 하면 '/etc/pam.d/su ' 파일에 ' auth required pam_wheel. use_uid ' 줄이 추가 됩니다. |
|' Root ' 그룹이 있어야 하 고,이 그룹에 su를 추가할 수 있는 모든 멤버가 포함 되어 있어야 합니다.<br /><sub>가로</sub> |설명: 공격자가 루트 그룹의 사용자 에게만 su를 제한 하지 않는 경우 암호 추측을 통해 권한을 상승 시킬 수 있습니다. |' Groupadd-g 0 root ' 명령을 통해 루트 그룹을 만듭니다. |
|암호가 없는 계정이 없습니다.<br /><sub>(23.2)</sub> |설명: 공격자가 암호 없이 계정에 로그인 하 고 임의의 명령을 실행할 수 있습니다. |Passwd 명령을 사용 하 여 모든 계정에 대 한 암호를 설정 합니다. |
|Root 이외의 계정에는 0 보다 큰 고유 Uid가 있어야 합니다.<br /><sub>24</sub> |설명: root 이외의 계정에 uid 0이 있으면 공격자가 계정을 손상 시키고 루트 권한을 얻을 수 있습니다. |' Usermod-u '를 사용 하 여 모든 루트가 아닌 계정에 고유 하 고 0이 아닌 고유 uid 할당 |
|가상 메모리 영역의 임의 배치를 사용 하도록 설정 해야 합니다.<br /><sub>가지의</sub> |설명: 공격자는 권한 상승이 발생 하는 메모리의 알려진 지역에 실행 코드를 작성할 수 있습니다. |'/Proc/sys/kernel/randomize_va_space ' 파일에 ' 1 ' 또는 ' 2 ' 값을 추가 합니다. |
|XD/NX 프로세서 기능에 대 한 커널 지원을 사용 하도록 설정 해야 합니다.<br /><sub>26</sub> |설명: 공격자는 시스템에서 메모리의 데이터 영역에 있는 실행 코드를 실행 하 여 권한 상승을 발생 시킬 수 있습니다. |'/Proc/cpuinfo ' 파일에 ' nx ' 플래그가 포함 되어 있는지 확인 합니다. |
|'. '는 루트의 $PATH에 표시 되지 않아야 합니다.<br /><sub>(27.1)</sub> |설명: 공격자가 루트의 $PATH에 악성 파일을 배치 하 여 권한을 상승 시킬 수 있습니다. |/Sa/o\fileprofile에서 ' export PATH = ' 줄을 수정 합니다. |
|사용자 홈 디렉터리는 모드 750 이상 이어야 합니다.<br /><sub>~</sub> |설명: 공격자가 다른 사용자의 홈 폴더에서 중요 한 정보를 검색할 수 있습니다. |홈 폴더 사용 권한을 750으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate |
|Defs.h에서 모든 사용자에 대 한 기본 umask를 077으로 설정 해야 합니다.<br /><sub>명</sub> |설명: 공격자는 다른 사용자가 소유한 파일에서 중요 한 정보를 검색할 수 있습니다. |'/Opt/microsoft/omsagent/plugin/omsremediate-umask ' 명령을 실행 합니다. '/Etc/login.defs ' 파일에 ' UMASK 077 ' 줄을 추가 합니다. |
|모든 부트로더는 암호 보호를 사용 하도록 설정 해야 합니다.<br /><sub>일자로</sub> |설명: 물리적 액세스 권한이 있는 공격자가 부팅 로더 옵션을 수정 하 여 무제한 시스템 액세스를 생성할 수 있습니다. |'/Boot/grub/grub.cfg ' 파일에 부팅 로더 암호를 추가 합니다. |
|부팅 로더 구성에 대 한 사용 권한이 구성 되어 있는지 확인<br /><sub>(31.1)</sub> |설명: 루트에 대 한 읽기 및 쓰기 권한으로 설정 하는 경우에만 루트가 아닌 사용자가 부팅 매개 변수를 보거나 변경할 수 없습니다. 부팅 매개 변수를 읽는 루트가 아닌 사용자는 부팅 시 보안 약점을 식별할 수 있으며이를 악용할 수 있습니다. |부팅 로더의 소유자와 그룹을 root: root로 설정 하 고 사용 권한을 0400으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r 부팅 |
|단일 사용자 모드에 인증을 사용 해야 합니다.<br /><sub>(33)</sub> |설명: 단일 사용자 모드로 인증을 요구 하면 권한이 없는 사용자가 시스템을 단일 사용자로 다시 부팅 하 여 자격 증명 없이 루트 권한을 확보할 수 없습니다. |다음 명령을 실행 하 여 루트 사용자에 대 한 암호를 설정 합니다. `passwd root` |
|패킷 리디렉션 전송이 사용 하지 않도록 설정 되어 있는지 확인 하십시오.<br /><sub>(38.3)</sub> |설명: 공격자가 손상 된 호스트를 사용 하 여 라우팅을 손상 시키고 사용자에 게 유효한 시스템이 아닌 공격자가 설정한 시스템에 액세스 하도록 할 수 있습니다. |/etc/sysctl.conf에서 다음 매개 변수를 설정 합니다. ' net.ipv4.conf.all.send_redirects = 0 ' 및 ' net.ipv4.conf.default.send_redirects = 0 ' 또는 '/opt/microsoft/omsagent/plugin/omsremediate-r-송신-리디렉션 '을 실행 합니다. |
|모든 인터페이스에 대해 ICMP 리디렉션 보내기를 사용 하지 않도록 설정 해야 합니다. (net.ipv4.conf.default.accept_redirects = 0)<br /><sub>(38.4)</sub> |설명: 공격자가이 시스템의 라우팅 테이블을 변경 하 여 트래픽을 대체 대상으로 리디렉션할 수 있습니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r-------리디렉션 '을 실행 합니다. |
|모든 인터페이스에 대해 ICMP 리디렉션 보내기를 사용 하지 않도록 설정 해야 합니다. (net.ipv4.conf.default.secure_redirects = 0)<br /><sub>(38.5)</sub> |설명: 공격자가이 시스템의 라우팅 테이블을 변경 하 여 트래픽을 대체 대상으로 리디렉션할 수 있습니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r e n t-r-리디렉션 ' 실행 |
|모든 인터페이스에 대해 원본 라우트된 패킷을 허용 하지 않도록 설정 해야 합니다. (net.ipv4.conf.all.accept_source_route = 0)<br /><sub>(40.1)</sub> |설명: 공격자가 악의적인 목적으로 트래픽을 리디렉션할 수 있습니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate '를 실행 합니다. |
|모든 인터페이스에 대해 원본 라우트된 패킷을 허용 하지 않도록 설정 해야 합니다. (net.ipv6.conf.all.accept_source_route = 0) 또는 '/opt/microsoft/omsagent/plugin/omsremediate ' 실행<br /><sub>(40.2)</sub> |설명: 공격자가 악의적인 목적으로 트래픽을 리디렉션할 수 있습니다. |를 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 합니다. |
|브로드캐스트에 대 한 가짜 ICMP 응답을 무시 해야 합니다. (net.ipv4.icmp_ignore_bogus_error_responses = 1)<br /><sub>(43)</sub> |설명: 공격자가 DoS로 인해 ICMP 공격을 수행할 수 있습니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r e r-r e r----------응답 '을 실행 합니다. |
|브로드캐스트/멀티 캐스트 주소로 전송 된 ICMP 에코 요청 (ping)을 무시 합니다. (net.ipv4.icmp_echo_ignore_broadcasts = 1)<br /><sub>(44)</sub> |설명: 공격자가 DoS로 인해 ICMP 공격을 수행할 수 있습니다. |을 (를 `sysctl -w key=value` ) 실행 하 고 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r e n----------브로드캐스트 '를 실행 합니다. |
|모든 인터페이스에 대해 martian 패킷 (주소가 허용 되지 않는 주소 포함)의 로깅을 사용 하도록 설정 해야 합니다. (net.ipv4.conf.all.log_martians = 1)<br /><sub>(45.1)</sub> |설명: 공격자가 감지 하지 않고 스푸핑된 주소에서 트래픽을 보낼 수 있습니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r martians '를 실행 합니다. |
|모든 인터페이스에 대해 역방향 경로를 사용 하 여 원본 유효성 검사를 수행 해야 합니다. (net.ipv4.conf.all.rp_filter = 1)<br /><sub>(46.1)</sub> |설명: 시스템이 unroutable 된 주소의 트래픽을 허용 합니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate |
|모든 인터페이스에 대해 역방향 경로를 사용 하 여 원본 유효성 검사를 수행 해야 합니다. (net.ipv4.conf.default.rp_filter = 1)<br /><sub>(46.2)</sub> |설명: 시스템이 unroutable 된 주소의 트래픽을 허용 합니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate |
|TCP sy쿠키를 사용 하도록 설정 해야 합니다. (net.ipv4.tcp_syncookies = 1)<br /><sub>(47)</sub> |설명: 공격자가 TCP를 통해 DoS를 수행할 수 있습니다. |을 실행 `sysctl -w key=value` 하 고를 규격 값으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r------sy쿠키 '를 실행 합니다. |
|시스템이 네트워크 스니퍼로 작동 해서는 안 됩니다.<br /><sub>(48)</sub> |설명: 공격자가 무차별 인터페이스를 사용 하 여 네트워크 트래픽을 찾아낼 수 있습니다. |'/Etc/network/interfaces ' 또는 '/etc/rc.local. '의 ' promisc ' 항목을 통해 무차별 모드를 사용할 수 있습니다. 두 파일을 모두 확인 하 고이 항목을 제거 합니다. |
|모든 무선 인터페이스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(49)</sub> |설명: 공격자가 가짜 AP를 만들어 전송을 가로챌 수 있습니다. |모든 무선 인터페이스가 '/etc/network/interfaces '에서 사용 하지 않도록 설정 되었는지 확인 |
|IPv6 프로토콜을 사용 하도록 설정 해야 합니다.<br /><sub>(50)</sub> |설명: 최신 네트워크에서 통신 하는 데 필요 합니다. |/Etc/sysctl.conf를 열고 ' net.ipv6.conf.all.disable_ipv6 ' 및 ' net.ipv6.conf.default.disable_ipv6 '이 0으로 설정 되어 있는지 확인 합니다. |
|DCCP를 사용 하지 않도록 설정<br /><sub>(54)</sub> |설명: 프로토콜이 필요 하지 않은 경우 잠재적인 공격 노출 영역을 줄이기 위해 드라이버를 설치 하지 않는 것이 좋습니다. |디렉터리에서 파일을 편집 하거나 만든 `/etc/modprobe.d/` `install dccp /bin/true` 후에 dccp 모듈을 추가 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r mods '를 실행 합니다. |
|SCTP를 사용 하지 않도록 설정<br /><sub>(55)</sub> |설명: 프로토콜이 필요 하지 않은 경우 잠재적인 공격 노출 영역을 줄이기 위해 드라이버를 설치 하지 않는 것이 좋습니다. |디렉터리에서 파일을 편집 하거나 만든 `/etc/modprobe.d/` 후 파일을 추가 하거나 `install sctp /bin/true` sctp 모듈을 언로드하고 '/opt/microsoft/omsagent/plugin/omsremediate-r-mods '를 실행 합니다. |
|RDS에 대 한 지원을 사용 하지 않도록 설정 합니다.<br /><sub>(56)</sub> |설명: 공격자가 RDS의 취약성을 사용 하 여 시스템을 손상 시킬 수 있습니다. |디렉터리에서 파일을 편집 하거나 만든 `/etc/modprobe.d/` 다음,이 파일을 추가 하 여 `install rds /bin/true` rds 모듈을 언로드하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r mods '를 실행 합니다. |
|TIPC를 사용 하지 않도록 설정<br /><sub>(57)</sub> |설명: 프로토콜이 필요 하지 않은 경우 잠재적인 공격 노출 영역을 줄이기 위해 드라이버를 설치 하지 않는 것이 좋습니다. |파일을 편집 하거나 파일을 만든 `/etc/modprobe.d/` 후 파일을 추가 하거나 `install tipc /bin/true` tipc 모듈을 언로드하고 '/opt/microsoft/omsagent/plugin/omsremediate-r mods '를 실행 합니다. |
|로깅이 구성 되어 있는지 확인<br /><sub>(60)</sub> |설명: 많은 중요 한 보안 관련 정보는를 통해 전송 됩니다. 예를 들어, `rsyslog` 성공 및 실패 한 su 시도, 실패 한 로그인 시도, 루트 로그인 시도 등이 있습니다. |해당 하는 경우 syslog, rsyslog 또는 syslog를 구성 합니다. |
|Syslog, rsyslog 또는 syslog 패키지를 설치 해야 합니다.<br /><sub>(61)</sub> |설명: 안정성 및 보안 문제가 로깅되지 않으며 적절 한 진단을 방지 합니다. |Rsyslog 패키지를 설치 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r install-rsyslog '를 실행 합니다. |
|로깅 서비스가 사용 하도록 설정 되어 있는지 확인<br /><sub>(62)</sub> |설명: 노드에 이벤트를 기록 하는 기능이 있어야 합니다. |Rsyslog 패키지를 사용 하도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r enable-rsyslog '를 실행 합니다. |
|모든 rsyslog 로그 파일에 대 한 파일 사용 권한은 640 또는 600로 설정 되어야 합니다.<br /><sub>(63)</sub> |설명: 공격자가 로그를 조작 하 여 작업을 처리할 수 있습니다. |' $FileCreateMode 0640 ' 줄을 '/etc/rsyslog.conf ' 파일에 추가 합니다. |
|로 거 구성 파일이 제한 되어 있는지 확인 하세요.<br /><sub>(63.1)</sub> |설명: 중요 한 syslog 데이터를 보관 하 고 보호 하기 위해 로그 파일이 존재 하 고 올바른 사용 권한이 있는지 확인 하는 것이 중요 합니다. |로 거의 구성 파일을 0640으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-s e r-s e r-r e c-사용 권한 ' 실행 |
|모든 rsyslog 로그 파일은 adm 그룹이 소유 해야 합니다.<br /><sub>(64)</sub> |설명: 공격자가 로그를 조작 하 여 작업을 처리할 수 있습니다. |' $FileGroup adm ' 줄을 '/etc/rsyslog.conf ' 파일에 추가 합니다. |
|모든 rsyslog 로그 파일은 syslog 사용자가 소유 해야 합니다.<br /><sub>(65)</sub> |설명: 공격자가 로그를 조작 하 여 작업을 처리할 수 있습니다. |'/Etc/rsyslog.conf ' 파일에 ' $FileOwner syslog ' 줄을 추가 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r syslog-소유자 '를 실행 합니다. |
|Rsyslog는 원격 메시지를 수락 하면 안 됩니다.<br /><sub>(67)</sub> |설명: 공격자가 syslog로 메시지를 삽입 하 여 DoS를 발생 시키거나 다른 활동에서 혼란을 일으킬 수 있습니다. |'/Etc/rsyslog.conf ' 파일에서 ' $ModLoad imudp ' 및 ' $ModLoad imudp ' 줄을 제거 합니다. |
|Logrotate (syslog rotater) 서비스를 사용 하도록 설정 해야 합니다.<br /><sub>(68)</sub> |설명: 로그 파일이 무제한으로 확장 되어 모든 디스크 공간을 사용할 수 있습니다. |Logrotate 패키지를 설치 하 고 logrotate cron 항목이 활성 상태 인지 확인 합니다 (chmod 755/etc/cron.daily/logrotate; chown root: root/etc/cron.daily/logrotate). |
|Rlogin 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(69)</sub> |설명: 공격자가 엄격한 인증 요구 사항을 무시 하 고 액세스 권한을 얻을 수 있습니다. |Inetd 서비스를 제거 합니다. |
|필요 하지 않은 경우 inetd를 사용 하지 않도록 설정 합니다. (inetd)<br /><sub>(70.1)</sub> |설명: 공격자가 inetd 서비스의 취약성을 악용 하 여 액세스 권한을 얻을 수 있습니다. |Inetd 서비스 제거 (apt-get remove inetd) |
|필요 하지 않은 경우 xinetd를 사용 하지 않도록 설정 합니다. (xinetd)<br /><sub>(70.2)</sub> |설명: 공격자가 xinetd 서비스의 취약성을 악용 하 여 액세스 권한을 얻을 수 있습니다. |Inetd 서비스 제거 (apt-get remove xinetd) |
|배포판에 필요한 경우에만 inetd를 설치 합니다. 현재 강화 표준에 따라 보안. (필요한 경우)<br /><sub>(71.1)</sub> |설명: 공격자가 inetd 서비스의 취약성을 악용 하 여 액세스 권한을 얻을 수 있습니다. |Inetd 서비스 제거 (apt-get remove inetd) |
|배포판에 필요한 경우에만 xinetd를 설치 합니다. 현재 강화 표준에 따라 보안. (필요한 경우)<br /><sub>(71.2)</sub> |설명: 공격자가 xinetd 서비스의 취약성을 악용 하 여 액세스 권한을 얻을 수 있습니다. |Inetd 서비스 제거 (apt-get remove xinetd) |
|텔넷 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(72)</sub> |설명: 공격자가 암호화 되지 않은 텔넷 세션을 도청 하거나 가로챌 수 있습니다. |'/Etc/inetd.conf ' 파일에서 telnet 항목을 제거 하거나 주석으로 처리 합니다. |
|모든 telnetd 패키지를 제거 해야 합니다.<br /><sub>(73)</sub> |설명: 공격자가 암호화 되지 않은 텔넷 세션을 도청 하거나 가로챌 수 있습니다. |Telnetd 패키지 제거 |
|Rcp/rsh 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(74)</sub> |설명: 공격자가 암호화 되지 않은 세션을 도청 하거나 가로챌 수 있습니다. |'/Etc/inetd.conf ' 파일에서 셸 항목을 제거 하거나 주석으로 처리 합니다. |
|Rsh 서버 패키지를 제거 해야 합니다.<br /><sub>(77)</sub> |설명: 공격자가 암호화 되지 않은 rsh 세션을 도청 하거나 가로챌 수 있습니다. |Rsh-서버 패키지 제거 (apt-get rsh-server) |
|Ypbind 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(78)</sub> |설명: 공격자가 ypbind 서비스에서 중요 한 정보를 검색할 수 있습니다. |Nis 패키지 제거 (apt remove nis) |
|Nis 패키지를 제거 해야 합니다.<br /><sub>(79)</sub> |설명: 공격자가 NIS 서비스에서 중요 한 정보를 검색할 수 있습니다. |Nis 패키지 제거 (apt remove nis) |
|Tftp 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(80)</sub> |설명: 공격자가 암호화 되지 않은 세션을 도청 하거나 가로챌 수 있습니다. |'/Etc/inetd.conf ' 파일에서 tftp 항목을 제거 합니다. |
|Tftpd 패키지를 제거 해야 합니다.<br /><sub>(81)</sub> |설명: 공격자가 암호화 되지 않은 세션을 도청 하거나 가로챌 수 있습니다. |Tftpd 패키지 제거 (apt-get remove tftpd) |
|미리 읽기-fedora 패키지를 제거 해야 합니다.<br /><sub>(82)</sub> |설명: 상당한 노출이 없으며 상당한 이점을 제공 하지 않습니다. |미리 읽기-fedora 패키지 제거 (apt-get remove 미리 읽기-fedora) |
|Bluetooth/hidd 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(84)</sub> |설명: 공격자가 무선 통신을 가로채 나 조작할 수 있습니다. |Bluetooth 패키지 제거 (apt) |
|Isdn 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(86)</sub> |설명: 공격자가 모뎀을 사용 하 여 무단 액세스를 얻을 수 있습니다. |Isdnutils 패키지 제거 (apt-get isdnutils-base) |
|Isdnutils 패키지를 제거 해야 합니다.<br /><sub>(87)</sub> |설명: 공격자가 모뎀을 사용 하 여 무단 액세스를 얻을 수 있습니다. |Isdnutils 패키지 제거 (apt-get isdnutils-base) |
|Kdump 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(88)</sub> |설명: 공격자가 이전 시스템 크래시를 분석 하 여 중요 한 정보를 검색할 수 있습니다. |Kdump 도구 패키지 제거 (apt-get remove kdump-tools) |
|Zeroconf 네트워킹을 사용 하지 않도록 설정 해야 합니다.<br /><sub>(89)</sub> |설명: 공격자는이를 악용 하 여 네트워크 시스템에 대 한 정보를 얻거나 신뢰 모델의 결함으로 인 한 DNS 요청을 스푸핑할 수 있습니다. |RedHat, CentOS 및 Oracle의 경우: `NOZEROCONF=yes or no` /etc/sysconfig/network.에 추가 다른 모든 배포판의 경우: '/etc/network/interfaces ' 파일에서 ' ipv4ll ' 항목을 제거 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r zeroconf '를 실행 합니다. |
|Crond 서비스를 사용 하도록 설정 해야 합니다.<br /><sub>(90)</sub> |설명: 정기 유지 관리 작업을 위해 거의 모든 시스템에 Cron가 필요 합니다. |Cron 패키지 (apt-get install-y cron)를 설치 하 고 '/etc/init/cron.conf ' 파일에 ' start on 실행 수준 [2345] ' 줄이 포함 되어 있는지 확인 합니다. |
|/Etc/anacrontab에 대 한 파일 사용 권한은 root: root 600로 설정 되어야 합니다.<br /><sub>(91)</sub> |설명: 공격자는이 파일을 조작 하 여 예약 된 작업을 방지 하거나 악의적인 작업을 실행할 수 있습니다. |/Etc/anacrontab에 대 한 소유권 및 사용 권한을 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r anacrontab-perms '를 실행 합니다. |
|/Etc/cron.d에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(93)</sub> |설명: 권한이 없는 사용자에 게이 디렉터리에 대 한 쓰기 권한을 부여 하면 권한 상승 된 권한 부여를 얻는 수단을 제공할 수 있습니다. 이 디렉터리에 대 한 읽기 권한을 부여 하면 권한 없는 사용자가 상승 된 권한을 얻거나 감사 제어를 우회 하는 방법에 대 한 정보를 얻을 수 있습니다. |/Etc/chron.d의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0700로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r cron-perms '를 실행 합니다. |
|/Etc/cron.daily에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(94)</sub> |설명: 권한이 없는 사용자에 게이 디렉터리에 대 한 쓰기 권한을 부여 하면 권한 상승 된 권한 부여를 얻는 수단을 제공할 수 있습니다. 이 디렉터리에 대 한 읽기 권한을 부여 하면 권한 없는 사용자가 상승 된 권한을 얻거나 감사 제어를 우회 하는 방법에 대 한 정보를 얻을 수 있습니다. |/Etc/chron.daily의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0700으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r cron-perms를 실행 합니다. |
|/Etc/cron.hourly에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(95)</sub> |설명: 권한이 없는 사용자에 게이 디렉터리에 대 한 쓰기 권한을 부여 하면 권한 상승 된 권한 부여를 얻는 수단을 제공할 수 있습니다. 이 디렉터리에 대 한 읽기 권한을 부여 하면 권한 없는 사용자가 상승 된 권한을 얻거나 감사 제어를 우회 하는 방법에 대 한 정보를 얻을 수 있습니다. |/Etc/chron.hourly의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0700으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r cron-perms를 실행 합니다. |
|/Etc/cron.monthly에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(96)</sub> |설명: 권한이 없는 사용자에 게이 디렉터리에 대 한 쓰기 권한을 부여 하면 권한 상승 된 권한 부여를 얻는 수단을 제공할 수 있습니다. 이 디렉터리에 대 한 읽기 권한을 부여 하면 권한 없는 사용자가 상승 된 권한을 얻거나 감사 제어를 우회 하는 방법에 대 한 정보를 얻을 수 있습니다. |/Etc/chron.monthly의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0700으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r cron-perms를 실행 합니다. |
|/Etc/cron.weekly에 대 한 사용 권한이 구성 되었는지 확인 합니다.<br /><sub>(97)</sub> |설명: 권한이 없는 사용자에 게이 디렉터리에 대 한 쓰기 권한을 부여 하면 권한 상승 된 권한 부여를 얻는 수단을 제공할 수 있습니다. 이 디렉터리에 대 한 읽기 권한을 부여 하면 권한 없는 사용자가 상승 된 권한을 얻거나 감사 제어를 우회 하는 방법에 대 한 정보를 얻을 수 있습니다. |/Etc/chron.weekly의 소유자와 그룹을 root로 설정 하 고 사용 권한을 0700으로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r cron-perms를 실행 합니다. |
|/Cron 권한이 부여 된 사용자로 제한 되는지 확인<br /><sub>(98)</sub> |설명: 많은 시스템에서 시스템 관리자 에게만 작업을 예약할 수 있는 권한이 부여 됩니다. `cron` 이 파일을 사용 하 여 `cron.allow` 작업을 실행할 수 있는 사람을 제어 하면 `cron` 이 정책이 적용 됩니다. 허용 목록을 거부 목록 보다 더 쉽게 관리할 수 있습니다. 거부 목록에서 잠재적으로 시스템에 사용자 ID를 추가 하 고 거부 파일에 추가 하는 것을 잊은 경우 |/etc/cron.deny 및/etc/at.deny을 해당 파일로 바꿉니다. `allow` |
|원격 로그인 경고 배너가 올바르게 구성 되어 있는지 확인 하세요.<br /><sub>(111)</sub> |설명: 경고 메시지는 시스템에 대해 법적 상태를 증명 하는 시스템에 로그인을 시도 하는 사용자에 게 알리고 시스템을 소유 하 고 있는 모든 모니터링 정책을 포함 해야 합니다. 또한 로그인 배너에 OS 및 패치 수준 정보를 표시 하면 공격자에 게 시스템의 특정 악용을 대상으로 하는 자세한 시스템 정보를 제공 하는 부작용이 있습니다. 권한 있는 사용자는 로그인 한 후 명령을 실행 하 여이 정보를 쉽게 얻을 수 있습니다 `uname -a` . |/Etc/issue.net 파일에서 \m \r \s 및 \v의 인스턴스를 제거 합니다. |
|로컬 로그인 경고 배너가 올바르게 구성 되어 있는지 확인 하세요.<br /><sub>(111.1)</sub> |설명: 경고 메시지는 시스템에 대해 법적 상태를 증명 하는 시스템에 로그인을 시도 하는 사용자에 게 알리고 시스템을 소유 하 고 있는 모든 모니터링 정책을 포함 해야 합니다. 또한 로그인 배너에 OS 및 패치 수준 정보를 표시 하면 공격자에 게 시스템의 특정 악용을 대상으로 하는 자세한 시스템 정보를 제공 하는 부작용이 있습니다. 권한 있는 사용자는 로그인 한 후 명령을 실행 하 여이 정보를 쉽게 얻을 수 있습니다 `uname -a` . |/Etc/issue 파일에서 \m \r \s 및 \v의 인스턴스를 제거 합니다. |
|Avahi-디먼 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(114)</sub> |설명: 공격자는 avahi 디먼의 취약성을 사용 하 여 액세스 권한을 얻을 수 있습니다. |Avahi-디먼 서비스를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate ' 실행 |
|Cup 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(115)</sub> |설명: 공격자가 cup 서비스의 결함을 사용 하 여 권한을 상승 시킬 수 있습니다. |Cup 서비스를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r cup '를 실행 합니다. |
|Isc-dhcpd 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(116)</sub> |설명: 공격자는 dhcpd를 사용 하 여 클라이언트에 잘못 된 정보를 제공할 수 있으며 정상 작업을 방해 합니다. |Isc-dhcp-서버 패키지 (apt-get isc-dhcp 서버)를 제거 합니다. |
|Isc-dhcp 서버 패키지를 제거 해야 합니다.<br /><sub>(117)</sub> |설명: 공격자는 dhcpd를 사용 하 여 클라이언트에 잘못 된 정보를 제공할 수 있으며 정상 작업을 방해 합니다. |Isc-dhcp-서버 패키지 (apt-get isc-dhcp 서버)를 제거 합니다. |
|Sendmail 패키지를 제거 해야 합니다.<br /><sub>(120)</sub> |설명: 공격자가이 시스템을 사용 하 여 악의적인 콘텐츠가 있는 전자 메일을 다른 사용자에 게 보낼 수 있습니다. |Sendmail 패키지 제거 (apt-get sendmail 가져오기) |
|후 위 패키지를 제거 해야 합니다.<br /><sub>(121)</sub> |설명: 공격자가이 시스템을 사용 하 여 악의적인 콘텐츠가 있는 전자 메일을 다른 사용자에 게 보낼 수 있습니다. |후 위 패키지 제거 (apt-제거 후 위) 또는 '/opt/microsoft/omsagent/plugin/omsremediate-r 제거-후 위 ' 실행 |
|후 위 네트워크 수신은 적절 하 게 사용 하지 않도록 설정 해야 합니다.<br /><sub>(122)</sub> |설명: 공격자가이 시스템을 사용 하 여 악의적인 콘텐츠가 있는 전자 메일을 다른 사용자에 게 보낼 수 있습니다. |' Inet_interfaces localhost ' 줄을 '/etc/postfix/main.cf ' 파일에 추가 합니다. |
|Ldap 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(124)</sub> |설명: 공격자가이 호스트의 LDAP 서비스를 조작 하 여 LDAP 클라이언트에 거짓 데이터를 배포할 수 있습니다. |Slapd 패키지 제거 (apt-get remove slapd) |
|Rpcgssd 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(126)</sub> |설명: 공격자가 rpcgssd/nfs의 결함을 사용 하 여 액세스 권한을 얻을 수 있습니다. |Rpcgssd 서비스를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r disable-rpcgssd '를 실행 합니다. |
|Rpcidmapd 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(127)</sub> |설명: 공격자가 idmapd/nfs의 결함을 사용 하 여 액세스 권한을 얻을 수 있습니다. |Rpcidmapd 서비스를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r disable-rpcidmapd '를 실행 합니다. |
|Portmap 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(129)</sub> |설명: 공격자가 portmap의 결함을 사용 하 여 액세스 권한을 얻을 수 있습니다. |Rpcbind 서비스를 사용 하지 않도록 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r rpcbind '를 실행 합니다. |
|Rpcsvcgssd 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(130)</sub> |설명: 공격자가 rpcsvcgssd의 결함을 사용 하 여 액세스 권한을 얻을 수 있습니다. |'/Etc/inetd.conf ' 파일에서 ' NEED_SVCGSSD = yes ' 줄을 제거 합니다. |
|명명 된 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(131)</sub> |설명: 공격자는 DNS 서비스를 사용 하 여 클라이언트에 거짓 데이터를 배포할 수 있습니다. |Bind9 패키지 제거 (apt-get remove bind9) |
|바인딩 패키지를 제거 해야 합니다.<br /><sub>(132)</sub> |설명: 공격자는 DNS 서비스를 사용 하 여 클라이언트에 거짓 데이터를 배포할 수 있습니다. |Bind9 패키지 제거 (apt-get remove bind9) |
|Dovecot 서비스를 사용 하지 않도록 설정 해야 합니다.<br /><sub>(137)</sub> |설명: 시스템을 IMAP/POP3 서버로 사용할 수 있습니다. |Dovecot 패키지 제거 (apt-get dovecot-core) |
|Dovecot 패키지를 제거 해야 합니다.<br /><sub>(138)</sub> |설명: 시스템을 IMAP/POP3 서버로 사용할 수 있습니다. |Dovecot 패키지 제거 (apt-get dovecot-core) |
|`+`/Etc/passwd에 레거시 항목이 존재 하지 않는지 확인 합니다.<br /><sub>(156.1)</sub> |설명: 공격자가 암호 없이 ' + ' 사용자 이름을 사용 하 여 액세스 권한을 얻을 수 있습니다. |' +: '로 시작 하는/etc/passwd의 모든 항목을 제거 합니다. |
|`+`/Etc/shadow에 레거시 항목이 존재 하지 않는지 확인 합니다.<br /><sub>(156.2)</sub> |설명: 공격자가 암호 없이 ' + ' 사용자 이름을 사용 하 여 액세스 권한을 얻을 수 있습니다. |' +: '로 시작 하는/etc/shadow의 모든 항목을 제거 합니다. |
|`+`/Etc/group에 레거시 항목이 존재 하지 않는지 확인 합니다.<br /><sub>(156.3)</sub> |설명: 공격자가 암호 없이 ' + ' 사용자 이름을 사용 하 여 액세스 권한을 얻을 수 있습니다. |' +: '로 시작 하는/etc/group의 모든 항목을 제거 합니다. |
|암호 만료가 365 일 이내 인지 확인 합니다.<br /><sub>(157.1)</sub> |설명: 공격자가 손상 된 자격 증명을 활용 하거나 온라인 무차별 암호 대입 공격을 통해 자격 증명을 성공적으로 손상 시킬 수 있는 기회는 암호의 보존 기간에 따라 제한 됩니다. 따라서 암호의 최대 보존 기간을 줄이면 공격자의 기회 창이 줄어듭니다. |`PASS_MAX_DAYS`매개 변수를 365에서 이하로 설정 `/etc/login.defs` 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-a s t '를 실행 합니다. |
|암호 만료 경고 일 수를 7 이상으로 유지 합니다.<br /><sub>(157.2)</sub> |설명: 암호를 만료 하는 사전 경고를 제공 하면 사용자가 보안 암호를 생각해 볼 수 있습니다. 인식 하지 못하는 사용자는 단순 암호를 선택 하거나 검색할 수 있는 위치에 기록할 수 있습니다. |`PASS_WARN_AGE`에서 매개 변수를 7로 설정 하거나 '/opt/microsoft/omsagent/plugin/omsremediate-r-w i n k- `/etc/login.defs` age '를 실행 합니다. |
|암호 다시 사용이 제한 되어 있는지 확인 합니다.<br /><sub>(157.5)</sub> |설명: 사용자가 지난 5 암호를 다시 사용 하지 않도록 하면 공격자가 암호를 추측할 수 없게 됩니다. |/Etc/pam.d/common-password 또는/etc/pam.d/password_auth 및/etc/pam.d/system_auth에서 ' 기억 ' 옵션이 최소 5로 설정 되어 있는지 확인 하거나 '/opt/microsoft/omsagent/plugin/omsremediate '를 실행 합니다. |
|암호 해시 알고리즘이 SHA-512 인지 확인<br /><sub>(157.11)</sub> |설명: SHA-512 알고리즘은 MD5 보다 훨씬 더 강력한 해시를 제공 하므로 공격자가 암호를 성공적으로 확인 하는 데 필요한 노력 수준을 높여 시스템에 추가 보호 기능을 제공 합니다. 참고: 이러한 변경 내용은 로컬 시스템에 구성 된 계정에만 적용 됩니다. |암호 해시 알고리즘을 sha512로 설정 합니다. 많은 배포판에서 PAM 구성을 업데이트 하는 도구를 제공 합니다. 자세한 내용은 설명서를 참조 하세요. 제공 된 도구가 없는 경우 적절 한 `/etc/pam.d/` 구성 파일을 편집 하 고 sha512 옵션을 포함 하는 줄을 추가 하거나 수정 합니다 `pam_unix.so` . ``` password sufficient pam_unix.so sha512 ``` |
|암호 변경 사이의 최소 기간이 7 이상 인지 확인 합니다.<br /><sub>(157.12)</sub> |설명: 암호 변경 빈도를 제한 하 여 관리자가 암호 재사용 제어를 회피 하기 위해 사용자가 암호를 반복 해 서 변경 하지 못하도록 차단할 수 있습니다. |`PASS_MIN_DAYS`매개 변수를 7의 7로 설정 `/etc/login.defs` `PASS_MIN_DAYS 7` 합니다. 암호를 일치 하도록 설정 된 모든 사용자의 사용자 매개 `chage --mindays 7`  변수를 수정 합니다. |
|모든 사용자의 마지막 암호 변경 날짜가 과거 인지 확인<br /><sub>(157.14)</sub> |설명: 사용자가 기록한 암호 변경 날짜를 나중에 사용 하는 경우 암호 만료를 임의로 설정 하지 않을 수 있습니다. |비활성 암호 잠금이 30 일 이내 인지 확인 합니다. 다음 명령을 실행 하 여 기본 암호 비활성 기간을 30 일로 설정 합니다. ``` # useradd -D -f 30 ``` 암호를 일치 하도록 설정 된 모든 사용자의 사용자 매개 변수 수정: ``` # chage --inactive 30  ``` |
|시스템 계정이 로그인이 아닌지 확인<br /><sub>(157.15)</sub> |설명: 일반 사용자가 사용 하지 않는 계정을 대화형 셸을 제공 하는 데 사용할 수 없도록 하는 것이 중요 합니다. 기본적으로 Ubuntu는 이러한 계정에 대 한 암호 필드를 잘못 된 문자열로 설정 하지만 암호 파일의 셸 필드를로 설정 하는 것이 좋습니다 `/usr/sbin/nologin` . 이렇게 하면 계정이 모든 명령을 실행 하는 데 사용 될 수 없습니다. |감사 스크립트에서 반환 하는 모든 계정에 대해 셸을 설정 합니다. `/sbin/nologin` |
|루트 계정에 대 한 기본 그룹이 GID 0 인지 확인<br /><sub>(157.16)</sub> |설명: 계정에 GID 0을 사용 `_root_ ` 하면 `_root_` 권한이 없는 사용자가 실수로 소유한 파일에 액세스할 수 없게 됩니다. |다음 명령을 실행 하 여 `root` 사용자 기본 그룹을 GID로 설정 합니다 `0` . ``` # usermod -g 0 root ``` |
|Root가 유일한 UID 0 계정 인지 확인<br /><sub>(157.18)</sub> |설명:이 액세스는 기본 계정 으로만 제한 하 `root ` 고 시스템 콘솔 에서만 사용 해야 합니다. 승인 된 메커니즘을 사용 하 여 관리 액세스 권한이 없는 계정을 통해 액세스 해야 합니다. |UID가 아닌 모든 사용자를 제거 `root` `0` 하거나 해당 하는 경우 새 uid를 할당 합니다. |
|불필요 한 패키지 제거<br /><sub>(158)</sub> |설명:  |'/Opt/microsoft/omsagent/plugin/omsremediate-r r o r e r--가로-공통 |
|불필요 한 계정 제거<br /><sub>(159)</sub> |설명: 규정 준수 |불필요 한 계정 제거 |
|Auditd 서비스가 사용 하도록 설정 되어 있는지 확인<br /><sub>(162)</sub> |설명: 시스템 이벤트를 캡처하는 것은 시스템 관리자에 게 시스템에 대 한 무단 액세스가 발생 하는지 여부를 확인 하는 데 사용할 수 있는 정보를 제공 합니다. |감사 패키지 설치 (systemctl 사용 감사 d) |
|AuditD 서비스 실행<br /><sub>(163)</sub> |설명: 시스템 이벤트를 캡처하는 것은 시스템 관리자에 게 시스템에 대 한 무단 액세스가 발생 하는지 여부를 확인 하는 데 사용할 수 있는 정보를 제공 합니다. |AuditD 서비스 실행 (systemctl 시작 auditd) |
|SNMP 서버를 사용 하도록 설정 하지 않았는지 확인 합니다.<br /><sub>(179)</sub> |설명: SNMP 서버는 SNMP v1을 사용 하 여 통신할 수 있으며,이는 암호화 되지 않은 데이터를 전송 하 고 명령을 실행 하기 위한 인증이 필요 하지 않습니다. 반드시 필요한 경우가 아니면 SNMP 서비스를 사용 하지 않는 것이 좋습니다. SNMP가 필요한 경우 서버에서 SNMP v1을 허용 하지 않도록 구성 해야 합니다. |다음 명령 중 하나를 실행 하 여 사용 하지 않도록 설정 합니다 `snmpd` . ``` # chkconfig snmpd off ``` ``` # systemctl disable snmpd `````` # update-rc.d snmpd disable ``` |
|Rsync 서비스가 사용 하도록 설정 되어 있지 않은지 확인 합니다.<br /><sub>(181)</sub> |설명: `rsyncd` 통신에 암호화 되지 않은 프로토콜을 사용 하므로 서비스에서 보안 위험을 제시 합니다. |다음 명령 중 하나를 실행 하 여을 사용 하지 않도록 설정 합니다 `rsyncd` . `chkconfig rsyncd off` , `systemctl disable rsyncd` 또는를 실행 하려면 `update-rc.d rsyncd disable` '/opt/microsoft/omsagent/plugin/omsremediate-r disable-rsysnc '를 실행 합니다. |
|NIS 서버 사용 안 함 확인<br /><sub>(182)</sub> |설명: NIS 서비스는 기본적으로 DOS 공격에 취약 하 고, 버퍼 오버플로를 발생 하 고, NIS 맵 쿼리를 위한 잘못 된 인증을 받은 안전 하지 않은 시스템입니다. 일반적으로 NIS는 LDAP (Lightweight Directory Access Protocol)와 같은 프로토콜로 대체 되었습니다. 서비스를 사용 하지 않도록 설정 하 고 기타 보안 서비스를 사용 하는 것이 좋습니다. |다음 명령 중 하나를 실행 하 여 사용 하지 않도록 설정 합니다 `ypserv` . ``` # chkconfig ypserv off ``` ``` # systemctl disable ypserv `````` # update-rc.d ypserv disable ``` |
|Rsh 클라이언트가 설치 되지 않았는지 확인 합니다.<br /><sub>(183)</sub> |설명: 이러한 레거시 클라이언트는 수많은 보안 노출을 포함 하 고 더 안전한 SSH 패키지로 대체 되었습니다. 서버를 제거 하는 경우에도 사용자가 실수로 이러한 명령을 사용 하 여 자격 증명을 노출 하는 것을 방지 하기 위해 클라이언트가 제거 되도록 하는 것이 좋습니다. 패키지를 제거 하면 `rsh ` , 및에 대 한 클라이언트가 제거 됩니다 `rsh` `rcp ` `rlogin` . |`rsh`적절 한 패키지 관리자 또는 수동 설치를 사용 하 여를 제거 합니다. ``` yum remove rsh ``` ``` apt-get remove rsh `````` zypper remove rsh ``` |
|Samba로 SMB V1 사용 안 함<br /><sub>(185)</sub> |설명: SMB v1은 잘 알려진 심각한 취약성을 포함 하 고 전송 중인 데이터를 암호화 하지 않습니다. 비즈니스 이유를 재정의 하는 데 사용 해야 하는 경우이 프로토콜의 사용을 보완 하기 위해 다른 완화 방법을 확인 하는 것이 좋습니다.  |Samba가 실행 되 고 있지 않은 경우에는 패키지를 제거 합니다. 그렇지 않으면/etc/samba/smb.conf: min protocol = SMB2의 [global] 섹션에 줄이 있거나 실행 '/opt/microsoft/omsagent/plugin/omsremediate |

> [!NOTE]
> 특정 Azure Policy 게스트 구성 설정의 가용성은 Azure Government 및 기타 국가별 클라우드에서 달라질 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure Policy 및 게스트 구성에 대 한 추가 문서:

- [게스트 구성을 Azure Policy](../concepts/guest-configuration.md)합니다.
- [규정 준수](../concepts/regulatory-compliance.md) 개요
- [Azure Policy 샘플](./index.md)의 다른 예제를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [규정 비준수 리소스를 수정](../how-to/remediate-resources.md)하는 방법을 알아봅니다.
