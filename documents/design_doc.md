# Design Doc

# 1. Introduction

## 1.1 Overview

We are a logistics company with ground robot assistants and drones to help users in San Francisco deliver small and medium-sized items. We have a total of 3 distribution stations in the city.

## 1.2 Goals

Develop a Web app that enables users to place orders and track packages status as well as enable our company to manage the order dispatch and delivery.

## 1.3 Milestone

**Week 1** (9/7-9/12)

- Specify the role of each member
- MVP
- Priority 0 & Priority 1

**Week 2** (9/13-9/19)

- Design Doc
- DB Schema
- UI Design
- Basic Codes

**Week 3** (9/20-9/26)

- Implement Design
- Write Code
- Midterm Present

**Week 4** (9/27-10/3)

- Implement Design
- Code Review

**Week 5** (10/4-10/8)

- Code Review
- Final Presentation

---

# 2. Proposed Solution

## 2.1 Overview

Dispatch & Delivery Management system is a system that allows users to order delivery service and track their order any time after they create an order. Our systems could achieve the below functions:

- Users’ registration, login, and logout
- Give users two options (include route, fee, and time) of carriers(drones and robots) based on the two destination points they are given, and the user can pick an option.
- Track the time left and current destination of unfinished order
- Track all previous personal orders on our system

[https://embed.creately.com/ViJvvCVknTk?type=svg](https://embed.creately.com/ViJvvCVknTk?type=svg)

## 2.2 Implementation

To implement the whole workflow of our service architecture, we divide our system core into 4 independent logistic functions, option generation, order placement, order tracking, and history order.

[https://lh3.googleusercontent.com/ri8gTRzLzf9xv_3Sq3turnlCOwAWtm13DcZ8fWDh2MoBdAQpD4CSphPS-8ihyDukhHiUqeV2C67v5wV2XySVkTQZCsyl6TTEiIoqwDj7LCSMxjj045rRF8jx1vMu5OXDWz7lJAys](https://lh3.googleusercontent.com/ri8gTRzLzf9xv_3Sq3turnlCOwAWtm13DcZ8fWDh2MoBdAQpD4CSphPS-8ihyDukhHiUqeV2C67v5wV2XySVkTQZCsyl6TTEiIoqwDj7LCSMxjj045rRF8jx1vMu5OXDWz7lJAys)

Framework and tools we use: Spring MVC, Hibernate, JVC API Architecture(or count down API), thread pool, AWS RDS, and …...

- Option generation
    - Get user input (two addresses)
    - Call external google map API
    - Create two options instance and return to front end
    - Record user choice
- Order placement
    - Create order instance by users return
    - Store order to the database
- Order tracking
    - Get certain carrier via carrier id stored in order record
    - Update information stored in order
    - Return and render order to the front end
- History order
    - Loop through the database and find all the order has required userIdand
    - Return a list of order and render in the front end

## 2.3 Challenge

One key point through the whole process above is how to update order status in time and implement real-time monitoring.

One solution here is using a Java countdown API in every carrier thread, update current status code automatically by presetting several time points, for example, if we count from 0s, the first status update point will be the time cost from carrier station to order start address, the second point should be the time cost from start point to the end point plus time in previous step, and etc. Moreover, when the user clicks at the front end and wants to see where the order currently is, it can also be implemented easily by tracking the corresponding carrier thread, which has the countdown in, from the order record in the database.

---

## 3. Alternative Solution

## 3.1 Overview

Our alternative solution use publisher-subscriber pattern to divide the system into four different services:

1. Order Service: OS
2. Notification Service: NS
3. Dispatch Service: DS
4. User Profile Management

Each service can talk with another one by publishing a message and subscribing to topics.

[https://embed.creately.com/mhWqSNtTHqC?token=qonDRC29NLNueePj&type=svg](https://embed.creately.com/mhWqSNtTHqC?token=qonDRC29NLNueePj&type=svg)

## 3.2 Implementation

### 3.2.1 Order Service

This service will manage the option management and placement of delivery. It will persist the result into an **Order** database.

Users can view the detail of their order using this service and their past order histories.

External Payment Gateway is replaced by prepaid credit in user accounts.

### 3.2.2 Notification Service

This service will delivering notifications to users and station admin.

- **The user**: about different stages of the order.

### 3.2.3 Dispatch Service

Few functionalities that this service will handle:

- View order from a queue that station admin can accept.
- post accept order message.
- post delivery completion message.

### 3.2.4 User Profile Management

The users can create their profile with email and password.

## 3.3 Order fulfillment Workflow

After the user places an order using the web client, the order processes as follows:

1. **OS** publishes a **MSG(#1)** to the queue about the order.
2. **DS** will read the **MSG(#1)**, distribute order to a carrier and post a **MSG(#2)** to the queue about the carrier acceptance.
3. **NS** will read **MSG(#2)** and notfiy the user about the carrier assignment.
4. After the carrier picks up the package, carrier will update the status of the order using **DS**. **DS** will then post a **MSG(#3)** to the queue that the package has been picked up.
5. **NS** will read **MSG(#3)** and notify the user that the package is picked up.
6. Carrier will reach the end address and deliver the package and marks the order as complete. **DS** will post a **MSG(#4)** to the queue about delivery completion.
7. **NS** will read **MSG(#4)** and notify the user about the delivery of package.

---

# 4. Design Viewpoints

## 4.1 Introduction

In this part, five main design viewpoints will be explained in detail.

- Context Viewpoint
- Composition Viewpoint
- Logical Viewpoint
- State Dynamics Viewpoint
- Interaction Viewpoint

## 4.2 Context Viewpoint

Our software context viewpoint shows the functionalities among station, user and system provided by a design. The context is defined by users who interact with the software.

### 4.2.1 User Use Case Diagram

[https://embed.creately.com/w6xWFfkHCKh?token=rsuqf3yQMyRrL4iB&type=svg](https://embed.creately.com/w6xWFfkHCKh?token=rsuqf3yQMyRrL4iB&type=svg)

Figure 1: Use Case Diagram

## 4.3 State Dynamics Viewpoint

[https://embed.creately.com/ViJvvCVknTk?type=svg](https://embed.creately.com/ViJvvCVknTk?type=svg)

## 4.4 Composition Viewpoint

[Composition ViewPoint.drawio](https://drive.google.com/file/d/1P11c9ExIXDYU-cU7qnmSWtl05Ix74GGG/view?usp=drivesdk)

## 4.5 Logical Viewpoint

### 4.5.1 User Class

![Design%20Doc%2000a87f4782c04a2e9e4798283ee29e7b/Untitled_(2).png](Design%20Doc%2000a87f4782c04a2e9e4798283ee29e7b/Untitled_(2).png)

---

## 4.6 Interaction Viewpoint

### 4.6.1 User -Server Sequence Diagram

UML Sequence Diagram TODO

---

## 5. Conclusion

- Use agile development methodology to develop a Dispatch & Delivery Management app for a logistics company utilizing ground robots and drones delivery in the San Francisco urban area.
- Two design approaches are suggested:
    - Two approaches share some similar business logic in - Register/Login/Track/History.
    - The main difference between two is how to to implement getCarrierOption/Orderlogic. One uses a Java countdown API in each carrier thread to track its current status automatically by presetting several time points while other uses publish/subscribe pattern to determine how to dispatch carriers.
- Five aspects of design viewpoints are addressed based on the functionality.
- Prototype coding of implementation on DB/DAO is achieved.
