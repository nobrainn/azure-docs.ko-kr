---
title: Azure Cosmos DB SQL(Core) API용 Azure CLI 샘플
description: Azure Cosmos DB SQL(Core) API용 Azure CLI 샘플
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 06/03/2020
ms.author: mjbrown
ms.openlocfilehash: 39550c3da7a5ed129e26dec3d33f19c65d76cdfa
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2020
ms.locfileid: "85560530"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-core-sql-api"></a>Azure Cosmos DB SQL(Core) API용 Azure CLI 샘플

다음 표는 Azure Cosmos DB SQL(Core) API의 Azure CLI 샘플 스크립트에 대한 링크를 포함합니다. 모든 Azure Cosmos DB CLI 명령에 대한 참조 페이지는 [Azure CLI 참조](/cli/azure/cosmosdb)에서 제공됩니다. 다른 Azure Cosmos DB API에 대한 Azure CLI 샘플은 [Cassandra API](cli-samples-cassandra.md), [MongoDB API](cli-samples-mongodb.md), [Gremlin API](cli-samples-gremlin.md) 및 [Table API](cli-samples-table.md)를 참조하세요. 모든 Azure Cosmos DB CLI 스크립트 샘플은 [Azure Cosmos DB CLI GitHub 리포지토리](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)에서 확인할 수 있습니다.

|Task | Description |
|---|---|
| [Azure Cosmos 계정, 데이터베이스 및 컨테이너 만들기](scripts/cli/sql/create.md?toc=%2fcli%2fazure%2ftoc.json)| SQL(Core) API에 대한 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 만듭니다. |
| [처리량 변경](scripts/cli/sql/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | 데이터베이스 및 컨테이너에서 RU/s를 업데이트합니다.|
| [영역 추가 또는 장애 조치(failover)](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | 영역을 추가하고, 장애 조치(failover) 우선순위를 변경하고, 수동 장애 조치(failover)를 트리거합니다.|
| [계정 키 및 연결 문자열](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | 계정 키, 읽기 전용 키를 나열하고, 키를 다시 생성하고, 연결 문자열을 나열합니다.|
| [IP 방화벽으로 보안](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| IP 방화벽이 구성된 Cosmos 계정을 만듭니다.|
| [서비스 엔드포인트로 새 계정 보호](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Cosmos 계정을 만들고 서비스 엔드포인트로 보호합니다.|
| [서비스 엔드포인트로 기존 계정 보호](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| 서브넷이 최종적으로 구성될 때 서비스 엔드포인트로 보호할 Cosmos 계정을 업데이트합니다.|
| [삭제에서 리소스 잠그기](scripts/cli/sql/lock.md?toc=%2fcli%2fazure%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다.|
|||
