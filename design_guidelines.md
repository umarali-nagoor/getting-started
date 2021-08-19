# IBM Cloud Terraform Modules - Design Guidelines

Design guidelines for the terraform-module developer, who wish to contribute to [terraform-ibm-modules](https://github.com/terraform-ibm-modules) org. 

Based on certain characterstics, IBM Cloud Terraform modules are categorized into 3 types

| Classification   | Span / complexity                             | Usecases                       | Opinionated | 
|------------------|-----------------------------------------------|:-------------------------------|-------------|
| Type-1           | Single IBM Cloud service                      | Caters to provisioning & <br>configuration of IBM Cloud services;<br>Automates tasks that can be performed<br> using UI or CLI|     Generic & not opinionated        |
| Type-2           | Assemble two or more related<br>IBM Cloud services & its resources<br>to build reusable<br> solution-components.| Catering to a class of usecase &<br>component stereotypes (data service,<br>storage services, access-management<br>service, etc.); Automates the provisioning<br>and configuration of composite services.|less opinionated|
| Type-3          | Leverages multiple IBM Cloud services<br>that are required to build a solution architecture |Catering to provisioning of<br>infrastructure for a complete solution<br>architectures, industry patterns, and<br> quick-starts Automates<br>the configuration of variabilities<br>in the solution architecture.|more opinionated|

# Type-1 Modules

## Characteristics

* Designed for use-cases targeting a single IBM Cloud Service abstraction .

    - used to create lightweight abstractions, and a container for multiple resources that are used together (the primary IBM Cloud Service, and the dependent services). 

    - a IBM Cloud Service abstraction is used to describe the components of an architecture, rather than directly in terms of physical objects.

* Generic in nature, not opinionated, and highly configurable.
* Used to provision and configure the IBM Cloud Service abstractions (and the related resource-instances), using all the  
  attributes exposed by the underlying Terraform resources.
* Designed to be Observable, Scalable, Available and Secure (IAM) - by design.


## Design guidelines

* SHOULD not design modules, as just thin wrappers around a single terraform resource. 
  - If you have trouble finding a name for your module that isn't the same as the main terraform resource type inside it, that may be a sign that your module is not creating any new abstraction and so the module is adding unnecessary complexity. Just use the resource type directly in the calling module instead.
* SHOULD have root module and sub-modules for the IBM Cloud Service abstraction
* MUST expose all the input variables that are required to configure the IBM Cloud Services abstraction (and the dependent  
  services).
* MUST return all the results using output variables that can be consumed by other modules.
* MUST design child module that are Observable, Scalable, Available and Secure - by default
    Example:  
        - Observable, scalable, secure & highly-available VPC cluster (https://github.com/terraform-ibm-modules/terraform-ibm-cluster/tree/master/modules/vpc-kubernetes)
* SHOULD design child modules that can automate the tasks performed using the IBM Cloud Console UI. 
    Example: 
        -   Create VPC kubernetes cluster (https://github.com/terraform-ibm-modules/terraform-ibm-cluster/tree/master/modules/vpc-kubernetes).
        -   Configure VPC cluster with worker pool (https://github.com/terraform-ibm-modules/terraform-ibm-cluster/tree/master/modules/configure-vpc-worker-pool)


# Type-2 Modules

## Characteristics

* Designed to address a common class of usecases that involves multiple IBM Cloud Services 
* Play a stereotyped role in a solution architecture; as a solution-component - such as, storage-layer, management-layer, 
  security-layer, application layer, etc. 
* Slightly opinionated 
    -   with simple input / output parameters,
    -   with pre-baked bindings between multiple cloud services (horizontally)
    -   build a vertical stack of related resources (using the control plane & data plane components) for the solution-component
    -   compliant to industry standards (eg.  FedRAMP, SOC, etc.)

## Design guidelines

* SHOULD be a root module, for the solution-component (not as child modules)
* MUST expose the input variables that are required to configure the solution-component, and bind itself to the other 
  solution-components of the solution-architecture
* MUST return the relevant results using output variables that can be consumed by other solution-components in the architecture; 
  and compliant to the industry standards
* COULD be used to create & configure solution-component using a fewer number of input parameters; 
    -   leverage the 'local' parameters - to simplify & reduce the number input parameters (or abstracts the complexities) - for 
        example, use t-shirt size {XL, L, M, S} to abstract the values for multiple input parameters.
* COULD combine the provisioning & configuration of multiple IBM Cloud Services in an opinionated manner (with pre-defined 
  bindings), to offer a wider platform-level capability
    -   For example, a secure-roks-cluster-on-vpc module that combine the capabilities of IBM Cloud Services such as - Cluster,  
        Activity Tracker, VPC, Security Groups, Subnets ACL, and other Network policies in a opinionated manner
* COULD combine the provisioning & configuration of a stack of IBM Cloud Services, and the related data-plane components in an 
  opinionated manner, to offer a deeper application-level capability. 
    -   For example, provision IKS or ROKS cluster using IBM Cloud provider; and further configure the kubernetes resource using 
        the Kubernetes provider or Helm provider
    -   For example, provision IBM Cloud Database using IBM Cloud provider; and further configure the database resources used   
        the respective database provider (such as, Mongodb provider, Postgre provider, Kafka provider, etc.), such as the backup/restore policies
* COULD combine the provisioning & configuration of IBM Cloud Services, and other Cloud providers in an opinionated manner, to offer multi-cloud solutions
    -   For example, provision a Satellite location & cluster using the IBM Cloud provider; and AWS provider to provision & 
        configure the AWS host. (https://github.com/terraform-ibm-modules/terraform-ibm-satellite/tree/main/modules) 


# Type-3 Modules

## Characteristics

* Designed to fit-for-purpose - and to address a complete Solution Architecture
* Use the module as-a-whole with minimal configuration variabilities 
* Highly opinionated, that implements well-defined usecases, as following
    -   Quickstart code patterns
    -   Industry application pattern
    -   Reference architectures
* Address all the system quality attributes (or -ilities) of the Solution

## Design guidelines

* SHOULD be a root module, for the solution-architecture (not as child modules)
* COULD be composed of the Type-1 / Type-2 modules
* MUST expose a minimal set of input variables that are required to configure the overall solution-architecture;  such as, 
  profile='experimental' (or ''dev", "pre-prod", "prod");  
* MUST return the outcome of the solution using output variables; such as, Application / software URL
* COULD be used to automate the provisioning and configuration of infrastructure for 
    -   Code patterns, 
    -   Reference architectures in Architecture center, 
    -   NIST compliant solution architecture
* COULD be used to automate the provisioning of softwares on IBM Cloud infrastructure (such as - CloudPaks), and the related 
  Quickstarts for multi-cloud usecases