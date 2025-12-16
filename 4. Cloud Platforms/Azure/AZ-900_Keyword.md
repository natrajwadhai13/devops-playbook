---
title: "📌 AZ-900-KeyWord"
parent: "• Azure"
grand_parent: "4. Cloud Platforms"
nav_order: 41
has_children: true
---


# Module 1: Describe cloud computing

## Define cloud models

Private, Public, Hybrid cloud, Multi-cloud

Azure Arc - Azure Arc is a set of technologies that helps manage your cloud environment. Azure Arc can help manage your cloud environment whether it's a public cloud solely on Azure, a private cloud in your datacenter, a hybrid configuration, or even a multi-cloud environment running on multiple cloud providers at once.

Azure VMware Solution - What if you’re already established with VMware in a private cloud environment but want to migrate to a public or hybrid cloud? Azure VMware Solution lets you run your VMware workloads in Azure with seamless integration and scalability.


## unit: Describe the consumption-based model

When comparing IT infrastructure models, there are two types of expenses to consider. Capital expenditure (CapEx) and operational expenditure (OpEx).

CapEx is typically a one-time, up-front expenditure to purchase or secure tangible resources. A new building, repaving the parking lot, building a datacenter, or buying a company vehicle are examples of CapEx.

In contrast, OpEx is spending money on services or products over time. Renting a convention center, leasing a company vehicle, or signing up for cloud services are all examples of OpEx.



## Learning objectives cloud models

Define cloud computing.
Describe the shared responsibility model.
Define cloud models, including public, private, and hybrid.
Identify appropriate use cases for each cloud model.
Describe the consumption-based model.
Compare cloud pricing models.

========================================================

# Module 2: Describe the benefits of using cloud services

Learning objectives


Describe the benefits of high availability and scalability in the cloud.

Describe the benefits of reliability and predictability in the cloud.

Describe the benefits of security and governance in the cloud.

Describe the benefits of manageability in the cloud.


# High availability

Scalability - Scaling generally comes in two varieties: vertical and horizontal. Vertical scaling is focused on increasing or decreasing the capabilities of resources. Horizontal scaling is adding or subtracting the number of resources.

Vertical scaling - With vertical scaling, if you were developing an app and you needed more processing power, you could vertically scale up to add more CPUs or RAM to the virtual machine. Conversely, if you realized you had over-specified the needs, you could vertically scale down by lowering the CPU or RAM specifications.

Horizontal scaling- With horizontal scaling, if you suddenly experienced a steep jump in demand, your deployed resources could be scaled out (either automatically or manually). For example, you could add additional virtual machines or containers, scaling out. In the same manner, if there was a significant drop in demand, deployed resources could be scaled in (either automatically or manually), scaling in.


# Describe the benefits of reliability and predictability in the cloud

Reliability: Reliability is the ability of a system to recover from failures and continue to function. It's also one of the pillars of the Microsoft Azure Well-Architected Framework.

The cloud, by virtue of its decentralized design, naturally supports a reliable and resilient infrastructure. With a decentralized design, the cloud enables you to have resources deployed in regions around the world. With this global scale, even if one region has a catastrophic event other regions are still up and running. You can design your applications to automatically take advantage of this increased reliability. In some cases, your cloud environment itself will automatically shift to a different region for you, with no action needed on your part. You’ll learn more about how Azure leverages global scale to provide reliability later in this series.

Predictability: Predictability in the cloud lets you move forward with confidence. Predictability can be focused on performance predictability or cost predictability. Both performance and cost predictability are heavily influenced by the Microsoft Azure Well-Architected Framework. Deploy a solution built around this framework and you have a solution whose cost and performance are predictable.

Performance: Performance predictability focuses on predicting the resources needed to deliver a positive experience for your customers. Autoscaling, load balancing, and high availability are just some of the cloud concepts that support performance predictability. If you suddenly need more resources, autoscaling can deploy additional resources to meet the demand, and then scale back when the demand drops. Or if the traffic is heavily focused on one area, load balancing will help redirect some of the overload to less stressed areas.

Cost: Cost predictability is focused on predicting or forecasting the cost of the cloud spend. With the cloud, you can track your resource use in real time, monitor resources to ensure that you’re using them in the most efficient way, and apply data analytics to find patterns and trends that help better plan resource deployments. By operating in the cloud and using cloud analytics and information, you can predict future costs and adjust your resources as needed. You can even use tools like the Total Cost of Ownership (TCO) or Pricing Calculator to get an estimate of potential cloud spend.

# Describe the benefits of security and governance in the cloud

# Describe the benefits of manageability in the cloud

Management in the cloud

Through a web portal.
Using a command line interface.
Using APIs.
Using PowerShell.

==========================

# Describe cloud service types

Learning objectives

Describe infrastructure as a service (IaaS).
Describe platform as a service (PaaS).
Describe software as a service (SaaS).
Identify appropriate use cases for each cloud service (IaaS, PaaS, SaaS).


## Shared responsibility model

Some common scenarios where IaaS might make sense include:

Lift-and-shift migration: You’re setting up cloud resources similar to your on-prem datacenter, and then simply moving the things running on-prem to running on the IaaS infrastructure.
Testing and development: You have established configurations for development and test environments that you need to rapidly replicate. You can start up or shut down the different environments rapidly with an IaaS structure, while maintaining complete control.

## Describe Platform as a Service

Some common scenarios where PaaS might make sense include:

Development framework: PaaS provides a framework that developers can build upon to develop or customize cloud-based applications. Similar to the way you create an Excel macro, PaaS lets developers create applications using built-in software components. Cloud features such as scalability, high-availability, and multi-tenant capability are included, reducing the amount of coding that developers must do.
Analytics or business intelligence: Tools provided as a service with PaaS allow organizations to analyze and mine their data, finding insights and patterns and predicting outcomes to improve forecasting, product design decisions, investment returns, and other business decisions.

## Describe Software as a Service

Some common scenarios for SaaS are:

Email and messaging.
Business productivity applications.
Finance and expense tracking.





=====================

# Module 3: Describe cost management in Azure

Describe factors that can affect costs in Azure.
Compare the Pricing calculator and Total Cost of Ownership (TCO) calculator.
Describe the Microsoft Cost Management Tool.
Describe the purpose of tags.
---
## Describe factors that can affect costs in Azure

Azure shifts development costs from the capital expense (CapEx) of building out and maintaining infrastructure and facilities to an operational expense (OpEx) of renting infrastructure as you need it, whether it’s compute, storage, networking, and so on.

That OpEx cost can be impacted by many factors. Some of the impacting factors are:

Resource type
Consumption
Maintenance
Geography
Subscription type
Azure Marketplace

### Geography

When you provision most resources in Azure, you need to define a region where the resource deploys. Azure infrastructure is distributed globally, which enables you to deploy your services centrally or closest to your customers, or something in between. With this global deployment comes global pricing differences. The cost of power, labor, taxes, and fees vary depending on the location. Due to these variations, Azure resources can differ in costs to deploy depending on the region.

Network traffic is also impacted based on geography. For example, it’s less expensive to move information within Europe than to move information from Europe to Asia or South America.


## Explore the pricing calculator

## Exercise - Estimate workload costs by using the Pricing calculator

## Describe the Microsoft Cost Management tool

What is Cost Management?

Cost Management provides the ability to quickly check Azure resource costs, create alerts based on resource spend, and create budgets that can be used to automate management of resources.

Cost analysis is a subset of Cost Management that provides a quick visual for your Azure costs. Using cost analysis, you can quickly view the total cost in a variety of different ways, including by billing cycle, region, resource, and so on.

Cost alerts: 

  Budget alerts: Budget alerts notify you when spending, based on usage or cost, reaches or exceeds the amount defined in the alert condition of the budget. Cost Management budgets are created using the Azure portal or the Azure Consumption API.

  Credit alerts: Credit alerts notify you when your Azure credit monetary commitments are consumed. Monetary commitments are for organizations with Enterprise Agreements (EAs). Credit alerts are generated automatically at 90% and at 100% of your Azure credit balance. Whenever an alert is generated, it's reflected in cost alerts, and in the email sent to the account owners.

  Department spending quota alerts.: Department spending quota alerts notify you when department spending reaches a fixed threshold of the quota. Spending quotas are configured in the EA portal. Whenever a threshold is met, it generates an email to department owners, and appears in cost alerts. For example, 50 percent or 75 percent of the quota.

  Budgets: A budget is where you set a spending limit for Azure. You can set budgets based on a subscription, resource group, service type, or other criteria. When you set a budget, you will also set a budget alert. When the budget hits the budget alert level, it will trigger a budget alert that shows up in the cost alerts area. If configured, budget alerts will also send an email notification that a budget alert threshold has been triggered.

A more advanced use of budgets enables budget conditions to trigger automation that suspends or otherwise modifies resources once the trigger condition has occurred.


## Describe the purpose of tags

An example tagging structure

A resource tag consists of a name and a value. You can assign one or more tags to each Azure resource.

Resource management Tags

Cost management and optimization Tags 

Operations management Tags

Security Tags

Governance and regulatory compliance Tags

Workload optimization and automation Tags


## Module assessment

---
===========================================

# **Describe features and tools in Azure for governance and compliance**

Learning objectives

Describe the purpose of Microsoft Purview

Describe the purpose of Azure Policy

Describe the purpose of resource locks

Describe the purpose of the Service Trust portal


