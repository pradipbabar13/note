# Understanding Operators and OLM in OpenShift

## Introduction

This document aims to provide a comprehensive understanding of Operators and the Operator Lifecycle Manager (OLM) in OpenShift. It covers their definitions, availability, development, deployment, and the resources that developers need to manage.

## Table of Contents

1. Introduction
2. What are Operators?
3. What is the Operator Lifecycle Manager (OLM)?
4. Availability of OLM in OpenShift
5. Building Operators for OperatorHub
6. Differences Between Normal Deployment and Deployment with OLM
7. Resources Managed by Developers
   - Without OLM
   - With OLM
8. Deploying an Operator with OLM Without Using the Operator SDK
9. Using Operator SDK
   - Development Process
   - Deployment Process
10. Summary of Development with and without Operator SDK
11. Components for Operator Developer (Included in Operator Bundle)
12. Components for User (Managed in Cluster)

## 1. Introduction

Operators and OLM are crucial for managing Kubernetes-native applications and their lifecycle in OpenShift. This document explains these concepts, their usage, and how they facilitate automated and efficient management of applications.

## 2. What are Operators?

Operators are Kubernetes-native applications that use custom resources to manage their components and automate operational tasks such as deployment, scaling, and backup. They encapsulate human operational knowledge in code.

## 3. What is the Operator Lifecycle Manager (OLM)?

OLM is a component of OpenShift that helps manage the lifecycle of Operators. It provides a framework to manage the installation, update, and management of Operators on a Kubernetes cluster, ensuring proper deployment, versioning, and maintenance.

## 4. Availability of OLM in OpenShift

OLM is included by default in OpenShift, making it readily available upon cluster installation. This allows administrators to manage Operators immediately.

## 5. Building Operators for OperatorHub

To publish an Operator in OperatorHub, it is recommended to build it using the OLM standard. This involves creating specific metadata and packaging the Operator in a format that OLM can understand and manage. The metadata includes information about the Operator’s capabilities, dependencies, and installation procedures.

## 6. Differences Between Normal Deployment and Deployment with OLM

### Normal Deployment of an Operator

- **Manual Steps**: Manually apply Kubernetes manifests using kubectl.
- **No Versioning**: Version management requires manual intervention and custom scripts.
- **No Dependency Management**: Dependencies must be manually managed.
- **Manual Upgrades**: Upgrades require manual application of updated manifests and ensuring compatibility.

### Deployment with OLM

- **Automated Management**: OLM automates the installation, upgrade, and removal of Operators.
- **Versioning**: OLM tracks and manages different Operator versions, facilitating upgrades and rollbacks.
- **Dependency Management**: OLM handles dependencies, ensuring required components are present before installation.
- **OperatorHub Integration**: Operators can be published to OperatorHub, making them discoverable and installable.
- **CRD Management**: OLM manages CRDs, ensuring they are created and updated as needed.
- **Lifecycle Hooks**: OLM can run pre- and post-install hooks, allowing for custom logic execution.

## 7. Resources Managed by Developers

### Without OLM

When developing an Operator without using OLM, developers need to manage the following resources manually:

1. **Custom Resource Definitions (CRDs)**: Define the custom resources that the Operator will manage.
    - Example: `MyCustomResourceDefinition.yaml`
2. **Custom Resources (CRs)**: Instances of the CRDs that the Operator will manage.
    - Example: `MyCustomResource.yaml`
3. **Controller Deployment**: Deployment of the Operator's controller.
    - Example: `OperatorDeployment.yaml`
4. **Service Accounts**: Used by the Operator to interact with the Kubernetes API.
    - Example: `ServiceAccount.yaml`
5. **Role and RoleBindings**: Define the permissions needed by the Operator.
    - Example: `Role.yaml` and `RoleBinding.yaml`
6. **ClusterRole and ClusterRoleBindings**: If the Operator needs cluster-wide permissions.
    - Example: `ClusterRole.yaml` and `ClusterRoleBinding.yaml`
7. **ConfigMaps and Secrets**: Used for configuration and storing sensitive data.
    - Example: `ConfigMap.yaml` and `Secret.yaml`
8. **Services and Endpoints**: If the Operator needs to expose services.
    - Example: `Service.yaml`
9. **Admission Webhooks**: For validating and mutating admission controllers.
    - Example: `ValidatingWebhookConfiguration.yaml` and `MutatingWebhookConfiguration.yaml`
10. **Other Resources**: Any other Kubernetes resources required by the application managed by the Operator.
    - Examples: `PersistentVolumeClaim.yaml`, `StatefulSet.yaml`

### With OLM

When developing an Operator to work with OLM, developers need to manage additional resources, but many tasks are automated or simplified:

1. **Custom Resource Definitions (CRDs)**: Define the custom resources that the Operator will manage.
    - Example: `MyCustomResourceDefinition.yaml`
2. **Custom Resources (CRs)**: Instances of the CRDs that the Operator will manage.
    - Example: `MyCustomResource.yaml`
3. **ClusterServiceVersion (CSV)**: Describes the metadata and lifecycle of the Operator, including its versions, permissions, and owned CRDs. This is a critical OLM-specific resource.
    - Example: `ClusterServiceVersion.yaml`
4. **Package Manifest**: Describes the available versions of the Operator and specifies which CSV to use for each version.
    - Example: `PackageManifest.yaml`
5. **Catalog Source**: Defines a source of Operator packages that OLM can use to discover and install Operators.
    - Example: `CatalogSource.yaml`
6. **Subscriptions**: Used to subscribe to a specific Operator from a Catalog Source, managing its installation and updates.
    - Example: `Subscription.yaml`
7. **OperatorGroup**: Defines the scope of the Operator’s deployment, specifying which namespaces the Operator will watch and manage.
    - Example: `OperatorGroup.yaml`
8. **Role and RoleBindings / ClusterRole and ClusterRoleBindings**: Managed within the CSV, specifying the necessary permissions directly in the CSV manifest.
9. **Service Accounts**: Defined within the CSV, reducing the need for separate YAML files.
10. **ConfigMaps, Secrets, Services, etc.**: Managed within the CSV, simplifying resource management.

## 8. Deploying an Operator with OLM Without Using the Operator SDK

Deploying an operator with OLM without using the Operator SDK requires manually managing several configurations and resources. Here’s a brief overview of the necessary components and configurations:

### For Operator Developer (Creating the Operator Bundle)

1. **Custom Resource Definitions (CRDs)**
    - **Purpose**: Defines the schema and validation for the custom resources managed by your operator.
    - **Configuration**: Write YAML files describing the structure of your custom resources, including fields, types, and validation rules.
2. **ClusterServiceVersion (CSV)**
    - **Purpose**: Provides metadata about the operator, including version information, installation strategy, permissions, and custom resource definitions.
    - **Configuration**:
        - **Metadata**: Name, version, description, and other metadata.
        - **Install Strategy**: Specifies how the operator should be deployed (e.g., Deployment).
        - **Custom Resources**: Lists the CRDs that the operator manages.
        - **Permissions**: Specifies RBAC roles and permissions the operator needs.
        - **Deployment Details**: Includes information about the operator's image, replicas, and other deployment details.
3. **Annotations Metadata**
    - **Purpose**: Contains metadata annotations required by OLM.
    - **Configuration**: Write an `annotations.yaml` file that includes annotations for OLM, such as the bundle format, package name, and channels.
4. **Package Manifest (Optional)**
    - **Purpose**: Describes the package and channels available for the operator.
    - **Configuration**: Create a YAML file that lists available versions and channels for the operator.
5. **Bundle Dockerfile**
    - **Purpose**: Used to build the operator bundle image.
    - **Configuration**: Write a Dockerfile that packages your manifests and metadata into a container image.

### For User (Deploying the Operator with OLM)

1. **CatalogSource**
    - **Purpose**: Provides a way to discover and install operators from a specific source or catalog.
    - **Configuration**: Write a YAML file that points to the operator bundle image in a registry. This file includes information about the source type (e.g., grpc), image URL, and update interval.
2. **Subscription**
    - **Purpose**: Subscribes to an operator from a CatalogSource, enabling automatic updates and lifecycle management.
    - **Configuration**:
        - **Metadata**: Name and namespace of the subscription.
        - **Spec**: Specifies the package name, channel, and source of the operator. It can also include installation modes and starting CSV version.
3. **OperatorGroup**
    - **Purpose**: Defines the scope (namespace) in which the operator will be installed and manage resources.
    - **Configuration**: Write a YAML file that includes the target namespaces the operator should watch. This ensures the operator has the correct scope and permissions.

### Summary of Configuration Files

| Component | Purpose | Configuration Details |
|-----------|---------|------------------------|
| **CRDs** | Define custom resource schemas and validation rules | YAML files specifying fields, types, validation rules, and metadata for each custom resource. |
| **ClusterServiceVersion** | Metadata about the operator, including version, installation strategy, permissions, and CRDs | YAML file including metadata (name,

 version, description), install strategy (e.g., Deployment), custom resources, permissions, and deployment details. |
| **Annotations Metadata** | Contains metadata annotations required by OLM | YAML file (`annotations.yaml`) with annotations for OLM, such as bundle format, package name, channels, etc. |
| **Package Manifest** | Describes the operator package and available channels (optional) | YAML file (`package.yaml`) listing the operator package name, versions, and channels. |
| **Bundle Dockerfile** | Builds the operator bundle image | Dockerfile specifying how to package the manifests and metadata into a bundle image. |
| **CatalogSource** | Points to the operator bundle image for discovery and installation | YAML file including source type (grpc), image URL, and update interval. |
| **Subscription** | Subscribes to the operator from a CatalogSource, managing updates and lifecycle | YAML file specifying package name, channel, source, installation modes, and starting CSV version. |
| **OperatorGroup** | Defines the namespaces the operator will manage | YAML file listing the target namespaces for the operator, ensuring correct scope and permissions. |

By manually managing these configurations, you can deploy an operator with OLM without using the Operator SDK. However, this approach requires careful attention to detail and a thorough understanding of Kubernetes and OLM concepts to ensure everything is set up correctly.

## 9. Using Operator SDK

### Development Process

- **Scaffolding**: Automatically generates the initial project structure and boilerplate code.
- **CRD and CSV Generation**: Simplifies the creation of Custom Resource Definitions (CRDs) and ClusterServiceVersion (CSV) manifests.
- **Code Generation**: Automatically generates code for controllers and API types based on your CRD definitions.
- **Testing**: Provides built-in testing frameworks to help ensure your operator works as expected.
- **Validation**: Includes tools to validate your CSV, CRDs, and other manifests to ensure they comply with OLM requirements.

### Deployment Process

- **Bundle Creation**: Generates the necessary bundle metadata and Dockerfile for building the operator bundle image.
- **OLM Integration**: Simplifies the creation of resources like CatalogSource, Subscription, and OperatorGroup.
- **Automation**: Automates many repetitive tasks, reducing the chance of errors and speeding up the development cycle.

### Not Using Operator SDK

#### Development Process

- **Manual Setup**: You need to manually create the project structure and all necessary boilerplate code.
- **Manual CRD and CSV Creation**: Write CRDs and CSVs by hand, which is time-consuming and error-prone.
- **Code Writing**: Manually implement controllers, API types, and reconciliation logic.
- **Manual Testing**: Set up and configure testing frameworks and write tests without built-in support.
- **Validation**: Manually ensure your CRD and CSV conform to OLM requirements, increasing the likelihood of mistakes.

#### Deployment Process

- **Manual Bundle Creation**: Write the Dockerfile and bundle metadata manually.
- **OLM Resources**: Manually create CatalogSource, Subscription, and OperatorGroup resources, which requires a deep understanding of OLM.
- **Increased Risk**: Higher likelihood of errors due to manual processes, leading to potential deployment issues and increased debugging time.

### Summary of Development with and without Operator SDK

| Aspect | Using Operator SDK | Not Using Operator SDK |
|--------|--------------------|------------------------|
| **Scaffolding** | Automated project setup | Manual project setup |
| **CRD/CSV Creation** | Automated generation and validation | Manual creation and validation |
| **Code Generation** | Automated code generation for controllers and APIs | Manual coding for controllers and APIs |
| **Testing** | Built-in testing framework | Manual setup of testing frameworks |
| **Validation** | Automated validation of manifests | Manual validation of manifests |
| **Bundle Creation** | Automated bundle metadata and Dockerfile generation | Manual creation of bundle metadata and Dockerfile |
| **OLM Integration** | Simplified creation of CatalogSource, Subscription, etc. | Manual creation of OLM resources |
| **Development Speed** | Faster development and fewer errors | Slower development with higher risk of errors |

Using the Operator SDK greatly simplifies the development and deployment process by automating many tasks and providing tools that ensure compliance with best practices and OLM requirements. Not using the SDK requires a more manual approach, which can be more error-prone and time-consuming.

## 10. Components for Operator Developer (Included in Operator Bundle)

| Component | Description |
|-----------|-------------|
| **CustomResourceDefinition** | Defines the schema for the custom resources managed by the operator. |
| **ClusterServiceVersion** | Provides metadata about the operator, including version, deployment strategy, and RBAC permissions. |
| **annotations.yaml** | Contains metadata annotations required by OLM for the operator bundle. |
| **package.yaml (optional)** | Describes the package and channels available for the operator. |
| **bundle.Dockerfile** | Dockerfile used to build the operator bundle image. |

## 11. Components for User (Managed in Cluster)

| Component | Description |
|-----------|-------------|
| **Subscription** | Subscribes to an operator from a CatalogSource, allowing for automatic updates and lifecycle management. |
| **CatalogSource** | Provides a way to discover and install operators from a specific source or catalog. |
| **OperatorGroup** | Defines the scope (namespace) in which the operator will be installed and manage resources. |

This document provides a structured approach to understanding, developing, and deploying Operators using OLM in OpenShift. Whether using the Operator SDK or manually managing configurations, it outlines the necessary steps and components to ensure a smooth and efficient workflow.
