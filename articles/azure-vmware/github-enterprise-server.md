---
title: Azure VMware 솔루션 사설 클라우드에서 GitHub Enterprise 서버 설정
description: Azure VMware 솔루션 사설 클라우드에서 GitHub Enterprise 서버를 설정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: afce212416c7c12631a7f8d388dc991ed957736f
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2020
ms.locfileid: "91949312"
---
# <a name="set-up-github-enterprise-server-on-your-azure-vmware-solution-private-cloud"></a>Azure VMware 솔루션 사설 클라우드에서 GitHub Enterprise 서버 설정

이 문서에서는 Azure VMware 솔루션 사설 클라우드에서 GitHub Enterprise Server, "온-프레미스" 버전의 [GitHub.com](https://github.com/)을 설정 하는 단계를 안내 합니다. 이 연습에서 다루는 시나리오는 GitHub 작업에서 최대 25 개의 작업을 실행 하는 최대 3000 명의 개발자를 제공할 수 있는 GitHub Enterprise 서버 인스턴스에 대 한 것입니다. 여기에는 GitHub 작업과 같은 *미리 보기* 기능 (작성 시점)의 설정이 포함 됩니다. 특정 요구 사항에 맞게 설정을 사용자 지정 하려면 [VMware에서 GitHub Enterprise Server 설치](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)에 나열 된 요구 사항을 검토 하세요.

## <a name="before-you-begin"></a>시작하기 전에

GitHub Enterprise Server에는 유효한 라이선스 키가 필요 합니다. [평가판 라이선스](https://enterprise.github.com/trial)에 등록할 수 있습니다. 통합을 통해 GitHub Enterprise Server의 기능을 확장 하려는 경우 5 명의 무료 개발자 라이선스를 한정할 수 있습니다. [GitHub의 파트너 프로그램](https://partner.github.com/)을 통해이 라이선스에 적용 합니다.

## <a name="installing-github-enterprise-server-on-vmware"></a>VMware에 GitHub Enterprise Server 설치

VMware ESXi/vSphere 대 한 [GitHub Enterprise Server의 현재 릴리스](https://enterprise.github.com/releases/2.19.0/download) (ova)를 다운로드 하 고 다운로드 한 [ova 템플릿을 배포](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-17BEDA21-43F6-41F4-8FB2-E01D275FE9B4.html) 합니다.

:::image type="content" source="media/github-enterprise-server/github-options.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::  

:::image type="content" source="media/github-enterprise-server/deploy-ova-template.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다." (작업) |
| --- | --- | --- |
| vCPU | 4 | 8 |
| 메모리 | 32GB | 61 GB |
| 연결된 스토리지 | 250GB | 300GB |
| 루트 저장소 | 200GB | 200GB |

그러나 요구 사항이 다를 수 있습니다. [VMware에 GitHub Enterprise Server 설치](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#hardware-considerations)의 하드웨어 고려 사항에 대 한 지침을 참조 하세요. 또한 사용자의 상황에 따라 하드웨어 구성을 사용자 지정 하려면 [VMware에 대 한 CPU 또는 메모리 리소스 추가](https://docs.github.com/en/enterprise/admin/enterprise-management/increasing-cpu-or-memory-resources#adding-cpu-or-memory-resources-for-vmware) 를 참조 하세요.

## <a name="configuring-the-github-enterprise-server-instance"></a>GitHub Enterprise Server 인스턴스 구성

:::image type="content" source="media/github-enterprise-server/install-github-enterprise.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::  

새로 프로 비전 된 VM (가상 머신)이 켜진 후 [브라우저를 통해 구성](https://docs.github.com/en/enterprise/admin/installation/installing-github-enterprise-server-on-vmware#configuring-the-github-enterprise-server-instance)합니다. 라이선스 파일을 업로드 하 고 관리 콘솔 암호를 설정 해야 합니다. 이 암호는 안전한 위치에 기록해 두어야 합니다.

:::image type="content" source="media/github-enterprise-server/ssh-access.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::    

최소한 다음 단계를 수행 하는 것이 좋습니다.

1. [Ssh를 통해 관리 셸에 액세스할](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)수 있도록 공용 ssh 키를 관리 콘솔에 업로드 합니다. 

2. 신뢰할 수 있는 인증 기관에서 서명한 인증서를 사용할 수 있도록 [인스턴스에서 TLS를 구성](https://docs.github.com/en/enterprise/admin/configuration/configuring-tls) 합니다.

:::image type="content" source="media/github-enterprise-server/configuring-your-instance.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

설정을 적용 합니다.  인스턴스를 다시 시작 하는 동안 **GitHub 동작에 대 한 Blob Storage를 구성**하는 다음 단계를 계속 진행할 수 있습니다.

:::image type="content" source="media/github-enterprise-server/create-admin-account.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

인스턴스가 다시 시작 되 면 인스턴스에 새 관리자 계정을 만듭니다. 이 사용자의 암호를 기록해 두어야 합니다.

### <a name="additional-configuration-steps"></a>추가 구성 단계

프로덕션 사용을 위해 인스턴스를 강화 하려면 다음과 같은 선택적 설정 단계를 수행 하는 것이 좋습니다.

1. 보호를 위해 [고가용성](https://help.github.com/enterprise/admin/guides/installation/configuring-github-enterprise-for-high-availability/) 구성:

    - 소프트웨어 충돌 (OS 또는 응용 프로그램 수준)
    - 하드웨어 오류 (저장소, CPU, RAM 등)
    - 가상화 호스트 시스템 오류
    - 논리적으로 또는 물리적으로 손상 되는 네트워크

2. [Configure](https://docs.github.com/en/enterprise/admin/configuration/configuring-backups-on-your-appliance) [백업-유틸리티](https://github.com/github/backup-utils)를 구성 합니다. 재해 복구를 위해 버전이 지정 된 스냅숏이 제공 되며 주 인스턴스와는 별개의 가용성으로 호스팅됩니다.
3. 사이트 간 스크립팅 및 기타 관련 취약성을 완화 하기 위해 유효한 TLS 인증서를 사용 하 여 하위 [도메인 격리를 설정](https://docs.github.com/en/enterprise/admin/configuration/enabling-subdomain-isolation)합니다.

## <a name="configuring-blob-storage-for-github-actions"></a>GitHub 작업을 위한 blob storage 구성

> [!NOTE]
> Github 작업은 [현재 Github Enterprise Server 릴리스 2.22에 대 한 제한 된 베타 버전으로 제공](https://docs.github.com/en/enterprise/admin/github-actions)됩니다.

GitHub Enterprise 서버 (현재 "베타" 기능으로 제공)에서 GitHub 작업을 사용 하려면 외부 blob 저장소가 필요 합니다. 이 외부 blob 저장소는 아티팩트 및 로그를 저장 하는 작업에서 사용 됩니다. GitHub Enterprise Server에 대 한 작업 [은 Azure Blob Storage를 저장소 공급자로 지원](https://docs.github.com/en/enterprise/admin/github-actions/enabling-github-actions-and-configuring-storage#about-external-storage-requirements) 합니다 (기타 일부). 따라서 [저장소 계정 유형](../storage/common/storage-account-overview.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json#types-of-storage-accounts) blobstorage를 사용 하 여 새 Azure storage 계정을 프로 비전 합니다.

:::image type="content" source="media/github-enterprise-server/storage-account.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

새 BlobStorage 리소스가 성공적으로 배포 되 면 액세스 키에서 사용할 수 있는 연결 문자열을 복사 하 여 적어 둡니다. 이 문자열은 곧 필요 합니다.

## <a name="setting-up-the-github-actions-runner"></a>GitHub Actions runner 설정

> [!NOTE]
> Github 작업은 [현재 Github Enterprise Server 릴리스 2.22에 대 한 제한 된 베타 버전으로 제공](https://docs.github.com/en/enterprise/admin/github-actions)됩니다.

이 시점에서 관리자 계정이 생성 된 GitHub Enterprise Server 인스턴스를 실행 해야 합니다. GitHub 작업에서 지 속성에 사용할 외부 blob 저장소도 있어야 합니다.

이제 GitHub 작업을 실행할 위치를 만들어 보겠습니다. 다시 Azure VMware 솔루션을 사용 합니다.

먼저 클러스터에 새 VM을 프로 비전 하겠습니다. [최신 버전의 Ubuntu 서버](http://releases.ubuntu.com/20.04.1/)에서 VM을 기반으로 합니다.

:::image type="content" source="media/github-enterprise-server/provision-new-vm.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

:::image type="content" source="media/github-enterprise-server/provision-new-vm-2.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

VM을 만든 후에는 전원을 켜고 SSH를 통해 연결 합니다.

다음으로 GitHub 작업 워크플로에서 작업을 실행 하는 [Actions runner](https://github.com/actions/runner) 응용 프로그램을 설치 합니다. [릴리스 페이지](https://github.com/actions/runner/releases) 에서 또는 다음 빠른 스크립트를 실행 하 여 Actions runner의 최신 Linux x64 릴리스를 식별 하 고 다운로드 합니다. 이 스크립트를 사용 하려면 사용자의 VM에는 말아 및 [jq](https://stedolan.github.io/jq/) 가 모두 있어야 합니다.

`LATEST\_RELEASE\_ASSET\_URL=$( curl https://api.github.com/repos/actions/runner/releases/latest | \`

`  jq -r '.assets | .[] | select(.name | match("actions-runner-linux-arm64")) | .url' )`

`DOWNLOAD\_URL=$( curl $LATEST\_RELEASE\_ASSET\_URL | \`

`  jq -r '.browser\_download\_url' )`

`curl -OL $DOWNLOAD\_URL`

이제 VM의 로컬에 파일 (arm64-release.tar.gz)이 있어야 합니다 \* . 이 tarball를 로컬로 추출 합니다.

`tar xzf actions-runner-linux-arm64-\*.tar.gz`

이 추출은 및 스크립트를 포함 하 여 일부 파일을 로컬로 압축을 풉니다 `config.sh` `run.sh` . 잠시 후에 다시 돌아올 것입니다.

## <a name="enabling-github-actions"></a>GitHub 작업 사용

> [!NOTE]
> Github 작업은 [현재 Github Enterprise Server 릴리스 2.22에 대 한 제한 된 베타 버전으로 제공](https://docs.github.com/en/enterprise/admin/github-actions)됩니다.

거의 마쳤습니다! GitHub Enterprise 서버 인스턴스에서 GitHub 작업을 구성 하 고 사용 하도록 설정 해 보겠습니다. [SSH를 통해 GitHub Enterprise Server 인스턴스의 관리 셸에 액세스](https://docs.github.com/en/enterprise/admin/configuration/accessing-the-administrative-shell-ssh)한 후 다음 명령을 실행 해야 합니다.

`# set an environment variable containing your Blob storage connection string`

`export CONNECTION\_STRING="<your connection string from the blob storage step>"`

`# configure actions storage`

`ghe-config secrets.actions.storage.blob-provider azure`

`ghe-config secrets.actions.storage.azure.connection-string "$CONNECTION\_STRING"`

`# apply these settings`

`ghe-config-apply`

`# execute a precheck, this install additional software required by Actions on GitHub Enterprise Server`

`ghe-actions-precheck -p azure -cs "$CONNECTION\_STRING"`

`# enable actions, and re-apply the config`

`ghe-config app.actions.enabled true`

`ghe-config-apply`

다음 실행:

`ghe-actions-check -s blob`

"Blob Storage 정상 상태입니다." 라는 출력이 표시 됩니다.

이제 GitHub 작업이 구성 되었으므로 사용자에 대해 사용 하도록 설정 합니다. GitHub Enterprise Server 인스턴스에 관리자 권한으로 로그인 하 고 ![ 로켓 아이콘을 선택 합니다.](media/github-enterprise-server/rocket-icon.png) 페이지의 오른쪽 위 모퉁이에 있습니다. 왼쪽 세로 막대에서 **엔터프라이즈 개요**, **정책**, **작업**을 차례로 선택 하 고 **모든 조직에 대해 작업을 사용 하도록 설정**하는 옵션을 선택 합니다.

그런 다음 **자체 호스팅 러너** 탭에서 runner를 구성 합니다. 드롭다운에서 **새로 추가** 를 선택 하 고 새 **runner** 를 선택 합니다.

다음 페이지에서는 실행할 명령 집합이 제공 됩니다. 예를 들어 다음과 같이 명령을 **복사 하 여** 명령을 복사 하기만 하면 됩니다.

`./config.sh --url https://10.1.1.26/enterprises/octo-org --token AAAAAA5RHF34QLYBDCHWLJC7L73MA`

명령을 복사 `config.sh` 하 여 Actions runner의 세션에 붙여넣습니다 (이전에 만듦).

:::image type="content" source="media/github-enterprise-server/actions-runner.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

Run.sh 명령을 사용 하 여 runner를 *실행* 합니다.

:::image type="content" source="media/github-enterprise-server/run-runner.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

엔터프라이즈의 조직에서이 runner를 사용할 수 있도록 하려면 해당 조직 액세스를 편집 합니다.

:::image type="content" source="media/github-enterprise-server/edit-runner-access.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

여기서는 모든 조직에서 사용할 수 있도록 하지만 조직의 하위 집합 및 특정 리포지토리에 대 한 액세스를 제한할 수도 있습니다.

## <a name="optional-configuring-github-connect"></a>필드 GitHub 연결 구성

이 단계는 선택 사항 이지만 GitHub.com에서 사용할 수 있는 오픈 소스 작업을 사용 하려는 경우에 권장 됩니다. 이렇게 하면 워크플로에서 재사용 가능한 작업을 참조 하 여 다른 사용자의 작업을 빌드할 수 있습니다.

GitHub 연결을 사용 하도록 설정 하려면 [Github connect를 사용 하 여 GitHub.com 작업에 자동 액세스](https://docs.github.com/en/enterprise/admin/github-actions/enabling-automatic-access-to-githubcom-actions-using-github-connect)사용의 단계를 수행 합니다.

GitHub 연결이 사용 하도록 설정 되 면 **GitHub.com에서 작업을 사용할 서버** 를 선택 합니다.

:::image type="content" source="media/github-enterprise-server/enable-using-actions.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

## <a name="setting-up-and-running-your-first-workflow"></a>첫 번째 워크플로 설정 및 실행

이제 작업 및 GitHub 연결이 설정 되었으므로이 모든 작업을 적절 하 게 사용 하도록 하겠습니다. 다음은 뛰어난 [octokit/request-작업](https://github.com/octokit/request-action)을 참조 하는 워크플로 예제 이며 github 작업을 통해 제공 되는 github API를 사용 하 여 상호 작용을 통해 github를 "스크립팅" 할 수 있습니다.

이 기본 워크플로에서는를 사용 하 여 `octokit/request-action` API를 사용 하 여 GitHub에서 문제를 열기만 합니다.

:::image type="content" source="media/github-enterprise-server/workflow-example.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

>[!NOTE]
>GitHub.com는 작업을 호스트 하지만 GitHub Enterprise 서버에서 실행 되는 경우 *자동으로* Github ENTERPRISE server API를 사용 합니다.

GitHub 연결을 사용 하지 않도록 선택한 경우 다음과 같은 대체 워크플로를 사용할 수 있습니다.

:::image type="content" source="media/github-enterprise-server/workflow-example-2.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

인스턴스의 리포지토리로 이동 하 고 위의 워크플로를 다음과 같이 추가 합니다. `.github/workflows/hello-world.yml`

:::image type="content" source="media/github-enterprise-server/workflow-example-3.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

리포지토리의 **작업** 탭에서 워크플로가 실행 될 때까지 기다립니다.

:::image type="content" source="media/github-enterprise-server/executed-example-workflow.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

또한 runner에서 처리 되는 것을 볼 수 있습니다.

:::image type="content" source="media/github-enterprise-server/workflow-processed-by-runner.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다." 라는 새 문제가 리포지토리에 표시 되어야 합니다.

:::image type="content" source="media/github-enterprise-server/example-in-repo.png" alt-text="온-프레미스 또는 클라우드에서 GitHub를 실행 하도록 선택 합니다.":::

축하합니다! Azure VMware 솔루션 사설 클라우드에서 실행 되는 GitHub Enterprise 서버에서 첫 번째 작업 워크플로를 완료 했습니다.

GitHub 작업으로 수행할 수 있는 작업의 화면을 사실일 합니다. 더 많은 작업을 수행 하려면 [GitHub Marketplace](https://github.com/marketplace)에서 작업 목록을 체크 아웃 하거나 고유한 작업을 [만듭니다](https://docs.github.com/en/actions/creating-actions).

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure VMware 솔루션 사설 클라우드 위에 GitHub Enterprise Server (자체 호스팅 GitHub.com)의 새 인스턴스를 설정 합니다. 이 인스턴스에는 GitHub 동작에 대 한 지원이 포함 되며 로그 및 아티팩트의 지 속성에 Azure Blob Storage 사용 됩니다. 이는 현대적인 공동 작업 및 보안 소프트웨어 개발 환경에 적합 한 조합입니다. 친숙 한 설정에서 클라우드 리소스를 활용할 수 있도록 하는 Azure VMware 솔루션의 견고한 기반을 기반으로 합니다.

자세한 내용은 다음 리소스를 참조 하세요.

- [GitHub 작업 시작](https://docs.github.com/en/actions)
- [베타 프로그램 참여](https://resources.github.com/beta-signup/)
- [GitHub Enterprise Server 관리에 대 한 자세한 정보](https://githubtraining.github.io/admin-training/#/00_getting_started)