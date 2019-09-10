---
description: 'https://github.com/Tencent/TSeer'
---

# TSeer

## Introduction

TSeer is a suite of service registry, service discovery and service fault tolerant solutions that is a lightweight transformation of the Tars's name service. TSeer is widely used in Tencent Browser, YingYongBao, Tencent Manager, QQ ubook, Tencent Literature, GuangDianTong and many other businesses, which with an average daily requests of ten billions.

In addition to the core features of service discovery, Tseer also supports a variety of excellent load balancing algorithms and provide reliable fault tolerant strategies. To handle the rapid development of massive services, Tseer supports three different routing strategies: nearest access, IDC Grouping , and Set Grouping. Through TSeer, the engineering services can achieve highly intelligent scheduling optimization, effectively solve the cross-regional cross-area room calls and other issues, greatly promoted the availability and performance.

Users can choose web management interface or API access to use TSeer according to their needs. TSeer provides transparent service discovery through proxy nodes and proxy server mechanisms for services that require frequent changes. TSeer's learning cost is very low and its operation is very convenient. Therefore, it is very friendly to Business operations staff.

At the same time, TSeer is so lightweight that it has low business intrusiveness. The services, not based on Tars, can also be seamlessly accessed. TSeer is an excellent name service solution in the microservices framework.

## Background

For a system with a large number of services, distributed service clusters and microservice frameworks have gradually become the mainstream. While the new architecture provides better support for services, they are frequently updated and scaled, resulting in frequent changes in the network location. Service response time and efficient fault tolerance become issues that need to be solved. To address this problem, we have developed TSeer.

## Architecture

![](../.gitbook/assets/pptseer.en.png)

TSeer's structure is composed of three parts: TSeerServer, Business Client\(caller\), Business Server\(callee\).

### TSeerServer 

TSeerServer is the hub and core module of the entire TSeer. When a new node goes online, the O&M personnel needs to first register the node with the TSeerServer cluster through the WEB management platform as well as record its network location information in the TSeerServer system. When the node needs to be offline or modified, the O&M personnel also needs to perform related operations on the WEB management platform. Callee will periodically report the heartbeat to TSeerserver. Finally, TSeerserver will block the nodes with timeout heartbeat so that they cannot be called.

### Client 

A business client is a node that needs to invoke other services. It is called caller and it is a user of the service discovery function. The business client can obtain the address of the service node to be invoked from the TSeerServer in two ways by installing the Agent and the API call, thereby completing the service call.

### Server

A business server is a node that needs to be called. It is called callee and is a provider of the service. When the new node of a business server goes online, the callee needs to be registered in TSeerServer. Regardless of the number of nodes in the same callee service cluster, the service cluster needs to register a unified name when registering. The caller in the calling logic only needs to specify the name of the service that needs to be invoked. TSeer will return the callee's address based on the name. When the callee's server cluster needs to expand, the O&M personnel only needs to add the new node's address under the name corresponding to the service. The business personnel does not need to manage the information of numerous service nodes in the callee cluster. Consequently, the use of TSeer is very convenient.

## Function

### Load Balance

When some nodes are frequently called in the same service cluster and other nodes do not bear a reasonable load, the service quality and response time of the service will be highly reduced, and resources will also be wasted. 

In the TSeer system, when the caller initiates a call, TSeer provides to the caller four load balancing methods for all available nodes under the callee's name to ensure that each node of the callee cluster bears a reasonable load. The implemented balancing methods are: Round Robin, Random, Static Weight, Consistent hash. Furthermore, the user can also customize the load balancing implementation by invoking the grouping method. The way of invoking the grouping will be mentioned below.

### Fault Tolerance

In order to solve the problem of service unavailability and service quality degradation caused by node downtime, TSeer also provides a reliable fault tolerance mechanism. 

After the caller makes a call, the result of the call is reported to TSeer Server. If the call failed, TSeer Server will temporarily mask the callee's node to prevent the faulty node from being called repeatedly. The TSeer Server will periodically probe the blocked callee's node. When the faulty callee's node recovers the service, TSeer Server will activate it again. When TSeer Server fails, callers can continue to call based on the cached information.

### Call Optimization

TSeer provides three ways to solve cross-regional calls for the calling logic: IDC grouping, Set grouping, and All.

#### IDC Grouping

The IDC grouping routing can be viewed approximately as the nearest access. 

This method is divided into two levels. The first level is the physical group, which is the smallest group scheduling unit. The physical group is assigned to a unified identification name according to the room or area where the node is located. The second level is the logical group made up of physical groups, which can assign a unified identification name to a larger area. 

For the logical grouping of IDC, TSeer also defines a call priority policy. That is, when some logical groups are unavailable, a list of available callee's node addresses is obtained according to the priority policy.

#### SET Grouping

IDC Grouping mainly divides the groupings in the concept of the area and implements the nearest access strategy. When the service size in the back-end service architecture reaches a certain amount, if it is required to implement isolation control based on capacity, grayscale, and partition management for certain service nodes, the IDC Grouping cannot be satisfied. Thus, Set Grouping is a refinement of IDC Grouping.

## Access Types

<table>
  <thead>
    <tr>
      <th style="text-align:center"></th>
      <th style="text-align:center">Agent</th>
      <th style="text-align:center">API</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">Needs Agent</td>
      <td style="text-align:center">&#x221A;</td>
      <td style="text-align:center">X</td>
    </tr>
    <tr>
      <td style="text-align:center">Needs Init</td>
      <td style="text-align:center">X</td>
      <td style="text-align:center">&#x221A;</td>
    </tr>
    <tr>
      <td style="text-align:center">Self Update</td>
      <td style="text-align:center">
        <p>&#x221A;</p>
        <p>(Configuration changes do not affect the caller&apos;s normal invocation.)</p>
      </td>
      <td style="text-align:center">
        <p>&#x221A;</p>
        <p>(Configuration changes require restarting the client and modifying initialization
          information.)</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:center">Name Routing</td>
      <td style="text-align:center">By Agent</td>
      <td style="text-align:center">By API</td>
    </tr>
    <tr>
      <td style="text-align:center">Data Reporting</td>
      <td style="text-align:center">API reports data to Agent</td>
      <td style="text-align:center">API reports data to TSeerServer</td>
    </tr>
    <tr>
      <td style="text-align:center">Fault Tolerance</td>
      <td style="text-align:center">
        <p>&#x221A;</p>
        <p>(Memory &amp; Hard disk)</p>
      </td>
      <td style="text-align:center">
        <p>&#x221A;</p>
        <p>(Memory &amp; Hard Disk)</p>
      </td>
    </tr>
  </tbody>
</table>

