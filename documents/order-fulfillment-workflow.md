# Order fulfillment Workflow

## Definition

Order Service: OS

Order Fulfillment Service: OFS

Notification Service: NS

Dispatch Service: DS

Message: MSG

[https://embed.creately.com/KrJiSfsobA3?token=j0LkWDUKcevQwW1L&type=svg](https://embed.creately.com/KrJiSfsobA3?token=j0LkWDUKcevQwW1L&type=svg)

### Order Service

This service will manage the option management and placement of delivery. It will process the payment using an **External Payment Gateway** and persist the result into an **Order** database.

## Workflow

1. **OS** publishes a **MSG(#1)** to the queue about the order.
2. **DS** will read the **MSG(#1)** and post an area-specific **MSG(#2)** to the queue. 
3. **NS** will read **MSG(#2)** and notify carriers in the area.
4. The carrier will accept the order using **DS**. **DS** will then post a **MSG(#3)** to the queue about the carrier acceptance.
5. **NS** will read **MSG(#3)** and notfiy the user about the carrier assignment.
6. After the carrier picks up the package, carrier will update the status of the order using **DS**. **DS** will then post a **MSG(#4)** to the queue that the package has been picked up.
7. **NS** will read **MSG(#4)** and notify the user that the package is picked up.
8. Carrier will reach the end address and deliver the package and marks the order as complete. **DS** will post a **MSG(#5)** to the queue about delivery completion.
9. **NS** will read **MSG(#5)** and notify the user about the delivery of package.
