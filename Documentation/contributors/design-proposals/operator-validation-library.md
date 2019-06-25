# Operator Validation Library (OVL)

## **Problem Statement**:

The [operator courier’s](https://github.com/operator-framework/operator-courier) “verify” command has morphed the courier into what many people see as a tool to validate static operator files, which is outside the original scope of the project. Given the adoption rate of courier by various CI/CD teams it is clear that there is a strong need for a library that defines a single definition of a valid operator. It is the goal of this proposal to define what steps an OpenShift intern will take to implement a validation library.

## **Project**:

The courier was designed with the intent of building, validating, and pushing operator bundles to [quay.io](https://quay.io/). These bundles are an amalgamation of a [ClusterServiceVersions](https://github.com/operator-framework/operator-lifecycle-manager/blob/master/Documentation/design/building-your-csv.md), [CustomResourceDefinitions](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/), and [Package Manifest](https://github.com/operator-framework/operator-lifecycle-manager#discovery-catalogs-and-automated-upgrades) yamls. The steps to create these files are manual, tedious, and error prone - as a result it made sense to validate the bundle with courier before pushing the bundle to an app registry where it would be released to the world.

Since courier’s release, the tool has been used by many as a static linter for operators, which is outside the scope of the original goals of the tool. While being used as a static linter it has become increasingly apparent that a great deal of effort is required to keep the tool in sync with the latest changes made to [operator-lifecycle-manager](https://github.com/operator-framework/operator-lifecycle-manager) (OLM). Rather than continuing to react to changes within OLM, we propose creating a library that can validate static operator files by importing the OLM types to be unmarshalled.

By creating a validation library external to courier we see the following advantages:
  * **Validation checks would always be up to date**. A validation library that imports OLM types (for example the ClusterServiceVersion type) means any changes introduced to OLM types will immediately be integrated into the validation library and distributed to all projects using the library.
  * **Less runtime failures**.
  * **Help with operator development**.

### **Some requirements**:
  * Written in Go
  * Imports OLM types
  * Serializes yaml into OLM types
  * To add validation that is specific to a given tool (for example [operatorhub.io’s](https://operatorhub.io/) UI), the tests would be defined in a relevant repo (here operatorhub.io’s code base for example) that calls this library

### **Phase 1 - Prototype (~4 weeks)**
The focus of this stage is to define a prototype of this library. The code for this project should live in a public repo on github. To start, the tool should serialize yaml bundles into OLM types and raise appropriate errors should this fail. The repo should expose a command line tool that imports relevant packages to validate for specific tools (these should live in the corresponding tool’s repo). The creation of such a prototype will facilitate the reception and integration of feedback necessary for phase 2. The outcome of this stage should be a demo.

### **Phase 2 - Refinement (~2 weeks)**
The prototype should now be demo-able. The focus of this stage is to demo the tool to the stakeholder teams to both create awareness about this project and receive quality feedback. Once the necessary alterations have been made to accommodate feedback, the outcome of this phase should be a PR against the OLM repo.

### **Phase 3 - Removing validation from courier (~2 week)**
The focus of this phase is to remove the verify commands of courier and encourage users of courier to use the command line tool created in phase 1 as a replacement for the verify command. Formal documentation of proper processes should be defined as part of this phase.

### **Phase 4 - Integration with SDK (stretch goal for the project)**
Once the validation library is created, the operator-sdk and/or [Operator Registry](https://github.com/operator-framework/operator-registry) could be updated to utilize the library. Alternatively the library could utilize [Operator Registry](https://github.com/operator-framework/operator-registry) methods if relevant.
Additionally, with OLM now supporting [Operator Registry](https://github.com/operator-framework/operator-registry) pods and the updated [Registry Manifest](https://github.com/operator-framework/operator-registry#manifest-format) format, courier no longer needs to build an operator bundle.

## **Outcomes for Red Hat**
  * A universally accepted definition of operator validation tests.
  * Improved developer experience brought about through the reduction of the number of tools required to build, test, and distribute an operator.
  * Stronger integration across operator-framework teams.

## **Outcomes for Intern**
  * Deep understanding of operators
  * Familiarity with Kubernetes, OpenShift, Operator-Framework
  * Experience of building an open source tool (essentially) from scratch
