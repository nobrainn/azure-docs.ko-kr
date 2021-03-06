---
title: Node.js에서 azure/service-bus 큐를 사용하는 방법
description: 새 @azure/service-bus 패키지를 사용하여 Service Bus 큐와 메시지를 주고 받는 Nodejs 프로그램을 작성하는 방법에 대해 알아봅니다.
author: spelluru
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.custom: devx-track-javascript
ms.openlocfilehash: 63b8c33b61d6bff28eca98929e344df7ea54e779
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/30/2020
ms.locfileid: "87430673"
---
# <a name="quickstart-how-to-use-service-bus-queues-with-nodejs-and-the-azureservice-bus-package"></a>빠른 시작: Node.js 앱 및 azure/service-bus 패키지에서 Service Bus 큐를 사용하는 방법
이 자습서에서는 새 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 패키지를 사용하여 Service Bus 큐와 메시지를 주고 받는 Nodejs 프로그램을 작성하는 방법에 대해 알아봅니다. 이 패키지는 더 빠른 [AMQP 1.0 프로토콜](service-bus-amqp-overview.md)을 사용하는 반면, 이전 [azure-sb](https://www.npmjs.com/package/azure-sb) 패키지는 [Service Bus REST 런타임 API](/rest/api/servicebus/service-bus-runtime-rest)를 사용했습니다. 샘플은 JavaScript로 작성되었습니다.

## <a name="prerequisites"></a>사전 요구 사항
- Azure 구독 이 자습서를 완료하려면 Azure 계정이 필요합니다. [MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
- 작업할 큐가 없는 경우 [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 큐를 만듭니다. Service Bus 인스턴스에 대한 연결 문자열 및 자신이 만든 큐의 이름을 적어둡니다. 샘플에서 사용할 것입니다.

> [!NOTE]
> - 이 자습서에서는 [Nodejs](https://nodejs.org/)를 사용하여 복사하고 실행할 수 있는 샘플을 사용합니다. Node.js 애플리케이션을 만드는 방법에 대한 지침은 [Node.js 애플리케이션을 만들어 Azure 웹 사이트에 배포](../app-service/app-service-web-get-started-nodejs.md) 또는 Windows PowerShell을 사용하는 [Node.js 클라우드 서비스](../cloud-services/cloud-services-nodejs-develop-deploy-app.md)를 참조하세요.
> - 새 [@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus) 패키지는 아직 큐 만들기를 지원하지 않습니다. 프로그래밍 방식으로 큐를 만들려면 [@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus) 패키지를 사용하세요.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>NPM(Node Package Manager)을 사용하여 패키지 설치
Service Bus에 대한 npm 패키지를 설치하려면 경로에 `npm`이 있는 명령 프롬프트를 열고, 샘플을 저장하고 이 명령을 실행할 폴더로 디렉터리를 변경합니다.

```bash
npm install @azure/service-bus
```

## <a name="send-messages-to-a-queue"></a>큐에 메시지 보내기
Service Bus 큐와의 상호 작용은 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 클래스를 인스턴스화하여 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 클래스를 인스턴스화하는 데 사용하는 것부터 시작합니다. 큐 클라이언트가 생겼으면 발신자를 만들고, 발신자에서 [send](https://docs.microsoft.com/javascript/api/%40azure/service-bus/sender#send-sendablemessageinfo-) 또는 [sendBatch](https://docs.microsoft.com/javascript/api/@azure/service-bus/sender#sendbatch-sendablemessageinfo---) 메서드를 사용하여 메시지를 보낼 수 있습니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. `send.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다. 이 코드는 큐에 10개 메시지를 보냅니다.

    ```javascript
    const { ServiceBusClient } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const sender = queueClient.createSender();
      
      try {
        for (let i = 0; i < 10; i++) {
          const message= {
            body: `Hello world! ${i}`,
            label: `test`,
            userProperties: {
                myCustomPropertyName: `my custom property value ${i}`
           }
          };
          console.log(`Sending message: ${message.body}`);
          await sender.send(message);
        }
        
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에 있는 연결 문자열 및 큐 이름을 입력합니다.
4. 그런 다음, 명령 프롬프트에서 `node send.js` 명령을 실행하여 이 파일을 실행합니다.

축하합니다! 방금 Service Bus 큐에 메시지를 보냈습니다.

메시지에는 메시지를 보낼 때 설정할 수 있는 `label` 및 `messageId` 같은 몇 가지 표준 속성이 있습니다. 사용자 지정 속성을 설정하려면 사용자 지정 데이터의 키-값 쌍을 보관할 수 있는 json 개체인 `userProperties`를 사용합니다.

Service Bus 큐는 [표준 계층](service-bus-premium-messaging.md)에서 256KB의 최대 메시지 크기를 [프리미엄 계층](service-bus-premium-messaging.md)에서 1MB를 지원합니다. 한 큐에 저장되는 메시지 수에는 제한이 없지만 한 큐에 저장되는 총 메시지 크기는 제한됩니다. 이 큐 크기는 생성 시 정의되며 상한이 5GB입니다. 할당량에 대한 자세한 내용은 [Service Bus 할당량](service-bus-quotas.md)을 참조하세요.

## <a name="receive-messages-from-a-queue"></a>큐에서 메시지 받기
Service Bus 큐와의 상호 작용은 [ServiceBusClient](https://docs.microsoft.com/javascript/api/@azure/service-bus/servicebusclient) 클래스를 인스턴스화하여 [QueueClient](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient) 클래스를 인스턴스화하는 데 사용하는 것부터 시작합니다. 큐 클라이언트가 생겼으면 수신기를 만들고, 이 수신기에서 [receiveMessages](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#receivemessages-number--undefined---number-) 또는 [registerMessageHandler](https://docs.microsoft.com/javascript/api/%40azure/service-bus/receiver#registermessagehandler-onmessage--onerror--messagehandleroptions-) 메서드를 사용하여 메시지를 받을 수 있습니다.

1. 선호하는 편집기(예: [Visual Studio Code](https://code.visualstudio.com/))를 엽니다.
2. `recieve.js`라는 파일을 만들고 아래 코드를 이 파일에 붙여넣습니다. 이 코드는 큐에서 10개 메시지를 수신하려고 시도합니다. 수신하는 실제 메시지 수는 큐의 메시지 수와 네트워크 대기 시간에 따라 달라집니다.

    ```javascript
    const { ServiceBusClient, ReceiveMode } = require("@azure/service-bus"); 
    
    // Define connection string and related Service Bus entity names here
    const connectionString = "";
    const queueName = ""; 
    
    async function main(){
      const sbClient = ServiceBusClient.createFromConnectionString(connectionString); 
      const queueClient = sbClient.createQueueClient(queueName);
      const receiver = queueClient.createReceiver(ReceiveMode.receiveAndDelete);
      try {
        const messages = await receiver.receiveMessages(10)
        console.log("Received messages:");
        console.log(messages.map(message => message.body));
         
        await queueClient.close();
      } finally {
        await sbClient.close();
      }
    }
    
    main().catch((err) => {
      console.log("Error occurred: ", err);
    });
    ```
3. 위의 코드에 있는 연결 문자열 및 큐 이름을 입력합니다.
4. 그런 다음, 명령 프롬프트에서 `node receiveMessages.js` 명령을 실행하여 이 파일을 실행합니다.

축하합니다! 방금 Service Bus 큐에서 메시지를 받았습니다.

[createReceiver](https://docs.microsoft.com/javascript/api/%40azure/service-bus/queueclient#createreceiver-receivemode-) 메서드는 값이 [ReceiveAndDelete](message-transfers-locks-settlement.md#settling-receive-operations) 및 [PeekLock](message-transfers-locks-settlement.md#settling-receive-operations)인 열거형 `ReceiveMode`를 사용합니다. 메시지에서 `complete()`, `abandon()`, `defer()` 또는 `deadletter()` 메서드를 사용하여 `PeekLock` 모드를 사용하는 경우 [메시지를 확인](message-transfers-locks-settlement.md#settling-receive-operations)해야 합니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계
자세한 내용은 다음 리소스를 참조하세요.
- [큐, 토픽 및 구독](service-bus-queues-topics-subscriptions.md)
- GitHub의 다른 [Service Bus용 Nodejs 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/servicebus/service-bus/samples/javascript) 확인
- [Node.js 개발자 센터](https://azure.microsoft.com/develop/nodejs/)

