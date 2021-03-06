<properties 
    pageTitle="서비스 버스 배달 못 한 편지 큐 | Microsoft Azure" 
    description="Azure 서비스 버스 배달 못 한 편지 큐의 개요" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="06/21/2016"
    ms.author="clemensv;sethm"/>

# 서비스 버스 배달 못 한 편지 큐의 개요

서비스 버스 큐 및 토픽 구독은 *배달 못 한 편지 큐*(DLQ)라고 하는 보조 하위 큐를 제공합니다. 배달 못 한 편지 큐는 명시적으로 만들 필요가 없으며 주 엔터티와 독립적으로 삭제하거나 관리할 수 없습니다.

배달 못 한 편지 큐의 목적은 수신자에게 배달할 수 없는 메시지 또는 단순히 처리하지 못한 메시지를 보관하는 것입니다. 그런 다음 DLQ에서 메시지를 제거하여 검사할 수 있습니다. 응용 프로그램에서 운영자의 도움을 받아 문제를 수정한 후 메시지를 다시 전송하고, 오류가 발생했다는 사실을 기록하고, 수정 작업을 수행할 수 있습니다.

API 및 프로토콜의 관점에서, 부모 엔터티의 배달 못 한 편지 제스처를 통해 메시지를 제출할 수 있다는 점만 제외하면 DLQ는 다른 큐와 거의 비슷합니다. 뿐만 아니라 활성 시간이 관찰되지 않으며, DLQ의 메시지를 배달 못 한 편지로 처리할 수 없습니다. 배달 못 한 편지 큐는 배달 보기-잠금 및 트랜잭션 작업을 완벽하게 지원합니다.

DLQ는 자동으로 정리되지 않습니다. 사용자가 DLQ에서 명시적으로 메시지를 검색하고 배달 못 한 메시지에서 [Complete()](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.completeasync.aspx)를 호출할 때까지 메시지가 DLQ에 남아 있습니다.

## DLQ로 메시지 이동

서비스 버스의 여러 활동에 의해 메시징 엔진 자체 내에서 DLQ로 메시지가 푸시됩니다. 또한 응용 프로그램이 명시적으로 메시지를 DLQ로 푸시할 수 있습니다.

broker에 의해 메시지가 이동되면 broker가 메시지의 [DeadLetter](https://msdn.microsoft.com/library/azure/hh291941.aspx) 메서드 내부 버전을 호출하므로 해당 메시지에 두 속성 `DeadLetterReason` 및 `DeadLetterErrorDescription` 속성이 추가됩니다.

응용 프로그램에서 `DeadLetterReason` 속성에 대한 고유의 코드를 정의할 수는 있지만, 시스템에서 다음 값을 설정합니다.

| 조건 | DeadLetterReason | DeadLetterErrorDescription |
|---------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------------------------------------------------------------------------------|
| 항상 | HeaderSizeExceeded | 이 스트림에 할당된 크기를 초과합니다. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing and SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions | exception.GetType().Name | exception.Message |
| EnableDeadLetteringOnMessageExpiration | TTLExpiredException | 메시지가 만료되어 배달 못 한 편지로 처리되었습니다. |
| SubscriptionDescription.RequiresSession | 세션 ID가 null입니다. | 세션이 활성화된 엔터티가 세션 식별자가 null인 메시지를 허용하지 않습니다. |
| !dead letter queue | MaxTransferHopCountExceeded | Null |
| 응용 프로그램에서 명시적으로 배달 못 한 편지로 처리 | 응용 프로그램에서 지정 | 응용 프로그램에서 지정 |

## Exceeding MaxDeliveryCount

큐와 구독에는 각각 [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) 및 [SubscriptionDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount.aspx) 속성이 있으며, 기본값은 10입니다. 메시지가 잠금([ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)) 모드에서 전달되었지만 명시적으로 중단되었거나 잠금이 만료되면 메시지의 [BrokeredMessage.DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx)가 증가합니다. [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx)가 [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx)를 초과하면 메시지가 DLQ로 이동되고, `MaxDeliveryCountExceeded` 이유 코드를 지정합니다.

이 동작을 비활성화할 수는 없지만 [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx)를 매우 큰 수로 설정할 수 있습니다.

## TimeToLive 초과

[QueueDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration.aspx) 또는 [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonmessageexpiration.aspx) 속성을 **true**로 설정하면(기본값은 **false**) 만료되는 모든 메시지가 DLQ로 이동되고, `TTLExpiredException` 이유 코드를 지정합니다.

만료된 메시지는 삭제되므로 기본 큐 또는 구독에서 풀링하는 활성 수신자가 하나 이상 있으면 메시지가 DLQ로 이동됩니다. 이 동작은 의도된 것입니다.

## 구독 규칙을 처리하는 동안 오류 발생

구독에 대해 [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonfilterevaluationexceptions.aspx) 속성이 활성화되면 구독의 SQL 필터 규칙이 실행되는 동안 발생하는 모든 오류가 오류 메시지와 함께 DLQ에 캡처됩니다.

## 응용 프로그램 수준에서 배달 못 한 편지 처리

시스템에서 제공하는 배달 못 한 편지 처리 기능 외에도 응용 프로그램에서 DLQ를 사용하여 허용할 수 없는 메시지를 명시적으로 거부할 수 있습니다. 여기에는 시스템 문제로 인해 올바르게 처리할 수 없는 메시지, 잘못된 페이로드가 포함된 메시지 또는 메시지 수준 보안 체계가 사용될 때 실패한 메시지가 포함될 수 있습니다.

## 예

다음은 메시지 수신자를 만드는 코드 조각입니다. 기본 큐의 수신 루프에서, 이 코드는 [Receive(TimeSpan.Zero)](https://msdn.microsoft.com/library/azure/dn130350.aspx)가 포함된 메시지를 검색합니다. 이 속성은 broker에 즉시 사용 가능한 메시지를 즉시 반환하도록 또는 결과 없이 반환하도록 요청합니다. 이 코드는 메시지를 받으면 그 즉시 메시지를 중단하며, 이로 인해 `DeliveryCount`가 증가합니다. 시스템에서 메시지를 DLQ로 이동하면 [ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130350.aspx)에서 **null**을 반환하기 때문에 기본 큐가 비어 있고 루프가 종료됩니다.

```
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## 다음 단계

서비스 버스 큐에 대한 자세한 내용은 다음 문서를 참조하세요.

- [서비스 버스 큐 시작](service-bus-dotnet-get-started-with-queues.md)
- [Azure 큐와 서비스 버스 큐 비교](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

<!---HONumber=AcomDC_0622_2016-->