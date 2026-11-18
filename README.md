# CST8915 Lab 7 - Introduction to Kubernetes Basics

**Course:** CST8915 Full-stack Cloud-native Development

**Semester:** Fall 2026

## Demo Video

YouTube video link: https://youtu.be/503AWGCT7Os

## 1. Is RabbitMQ a stateless or stateful application?

RabbitMQ is a stateful application because it stores 
information such as messages and queues. It needs to 
keep this information even when the application is 
restarted. In the Algonquin Pet Store application, 
RabbitMQ stores customer orders before they are processed.

## 2. What are the implications of running RabbitMQ 
without persistent storage?

Running RabbitMQ without persistent storage can cause 
data loss. If the Pod is deleted and replaced, the 
information stored inside it can be lost.

In a real online store, this could lead to lost customer 
orders, delivery problems and customer dissatisfaction. 
This is why persistent storage is important for stateful 
applications.

## 3. What happens when the RabbitMQ Pod is deleted 
or restarted?

During my lab, I placed three orders using the Algonquin
Pet Store application. I checked the RabbitMQ dashboard 
and confirmed that three messages were available in the 
order_queue.

After deleting the RabbitMQ Pod, Kubernetes automatically 
created a new Pod. However, when I refreshed the RabbitMQ 
dashboard, the queue and all three messages were gone.

I also used the kubectl get pvc command and confirmed 
that no Persistent Volume Claims were available in the 
default namespace.

This shows that Kubernetes can recover the application 
but cannot automatically recover its lost data. A restart 
can also cause data loss depending on the storage and 
RabbitMQ configuration.

## 4. What are the potential solutions to this problem?

The first solution is to use a Persistent Volume (PV) 
and a Persistent Volume Claim (PVC). This allows RabbitMQ 
to store its data outside the temporary storage of the Pod.

Another solution is to use a StatefulSet instead of a 
regular Deployment. A StatefulSet combined with persistent 
storage provides a stable identity and allows RabbitMQ 
to reconnect to its existing data when a Pod is replaced.

It is also important to use durable queues and persistent 
messages in RabbitMQ. These configurations help protect 
messages during a broker restart when the storage is 
preserved.

## 5. Does using Azure Service Bus solve the issues 
identified with the RabbitMQ configuration in this lab?

Yes, Azure Service Bus can solve the main problem 
identified in this lab because it is a managed messaging 
service that stores messages independently of Kubernetes 
Pods.

If a Kubernetes Pod is deleted or restarted, messages 
already stored in Azure Service Bus can remain available.

Microsoft manages the messaging infrastructure and 
provides built-in reliability features. This reduces 
the work required to manage persistent storage and 
message availability.

However, the Order Service must be modified to communicate 
with Azure Service Bus instead of RabbitMQ. The application 
must also handle connection failures and message processing 
correctly.

## Test Results

| Verification | Before Pod deletion | After Pod deletion |
|---|---|---|
| RabbitMQ Pod | Running | Running (new Pod) |
| Number of queues | 1 | 0 |
| Number of messages | 3 | 0 |
| Persistent Volume Claims | 0 | 0 |

## Challenges and Lessons Learned

One challenge I encountered was selecting a virtual 
machine size with enough available vCPU quota in my 
Azure for Students subscription. I solved this problem 
by using the Belgium Central region and Standard D2as v4 
virtual machines.

I also learned that Kubernetes self-healing does not 
automatically protect application data. Persistent 
storage is necessary when deploying stateful applications 
such as RabbitMQ.

## References

- Kubernetes - Persistent Volumes:
  https://kubernetes.io/docs/concepts/storage/persistent-volumes/

- Kubernetes - StatefulSets:
  https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/

- RabbitMQ - Queues and Durability:
  https://www.rabbitmq.com/docs/queues

- Microsoft Learn - Reliability in Azure Service Bus:
  https://learn.microsoft.com/en-us/azure/reliability/reliability-service-bus
