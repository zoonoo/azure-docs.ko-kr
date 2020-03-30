---
title: 아즈 스프링 구름
description: Azure CLI를 사용하여 Azure 스프링 클라우드 관리
author: bmitchell287
ms.service: spring-cloud
ms.topic: reference
ms.date: 10/03/2019
ms.author: brendm
ms.openlocfilehash: 33d13d2d4fa9003ef041c4c96be83a69ac595a78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298790"
---
# <a name="az-spring-cloud"></a>아즈 스프링 클라우드

## <a name="manage-azure-spring-cloud-using-the-azure-cli"></a>Azure CLI를 사용하여 Azure 스프링 클라우드 관리

>[!Note]
> Azure Spring Cloud는 현재 미리 보기로 제공되고 있습니다.  이러한 명령은 향후 릴리스에서 변경되거나 제거될 수 있습니다.

| 아즈 스프링 클라우드 |  |
|------|------:|
| [az 스프링 클라우드 생성](#az-spring-cloud-create) | Azure 스프링 클라우드 인스턴스를 만듭니다. |
| [az 스프링 클라우드 삭제](#az-spring-cloud-delete) | Azure 스프링 클라우드 인스턴스를 삭제합니다. |
| [아즈 스프링 클라우드 목록](#az-spring-cloud-list) | 지정된 리소스 그룹에 모든 Azure Spring Cloud 인스턴스를 나열하고 그렇지 않으면 구독 아이디를 나열합니다. |
| [아즈 스프링 클라우드 쇼](#az-spring-cloud-show) | Azure 스프링 클라우드에 대한 세부 정보를 표시합니다. |

| 아즈 스프링 클라우드 응용 프로그램 | Azure 스프링 클라우드에서 앱을 관리하는 명령입니다.  |
| ---- | ----: |
| [az 스프링 클라우드 응용 프로그램 만들기](#az-spring-cloud-app-create) | Azure 스프링 클라우드에서 기본 배포를 사용하여 새 앱을 만듭니다. |
| [az 스프링 클라우드 응용 프로그램 삭제](#az-spring-cloud-app-delete) | Azure 스프링 클라우드에서 앱을 삭제합니다. |
| [az 스프링 클라우드 앱 배포](#az-spring-cloud-app-deploy) | 소스 코드 또는 미리 빌드된 바이너리에서 앱으로 배포하고 관련 구성을 업데이트합니다. |
| [아즈 스프링 클라우드 앱 목록](#az-spring-cloud-app-list) | Azure 스프링 클라우드의 모든 앱을 나열합니다. |
| [az 스프링 클라우드 앱 다시 시작](#az-spring-cloud-app-restart) | 프로덕션 배포 기본값을 사용하여 앱인스턴스를 다시 시작합니다. |
| [az 스프링 클라우드 앱 스케일](#az-spring-cloud-app-scale) | 앱 또는 배포를 수동으로 확장합니다. |
| [az 스프링 클라우드 앱 세트 배포](#az-spring-cloud-app-set-deployment) | 앱의 프로덕션 배포를 설정합니다. |
| [아즈 스프링 클라우드 앱 쇼](#az-spring-cloud-app-show) | Azure 스프링 클라우드에서 앱의 세부 정보를 표시합니다. |
| [az 스프링 클라우드 앱 쇼-배포 로그](#az-spring-cloud-app-show-deploy-log) | 원본에서 최신 배포에 대한 빌드 로그를 표시합니다. 프로덕션 배포에 대한 기본값입니다. |
| [아즈 스프링 클라우드 응용 프로그램 시작](#az-spring-cloud-app-start) | 프로덕션 배포 기본값을 사용하여 앱의 인스턴스를 시작합니다. |
| [아즈 스프링 클라우드 응용 프로그램 중지](#az-spring-cloud-app-stop) | 프로덕션 배포 기본값을 사용하여 앱의 인스턴스를 중지합니다. |
| [아즈 스프링 클라우드 앱 업데이트](#az-spring-cloud-app-update) | 지정된 앱의 구성을 업데이트합니다. |

| az 스프링 클라우드 앱 바인딩 | Azure 데이터 서비스를 사용하여 바인딩을 관리하는 명령입니다.  이러한 설정이 적용되기 전에 앱을 다시 시작해야 합니다. |
| --- | ---: |
| [az 스프링 클라우드 앱 바인딩 목록](#az-spring-cloud-app-binding-list) | 앱의 모든 서비스 바인딩을 나열합니다. |
| [az 스프링 클라우드 앱 바인딩 제거](#az-spring-cloud-app-binding-remove) | 앱에서 서비스 바인딩을 제거합니다. |
| [az 스프링 클라우드 앱 바인딩 쇼](#az-spring-cloud-app-binding-show) | 서비스 바인딩의 세부 정보를 표시합니다. |
| [az 스프링 클라우드 앱 바인딩 코스모스 추가](#az-spring-cloud-app-binding-cosmos-add) | Azure CosmosDB를 앱과 바인딩합니다. |
| [az 스프링 클라우드 앱 바인딩 코스모스 업데이트](#az-spring-cloud-app-binding-cosmos-update) | Azure CosmosDB 서비스 바인딩을 업데이트합니다. |
| [az 스프링 클라우드 응용 프로그램 바인딩 mysql 추가](#az-spring-cloud-app-binding-mysql-add) | MySQL에 대 한 Azure 데이터베이스를 응용 프로그램 바인딩합니다. |
| [az 스프링 클라우드 응용 프로그램 바인딩 mysql 업데이트](#az-spring-cloud-app-binding-mysql-update) | MySQL 서비스 바인딩에 대한 Azure 데이터베이스를 업데이트합니다. |
| [az 스프링 클라우드 앱 바인딩 redis 추가](#az-spring-cloud-app-binding-redis-add) | 앱과 Redis에 대한 Azure 캐시를 바인딩합니다. |
| [az 스프링 클라우드 앱 바인딩 redis 업데이트](#az-spring-cloud-app-binding-redis-update) | Redis 서비스 바인딩에 대한 Azure 캐시를 업데이트합니다. |

| az 스프링 클라우드 앱 배포 | Azure Spring 클라우드에서 앱의 배포 수명 주기를 관리하는 명령입니다. |
| --- | ---: |
| [az 스프링 클라우드 앱 배포 생성](#az-spring-cloud-app-deployment-create) | 앱에 대한 스테이징 배포를 만듭니다. |
| [az 스프링 클라우드 앱 배포 삭제](#az-spring-cloud-app-deployment-delete) | 앱 배포를 삭제합니다. |
| [az 스프링 클라우드 앱 배포 목록](#az-spring-cloud-app-deployment-list) | 앱의 모든 배포를 나열합니다. |
| [az 스프링 클라우드 앱 배포 쇼](#az-spring-cloud-app-deployment-show) | 배포에 대한 세부 정보를 표시합니다. |

| az 스프링 클라우드 구성 서버 | Azure 스프링 클라우드 구성 서버를 관리하는 명령입니다. |
| --- | ---: |
| [az 스프링 클라우드 구성 서버 지우기](#az-spring-cloud-config-server-clear) | 구성 서버의 모든 설정을 지웁니다. |
| [az 스프링 클라우드 구성 서버 세트](#az-spring-cloud-config-server-set) | YAML 파일에서 구성 서버를 정의합니다. |
| [az 스프링 클라우드 구성 서버 쇼](#az-spring-cloud-config-server-show) | 구성 서버 구성을 표시합니다. |
| [az 스프링 클라우드 구성 서버 git 세트](#az-spring-cloud-config-server-git-set) | 구성 서버에 대한 git 속성을 정의합니다.  이전 값을 덮어씁습니다. |
| [az 스프링 클라우드 구성 서버 git 리포지토리 추가](#az-spring-cloud-config-server-git-repo-add) | Config 서버에 새 git 리포지토리 구성을 추가합니다. |
| [az 스프링 클라우드 구성 서버 git 리포지토리 목록](#az-spring-cloud-config-server-git-repo-list) | Config 서버에 대한 모든 git 리포지토리 구성을 나열합니다. |
| [az 스프링 클라우드 구성 서버 git 리포지토리 제거](#az-spring-cloud-config-server-git-repo-remove) | Config 서버에서 지정된 git 리포지토리를 제거합니다. |

| az 스프링 클라우드 테스트 엔드포인트 | Azure 스프링 클라우드에서 끝점 테스트를 관리하는 명령 |
| --- | ---: |
| [az 스프링 클라우드 테스트 엔드포인트 비활성화](#az-spring-cloud-test-endpoint-disable) | 테스트 끝점을 사용하지 않도록 설정합니다. |
| [az 스프링 클라우드 테스트 엔드포인트 사용](#az-spring-cloud-test-endpoint-enable) | 테스트 끝점을 활성화합니다. |
| [az 스프링 클라우드 테스트 엔드포인트 목록](#az-spring-cloud-test-endpoint-list) | 테스트 끝점 키를 나열합니다. |
| [az 스프링 클라우드 테스트 엔드포인트 갱신 키](#az-spring-cloud-test-endpoint-renew-key) | 테스트 끝점 키를 다시 생성합니다. |

## <a name="az-spring-cloud-create"></a>az 스프링 클라우드 생성

Azure 스프링 클라우드에서 기본 배포를 사용하여 새 앱을 만듭니다.

```azurecli
az spring-cloud create  --name -n
                        --resource-group -g
                        --location -l
                        --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 이 Azure 스프링 클라우드 인스턴스의 이름입니다. |
| --resource-group -g | 이 앱의 리소스 그룹을 지정합니다.  다음을 사용하여 기본 그룹 구성`az configure --defaults group=<name>` |

| 선택적 매개 변수 | |
| --- | ---: |
| --location -l | 이 앱의 서버 위치를 지정합니다.  사용 하 여 유효한 위치 찾기`az account list-locations` |
| --no-wait | 장기 실행 작업이 완료되지 않도록 하십시오.

### <a name="examples"></a>예

WestUS에 새 Azure 스프링 클라우드 만들기

```azurecli
az spring-cloud create -n MyService -g MyResourceGroup -l westus
```

## <a name="az-spring-cloud-delete"></a>az 스프링 클라우드 삭제

Azure 스프링 클라우드 인스턴스를 삭제합니다.

```azurecli
az spring cloud --name -n
                --resource-group -g
                --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 삭제할 Azure 스프링 클라우드 인스턴스의 이름입니다. |
| --resource-group -g | Azure 스프링 클라우드가 속한 리소스 그룹의 이름입니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| - 기다릴 필요가 없습니다 | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |

### <a name="example"></a>예제

'MyResourceGroup'에서 'MyService'라는 Azure 스프링 클라우드 인스턴스를 삭제합니다.

```azurecli
az spring-cloud delete -n MyService -g MyResourceGroup
```

## <a name="az-spring-cloud-list"></a>아즈 스프링 클라우드 목록

지정된 리소스 그룹과 연결된 모든 Azure Spring 클라우드 인스턴스를 나열합니다. 리소스 그룹을 지정하지 않으면 구독 아이디를 나열합니다.

```azurecli
az spring-cloud list --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --resource-group -g | 리소스 그룹의 이름입니다. |

## <a name="az-spring-cloud-show"></a>아즈 스프링 클라우드 쇼

지정된 Azure 스프링 클라우드 인스턴스에 대한 세부 정보를 표시합니다.

```azurecli
az spring-cloud show --name -n
                     -- resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | Azure 스프링 클라우드 인스턴스의 이름입니다. |
| --resource-group -g | Azure Spring 클라우드 인스턴스가 속한 리소스 그룹의 이름입니다.

## <a name="az-spring-cloud-app-create"></a>az 스프링 클라우드 응용 프로그램 만들기

Azure 스프링 클라우드에서 새 앱을 만듭니다.

```azurecli
az spring-cloud app create --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --enable-persistent-storage
                            --instance-count
                            --is-public
                            --memory
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --CPU | 인스턴스당 가상 코어 수입니다.  기본값: 1. |
| --영구 스토리지 사용 | 부울 값을 지정합니다.  true이면 기본 경로가 있는 50GB 디스크를 탑재합니다. |
| --instance-count | 인스턴스 수입니다.  기본값: 1. |
| ----공개 | 부울 값을 지정합니다.  true이면 공개 도메인을 할당합니다. |
| --메모리 | 인스턴스당 메모리 수입니다.  기본값: 1. |

### <a name="examples"></a>예

기본 구성을 사용하여 앱을 만듭니다.

```azurecli
az spring-cloud app create -n MyApp -s MyService
```

3개의 인스턴스로 공개적으로 액세스할 수 있는 앱을 만듭니다.  각 인스턴스에는 3GB의 메모리와 2개의 CPU 코어가 있습니다.

```azurecli
az spring-cloud app create -n MyApp -s MyService --is-public true --cpu 2 --memory 3
```

## <a name="az-spring-cloud-app-delete"></a>az 스프링 클라우드 응용 프로그램 삭제

Azure 스프링 클라우드에서 앱을 삭제합니다.

```azurecli
az spring cloud app delete  --name -n
                            --resource-group -g
                            --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-deploy"></a>az 스프링 클라우드 앱 배포

소스 코드 또는 미리 빌드된 바이너리에서 Azure Spring 클라우드에 앱을 배포하고 관련 구성을 업데이트합니다.

```azurecli
az spring cloud app deploy  --name -n
                            --resource-group -g
                            --service -s
                            --cpu
                            --deployment -d
                            --env
                            --instance-count
                            --jar-path
                            --jvm-options
                            --memory
                            --no-wait
                            --runtime-version
                            --target-module
                            --version
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --CPU | 인스턴스당 가상 CPI 코어 수입니다. |
| --배포 -d | 기존 앱 배포의 이름입니다.  지정하지 않으면 프로덕션 배포에 대한 기본값입니다. |
| --env | 'key[=value]' 형식의 공간 구분 환경 변수입니다. |
| --instance-count | 인스턴스 수입니다. |
| --항아리 경로 | 제공된 경우 지정된 경로에서 jar을 배포합니다. 그렇지 않으면 현재 폴더를 타르로 배포합니다. |
| --jvm 옵션 | JVM 옵션이 포함된 문자열입니다.  셸 구문 분석 오류를 방지하려면 ''대신 '='를 사용합니다. 예를 들어, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --메모리 | 인스턴스당 메모리 수입니다. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |
| --런타임 버전 | 앱에 사용된 언어의 런타임 버전입니다.  허용된 `Java_11`값: . `Java_8` |
| --대상 모듈 | 배포할 자식 모듈입니다.  소스 코드에서 여러 jar 패키지를 빌드하는 경우 필요합니다. |
| --version | 배포 버전입니다.  설정되지 않은 경우 변경되지 않습니다. |

### <a name="examples"></a>예

앱에 소스 코드를 배포합니다. 이렇게 하면 현재 디렉터리가 압축되고 Pivotal 빌드 서비스를 사용하여 바이너리를 빌드한 다음 앱에 배포됩니다.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService
```

JVM 옵션 및 환경 변수를 사용하여 미리 빌드된 jar을 앱에 배포합니다.

```azurecli
az spring-cloud app deploy -n MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseG1GC -XX:+UseStringDeduplication" --env foo=bar
```

앱의 특정 배포에 소스 코드를 배포합니다.

```azurecli
az spring-cloud app deploy -n MyApp -s Myspring-cloud -d green-deployment
```

## <a name="az-spring-cloud-app-list"></a>아즈 스프링 클라우드 앱 목록

Azure 스프링 클라우드 인스턴스의 모든 앱을 나열합니다.

```azurecli
az spring-cloud app list --resource-group -g
                         --service -s
```

|필수 매개 변수 | |
| --- | ---: |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-restart"></a>az 스프링 클라우드 앱 다시 시작

앱의 인스턴스를 다시 시작합니다.  프로덕션 배포에 대한 기본값입니다.

```azurecli
az spring-cloud app restart --name -n
                            --resource-group -g
                            --service -s
                            --deployment -d
                            --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포 -d | 앱의 기존 배포 이름입니다.  지정하지 않으면 프로덕션 배포에 대한 기본값입니다. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |

## <a name="az-spring-cloud-app-scale"></a>az 스프링 클라우드 앱 스케일

앱 또는 배포를 수동으로 확장합니다.

```azurecli
az spring-cloud app scale --name -n
                          --resource-group -g
                          --service -s
                          --cpu
                          --deployment -d
                          --instance-count
                          --memory
                          --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --CPU | 앱 인스턴스당 가상 CPU 코어 수입니다. |
| --배포 -d | 앱의 기존 배포 이름입니다.  지정하지 않으면 프로덕션 배포에 대한 기본값입니다. |
| --instance-count | 이 응용 프로그램의 인스턴스 수입니다. |
| --메모리 | 앱 인스턴스당 메모리 수입니다. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |

### <a name="examples"></a>예

앱을 인스턴스당 4개의 CPU 코어와 8GB의 메모리로 확장합니다.

```azurecli
az spring-cloud app scale -n MyApp -s MyService --cpu 3 --memory 8
```

앱 배포를 5개의 인스턴스로 확장합니다.

```azurecli
az spring-cloud app scale -n MyApp -s MyService -d green-deployment --instance-count 5
```

## <a name="az-spring-cloud-app-set-deployment"></a>az 스프링 클라우드 앱 세트 배포

앱의 프로덕션 배포에 대한 구성 옵션을 설정합니다.

```azurecli
az spring-cloud app set-deployment --deployment -d
                                   --name -n
                                   --resource-group -g
                                   --service -s
                                   --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --배포 -d | 앱의 기존 배포 이름입니다. |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |

### <a name="examples"></a>예

앱의 스테이징 배포를 프로덕션으로 바꿉습니다.

```azurecli
az spring-cloud app set-deployment -d green-deployment -n MyApp -s MyService
```

## <a name="az-spring-cloud-app-show"></a>아즈 스프링 클라우드 앱 쇼

Azure 스프링 클라우드에서 앱의 세부 정보를 표시합니다.

```azurecli
az spring-cloud app show --name -n
                         --resource-group -g
                         --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-show-deploy-log"></a>az 스프링 클라우드 앱 쇼-배포 로그

소스 코드에서 마지막 배포의 빌드 로그를 표시합니다.  프로덕션 환경에 대한 기본값입니다.

```azurecli
az spring-cloud app show-deploy-log --name -n
                                    --resource-group -g
                                    --service -s
                                    --deployment -d
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포 -d | 앱의 기존 배포 이름입니다.  프로덕션 환경에 대한 기본값입니다. |

## <a name="az-spring-cloud-app-start"></a>아즈 스프링 클라우드 응용 프로그램 시작

앱의 인스턴스를 시작합니다.  프로덕션 환경에 대한 기본값입니다.

```azurecli
az spring-cloud app start --name -n
                          --resource-group -g
                          --service -s
                          --deployment -d
                          --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포 -d | 앱의 기존 배포 이름입니다.  프로덕션 환경에 대한 기본값입니다. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |

## <a name="az-spring-cloud-app-stop"></a>아즈 스프링 클라우드 응용 프로그램 중지

앱의 인스턴스를 중지합니다.  프로덕션 환경에 대한 기본값입니다.

```azurecli
az spring-cloud app stop --name -n
                         --resource-group -g
                         --service -s
                         --deployment -d
                         --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포 -d | 앱의 기존 배포 이름입니다.  프로덕션 환경에 대한 기본값입니다. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |

## <a name="az-spring-cloud-app-update"></a>아즈 스프링 클라우드 앱 업데이트

앱의 저장된 구성을 업데이트합니다.

```azurecli
az spring-cloud app update --name -n
                           --resource-group -g
                           --service -s
                           --deployment -d
                           --enable-persistent-storage
                           --env
                           --is-public
                           --jvm-options
                           --no-wait
                           --runtime-version
```

| 필수 매개 변수 | |
| --- | ---: |
| --이름 -n | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --배포 -d | 앱의 기존 배포 이름입니다.  프로덕션 환경에 대한 기본값입니다. |
| --영구 스토리지 사용 | Boolean입니다.  true이면 기본 경로가 있는 50GB 디스크를 탑재합니다. |
| --env | 'key[=value]' 형식의 공간 구분 환경 변수입니다. |
| ----공개 | Boolean입니다.  true이면 앱에 공개 도메인을 할당합니다. |
| --jvm 옵션 | JVM 옵션이 포함된 문자열입니다.  셸 구문 분석 오류를 방지하려면 ''대신 '='를 사용합니다. 예를 들어, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |
| --런타임 버전 | 앱에 사용된 언어의 런타임 버전입니다.  허용된 `Java_11`값: . `Java_8` |

### <a name="example"></a>예제

앱에 대한 환경 변수를 추가합니다.

```azurecli
az spring-cloud app update --env foo=bar
```

## <a name="az-spring-cloud-app-binding-list"></a>az 스프링 클라우드 앱 바인딩 목록

앱의 모든 서비스 바인딩을 나열합니다.

```azurecli
az spring-cloud app binding list --app
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-binding-remove"></a>az 스프링 클라우드 앱 바인딩 제거

앱에서 서비스 바인딩을 제거합니다.

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 제거할 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-binding-show"></a>az 스프링 클라우드 앱 바인딩 쇼

서비스 바인딩의 세부 정보를 표시합니다.

```azurecli
az spring-cloud app binding show --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-binding-cosmos-add"></a>az 스프링 클라우드 앱 바인딩 코스모스 추가

Azure 코스모스 DB를 앱과 바인딩합니다.

```azurecli
az spring-cloud app binding cosmos add --api-type
                                       --app
                                       --name -n
                                       --resource-group -g
                                       --resource-id
                                       --service -s
                                       --collection-name
                                       --database-name
                                       --key-space
```

```azurecli
az spring-cloud app binding list --app
                                 --name -n
                                 --resource-group -g
                                 --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --api 형 | 카산드라, 그렘린, 몽고, sql, 테이블 중 하나를 사용하여 API 유형을 지정합니다. |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

|선택적 매개 변수 | |
| --- | ---: |
| --컬렉션 이름 | 컬렉션의 이름입니다.  그렘린을 사용할 때 필요합니다. |
| --데이터베이스 이름 | 데이터베이스의 이름입니다.  몽고, SQL 및 그렘린을 사용할 때 필요합니다. |
| --키 공간 | 카산드라 키 스페이스.  카산드라를 사용할 때 필요합니다. |

## <a name="az-spring-cloud-app-binding-cosmos-update"></a>az 스프링 클라우드 앱 바인딩 코스모스 업데이트

```azurecli
az spring-cloud app binding cosmos update --app
                                          --name -n
                                          --resource-group -g
                                          --service -s
                                          --collection-name
                                          --database-name
                                          --key-space
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

|선택적 매개 변수 | |
| --- | ---: |
| --컬렉션 이름 | 컬렉션의 이름입니다.  그렘린을 사용할 때 필요합니다. |
| --데이터베이스 이름 | 데이터베이스의 이름입니다.  몽고, SQL 및 그렘린을 사용할 때 필요합니다. |
| --키 공간 | 카산드라 키 스페이스.  카산드라를 사용할 때 필요합니다. |

## <a name="az-spring-cloud-app-binding-mysql-add"></a>az 스프링 클라우드 응용 프로그램 바인딩 mysql 추가

```azurecli
az spring-cloud app binding mysql add --app
                                      --database-name
                                      --key
                                      --name -n
                                      --resource-group -g
                                      --service -s
                                      --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --데이터베이스 이름 | 데이터베이스의 이름입니다. |
| --key | 서비스의 API 키입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --리소스 ID | 바인딩할 서비스의 Azure 리소스 ID입니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |
| --username | 데이터베이스 액세스에 대한 사용자 이름입니다. |

## <a name="az-spring-cloud-app-binding-mysql-update"></a>az 스프링 클라우드 응용 프로그램 바인딩 mysql 업데이트

MySQL용 Azure 데이터베이스에 서비스 바인딩 연결을 업데이트합니다.

```azurecli
az spring-cloud app binding mysql update --add
                                         --name -n
                                         --resource-group -g
                                         --service -s
                                         --database-name
                                         --key
                                         --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --데이터베이스 이름 | 데이터베이스의 이름입니다. |
| --key | 서비스의 API 키입니다. |
| --username | 데이터베이스 액세스에 대한 사용자 이름입니다. |

## <a name="az-spring-cloud-app-binding-redis-add"></a>az 스프링 클라우드 앱 바인딩 redis 추가

앱과 Redis에 대한 Azure 캐시를 바인딩합니다.

```azurecli
az spring-cloud app binding redis add --app
                                      --name -n
                                      --resource-group -g
                                      --resource-id
                                      --service -s
                                      --disable-ssl
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --리소스 ID | 바인딩할 서비스의 Azure 리소스 ID입니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --비활성화 ssl | TLS를 사용하지 않도록 설정합니다. |

## <a name="az-spring-cloud-app-binding-redis-update"></a>az 스프링 클라우드 앱 바인딩 redis 업데이트

Redis에 대한 Azure 캐시에 대한 서비스 바인딩을 업데이트합니다.

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --비활성화 ssl | TLS를 사용하지 않도록 설정합니다. |

## <a name="az-spring-cloud-app-deployment-create"></a>az 스프링 클라우드 앱 배포 생성

앱에 대한 스테이징 배포를 만듭니다.

코드를 배포하거나 기존 배포에 설정을 업데이트하려면 `az spring-cloud app deploy --deployment <staging-deployment>` 'az 스프링 클라우드 앱 <staging deployment>업데이트 --배포.배포.

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 서비스 바인딩의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --CPU | 인스턴스당 가상 CPU 코어 수입니다.  기본값: 1 |
| --env | 'key[=value]' 형식의 공간 구분 환경 변수입니다. |
| --instance-count | 인스턴스 수입니다. 기본값: 1. |
| --항아리 경로 | 제공된 경우 항아리를 배포합니다.  그렇지 않으면 현재 폴더를 타르로 배포합니다. |
| --jvm 옵션 | JVM 옵션이 포함된 문자열입니다.  셸 구문 분석 오류를 방지하려면 ''대신 '='를 사용합니다. 예를 들어, `--jvm-options='-Xms1024m -Xmx2048m`. |
| --메모리 | 인스턴스당 메모리 수입니다. |
| --no-wait | 장기 실행 작업이 완료될 때까지 기다리지 마십시오. |
| --런타임 버전 | 앱에 사용된 언어의 런타임 버전입니다.  허용된 `Java_11`값: . `Java_8` |
| --건너뛰기-복제-설정 | 현재 프로덕션 배포 설정을 복제하여 스테이징 배포를 만듭니다. |
| --대상 모듈 | 배포할 자식 모듈입니다.  소스 코드에서 여러 jar 패키지를 빌드하는 경우 필요합니다. |
| --version | 배포 버전입니다.  설정되지 않은 경우 변경되지 않습니다. |

### <a name="examples"></a>예

앱의 새 배포에 소스 코드를 배포합니다.  이렇게 하면 현재 디렉터리가 압축되고 Pivotal 빌드 시스템을 사용하여 바이너리를 빌드한 다음 배포됩니다.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService
```

JVM 옵션 및 환경 변수가 있는 앱에 미리 빌드된 jar을 배포합니다.

```azurecli
az spring-cloud app deployment create -n green-deployment --app MyApp -s MyService --jar-path app.jar --jvm-options="-XX:+UseStringDeDuplication" --env foo=bar
```

## <a name="az-spring-cloud-app-deployment-delete"></a>az 스프링 클라우드 앱 배포 삭제

앱 배포를 삭제합니다.

```azurecli
az spring-cloud app deployment delete --app
                                      --name -n
                                      --resource-group -g
                                      --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 배포의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-deployment-list"></a>az 스프링 클라우드 앱 배포 목록

앱의 모든 배포를 나열합니다.

```azurecli
az spring-cloud app deployment list --app
                                    --resource-group -g
                                    --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-app-deployment-show"></a>az 스프링 클라우드 앱 배포 쇼

배포에 대한 세부 정보를 표시합니다.

```azurecli
az spring-cloud app deployment show --app
                                    --name -n
                                    --resource-group -g
                                    --service -s
```

| 필수 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --name | 배포의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --서비스 -s | Azure 스프링 클라우드의 이름입니다.  을 사용하여 `az configure --defaults spring-cloud=<name>`기본 서비스를 구성할 수 있습니다. |

## <a name="az-spring-cloud-config-server-clear"></a>az 스프링 클라우드 구성 서버 지우기

구성 서버의 모든 구성 설정을 지웁니다.

```azurecli
az spring-cloud config-server clear --name
                                        --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-config-server-set"></a>az 스프링 클라우드 구성 서버 세트

YAML 파일을 사용하여 구성 서버에서 구성 설정을 설정합니다.

```azurecli
az spring-cloud config-server set --config-file
                                  --name -n
                                  --resource-group -g
                                  --no-wait
```

| 필수 매개 변수 | |
| --- | ---: |
| --구성 파일 | Config 서버의 구성에 대한 YAML 매니페스트에 대한 파일 경로입니다. |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --no-wait | 장기 실행 작업이 완료되지 않도록 하십시오.

## <a name="az-spring-cloud-config-server-show"></a>az 스프링 클라우드 구성 서버 쇼

구성 서버 설정을 표시합니다.

```azurecli
az spring-cloud config-server show --name -n
                                   --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-config-server-git-set"></a>az 스프링 클라우드 구성 서버 git 세트

구성 서버에 대한 git 속성을 설정합니다.  이렇게 하면 모든 기존 git 속성을 덮어씁니다.

```azurecli
az spring-cloud config-server git set --name -n
                                      --resource-group g
                                      --uri
                                      --defer
                                      --host-key
                                      --host-key-algorithm
                                      --label
                                      --password
                                      --private-key
                                      --search-paths
                                      --strict-host-key-checking
                                      --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --uri | 추가된 구성의 URI입니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --연기 | Azure로 보내는 대신 개체를 로컬 캐시에 일시적으로 저장합니다.  보기 `az cache` / 지우기 위해 사용합니다. |
| --호스트 키 | 추가된 구성에 대한 호스트 키입니다. |
| --호스트 키 알고리즘 | 추가된 구성에 대한 호스트 키 알고리즘입니다. |
| --라벨 | 추가된 구성의 레이블입니다. |
| --password | 추가된 구성의 암호입니다. |
| --개인 키 | 추가된 구성의 개인 키입니다. |
| --검색 경로 | 추가된 구성의 검색 경로입니다.  여러 경로에 쉼표 구분기호를 사용합니다. |
| --엄격한 호스트 키 확인 | 추가된 구성을 엄격하게 호스트 키 검사할 수 있습니다. |
| --username | 추가된 구성의 사용자 이름입니다. |

## <a name="az-spring-cloud-config-server-git-repo-add"></a>az 스프링 클라우드 구성 서버 git 리포지토리 추가

```azurecli
az spring-cloud config-server git repo add --name -n
                                           --repo-name
                                           --resource-group -g
                                           --uri
                                           --defer
                                           --host-key
                                           --host-key-algorithm
                                           --label
                                           --password
                                           --pattern
                                           --private-key
                                           --search-paths
                                           --strict-host-key-checking
                                           --username
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --리포지토리 이름 | 리포지토리의 URI입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --uri | 추가된 구성의 URI입니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --연기 | Azure로 보내는 대신 개체를 로컬 캐시에 일시적으로 저장합니다.  보기 `az cache` / 지우기 위해 사용합니다. |
| --호스트 키 | 추가된 구성에 대한 호스트 키입니다. |
| --호스트 키 알고리즘 | 추가된 구성에 대한 호스트 키 알고리즘입니다. |
| --라벨 | 추가된 구성의 레이블입니다. |
| --password | 추가된 구성의 암호입니다. |
| --패턴 | 리포지토리에 대한 패턴입니다.  여러 경로에 쉼표 구분기호를 사용합니다.|
| --개인 키 | 추가된 구성의 개인 키입니다. |
| --검색 경로 | 추가된 구성의 검색 경로입니다.  여러 경로에 쉼표 구분기호를 사용합니다. |
| --엄격한 호스트 키 확인 | 추가된 구성을 엄격하게 호스트 키 검사할 수 있습니다. |
| --username | 추가된 구성의 사용자 이름입니다. |

## <a name="az-spring-cloud-config-server-git-repo-list"></a>az 스프링 클라우드 구성-서버 git 리포지토리 목록

Config 서버에 정의된 모든 git 리포지토리 나열

```azurecli
az spring-cloud config-server git repo list --name -n
                                       --resource-group -g
                                       --defer
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --연기 | Azure로 보내는 대신 개체를 로컬 캐시에 일시적으로 저장합니다.  보기 `az cache` / 지우기 위해 사용합니다. |

## <a name="az-spring-cloud-config-server-git-repo-remove"></a>az 스프링 클라우드 구성-서버 git 리포지토리 제거

Config 서버에서 기존 git 리포지토리 구성을 제거합니다.

```azurecli
az spring-cloud config-server git repo remove --name -n
                                         --repo-name
                                         --resource-group -g
                                         --defer
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --리포지토리 이름 | 리포지토리의 URI입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --연기 | Azure로 보내는 대신 개체를 로컬 캐시에 일시적으로 저장합니다.  보기 `az cache` / 지우기 위해 사용합니다. |

## <a name="az-spring-cloud-test-endpoint-disable"></a>az 스프링 클라우드 테스트 엔드포인트 비활성화

Azure 스프링 클라우드의 테스트 끝점을 사용하지 않도록 설정

```azurecli
az spring-cloud test-endpoint disable --name -n
                                      --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-test-endpoint-enable"></a>az 스프링 클라우드 테스트 엔드포인트 사용

Azure 스프링 클라우드에 대한 테스트 끝점을 활성화합니다. 

```azurecli
az spring-cloud test-endpoint enable --name -n
                                     --resource-group -g
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

## <a name="az-spring-cloud-test-endpoint-list"></a>az 스프링 클라우드 테스트 엔드포인트 목록 

Azure 스프링 클라우드에 사용할 수 있는 테스트 끝점 키를 나열합니다.

```azurecli
az spring-cloud test-endpoint list --name -n
                                   --resource-group -g
                                   --app
                                   --deployment -d
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |

| 선택적 매개 변수 | |
| --- | ---: |
| --앱 | 응용 프로그램의 이름입니다. |
| --배포 -d | 앱의 기존 배포 이름입니다.  지정되지 않은 경우 프로덕션기본값입니다. |

## <a name="az-spring-cloud-test-endpoint-renew-key"></a>az 스프링 클라우드 테스트 엔드포인트 갱신 키

Azure 스프링 클라우드에 대한 테스트 끝점 키를 다시 생성합니다.

```azurecli
az spring-cloud test-endpoint renew-key --name -n
                                        --resource-group -g
                                        --type
```

| 필수 매개 변수 | |
| --- | ---: |
| --name | Azure 스프링 클라우드의 이름입니다. |
| --resource-group -g | 리소스 그룹의 이름입니다.  `az configure --defaults group=<name>`을 사용하여 기본 그룹을 구성할 수 있습니다. |
| --유형 | 테스트 끝점 키의 유형입니다.  허용된 값: 기본 값, 보조 값입니다. |
