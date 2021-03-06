---
title: Azure CLI 스크립트 샘플 - 부하가 분산된 가상 머신 확장 집합에 LAMP 스택 배포
description: 사용자 지정 스크립트 확장을 사용하여 Azure의 부하가 분산된 가상 머신 확장 집합에서 LAMP 스택을 배포합니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
ms.author: cynthn
tags: azure-service-management
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.custom: mvc, devx-track-azurecli
ms.date: 04/05/2017
ms.openlocfilehash: e71b2f940c637a8b1375be71f9b6ca95fd76628e
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501868"
---
# <a name="deploy-the-lamp-stack-in-a-load-balanced-virtual-machine-scale-set"></a>부하가 분산된 가상 머신 확장 집합에서 LAMP 스택 배포

이 예제에서는 가상 머신 확장 집합을 만들고 사용자 지정 스크립트를 실행하는 확장을 적용하여 LAMP 스택을 확장 집합의 각 가상 머신에 배포합니다.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>샘플 스크립트

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/build-stack.sh "Create virtual machine scale set with LAMP stack")]

## <a name="connect"></a>연결

이 코드를 사용하여 VM 및 확장 집합에 연결하는 방법을 확인합니다.

[!code-azurecli[main](../../../cli_scripts/virtual-machine/create-scaleset-php-ansible/how-to-access.sh "Access the virtual machine scale set")]

## <a name="clean-up-deployment"></a>배포 정리 

다음 명령을 실행하여 리소스 그룹, 확장 집합, VM 및 모든 관련된 리소스를 제거할 수 있습니다.

```azurecli-interactive 
az group delete -n myResourceGroup
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 리소스 그룹, 가상 머신, 가용성 집합, 부하 분산 장치 및 모든 관련된 리소스를 만듭니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [az group create](/cli/azure/group) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [az vmss create](/cli/azure/vmss) | 가상 머신 확장 집합을 만듭니다. |
| [az network lb rule create](/cli/azure/network/lb/rule) | 부하가 분산된 엔드포인트를 추가합니다. |
| [az vmss extension set](/cli/azure/vmss/extension) | VM 배포에서 사용자 지정 스크립트를 실행하는 확장을 만듭니다. |
| [az vmss update-instances](/cli/azure/vmss) | 확장이 확장 집합에 적용되기 전에 배포된 VM 인스턴스에서 사용자 지정 스크립트를 실행합니다. |
| [az vmss scale](/cli/azure/vmss) | 더 많은 VM 인스턴스를 추가하여 확장 집합의 규모를 확장합니다. VM 인스턴스가 배포되면 사용자 지정 스크립트가 해당 VM 인스턴스에서 실행됩니다. |
| [az network public-ip list](/cli/azure/network/public-ip) | 샘플에서 만든 VM의 IP 주소를 가져옵니다. |
| [az network lb show](/cli/azure/network/lb) | 부하 분산 장치에서 사용하는 프런트 엔드 포트와 백 엔드 포트를 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure CLI에 대한 자세한 내용은 [Azure CLI 설명서](/cli/azure)를 참조하세요.

추가 가상 머신 CLI 스크립트 샘플은 [Azure Linux VM 설명서](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)에서 확인할 수 있습니다.
