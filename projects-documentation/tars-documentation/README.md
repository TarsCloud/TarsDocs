# TARS Documentation

TARS is a high-performance RPC framework based on TARS protocol that provides an integrated solution of microservice governance for developers and operators. TARS was developed by Tencent, TARS is multi-language and agile R&D, has high availability, efficient operation, and it is out-of-the-box. TARS is a perfect practice of DevOps and massive services dedicated to solving problems for developers and operators. For more information please refer to [github.com/tarscloud](www.github.com/tarscloud)

### Multi-programming Language

TARS uses IDL, Interface Description Language, to support many programming languages, such as C++, Java, Node.JS, PHP, Python, and Golang. In this way, programs written in different languages or on different platforms can communicate with each other.

### Agile R&D

According to the interface description file, TARS can automatically generate basic communication code between client and server. Developers only need to focus on business logic and provide service to the outside. TARS can be integrated into component management, code scanning, testing, and other tools or platforms, by helping to detect and to fix the problems of code quality. TARS provides high extensibility of its microservices system, it does not affect the running of existing service when adding interface or expanding service.

### High Availability

The business service is registered with a naming service, and the client gets the actual service address from the service name. When the service node cannot report heartbeat due to breakdown, the naming service will no longer return the address of the faulty node to the client, and the client will automatically make a decision according to the abnormal situation and shield the fault. TARS supports intelligent scheduling based on network and server state, such as location and latency. It also provides IDC, SET grouping, and other functions to meet requirements of customized scheduling. In order to prevent system crash caused by traffic burst or server failure, TARS uses non-blocking asynchronous request queue and monitors the length of the queue to ensure operation of the system.

### Efficient Operation

The lossless change function of TARS makes the function module not affect the overall operation of the system in the process of changing through the way of grayscale. Additionally, it can support multi-dimensional monitoring based on service call data, log statistics, feature interface, and notification alarm. For self-management, TARS has a complete set of visual management platform in order to make monitoring more intuitive, the configuration modification is easier and safer, and service management is very convenient.

By supporting multi-language, agile R&D, high availability, efficient operation, TARS has become the right hand to developers and operators, and the common choice of more than 100 typical applications. TARS was born on our decades of precipitation. TARS is a brainchild of the open-source community, a powerful and loyal partner, growing with millions of developers and operators in the mysterious Internet universe.

