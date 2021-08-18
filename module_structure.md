# Module Structure - Guidelines

A module is a container for multiple resources that are used together. The `.tf` files in your working directory when you run `terraform plan` or `terraform apply` together form the root module. That module may call other modules and connect them together by passing output values from one to input values of another.

A complete example of a module following the standard structure is shown below.

```
├── .github/
│   ├── workflows/
│   │   ├── workflow_file1.yml
│   │   ├── .../
├── README.md
├── versions.tf
├── variables.tf
├── main.tf
├── outputs.tf
├── module.yaml
├── .pre-commit-config.yaml
├── modules/
│   ├── nestedA/
│   │   ├── README.md
│   │   ├── variables.tf
│   │   ├── main.tf
│   │   ├── outputs.tf
│   ├── nestedB/
│   ├── .../
├── examples/
│   ├── exampleA/
│   │   ├── main.tf
│   ├── exampleB/
│   ├── .../
├── LICENSE
├── CONTRIBUTING.md
├── CHANGELOG.md
├── test/
│   ├── test_file1.go/
│   ├── .../

```

To define a module, create a new directory for it and place one or more `.tf` files inside just as you would do for a root module. Terraform can load modules either from local relative paths or from remote repositories.

`Modules use:`

* `Input variables`:  To accept values from the calling module.
* `Output values`  :  To return results to the calling module, which it can then use to populate arguments elsewhere.
* `Resources`      :  To define one or more infrastructure objects that the module will manage.

## Module directory structure :

* `Root module`: Every Terraform configuration has at least one module, known as its root module, which consists of the resources defined in the .tf files in the main working directory.

* `Child module`: Rot modulecan call other modules. A module that has been called by another module is often referred to as a child module.

* `module.yaml` : This file captures meta data about the module.

* `Modules Directory :` Use to capture all the nested modules under `/modules` sub-directory. Each nested module should follow following directory structure
  
  ```
  ├── README.md
  ├── main.tf
  ├── variables.tf
  ├── outputs.tf
  
  ```

  `main.tf`, `variables.tf` and `outputs.tf` these are the recommended filenames for a minimal module, even if they're empty. `main.tf` should be the primary entrypoint. 
  
  For a simple module, this may be where all the resources are created. For a complex module, resource creation may be split into multiple files but any nested module calls should be in the main file. `variables.tf` and `outputs.tf` should contain the declarations for variables and outputs, respectively. 
  
  `README.md` shoud have a table capturing input and output varaibles decription, whether its a mandatory argument or not and default values if present.

* `examples Directory:` Examples of using the module should exist under the examples/ subdirectory at the root of the repository. Each example should have a README to explain the goal and usage of the example. Examples for submodules should also be placed in the root examples/ directory.

  If you want to include an example for how this module can be used in combination with other resources, put it in an examples directory. Each sub-dorectory under `/examples` should follow following directory structure
  
  ```
  ├── README.md
  ├── main.tf
  ├── variables.tf
  ├── outputs.tf
  
  ```

* `Github actions` - GitHub Actions makes it easy to automate all the software workflows. As part of github actions we add pre-commit hooks which are being used to validate the PR before it gets merged onto main branch. It also being used to run the test cases perodically to assure the quality of both templates and the modules.

* `test directory` - Use terratest to write the test cases under `test` directory, which will be executed by the github actions using a scheduler on a periodic basis. Following link can be used as a reference to write a [test file](https://github.com/terraform-ibm-modules/terraform-ibm-iam/blob/main/test/access-group/access_group_test.go). To execute terratest files locally, refer the [document here](terratest.md)

* `.gitignore` -  Use this file to specify intentionally the untracked files that Git should ignore.

* `.pre-commit-config.yaml` - Used for identifying simple issues before submission to code review. We run our hooks on every commit to automatically point out issues in code such as missing semicolons, trailing whitespace, and debug statements. 

* `README :` The root module and any nested modules should have README files. This file should be named README.md. There should be a description of the module and what it should be used for. 

* `LICENSE :` The license under which this module is available. If you are publishing a module publicly, many organizations will not adopt a module unless a clear license is present. We recommend always having a license file, even if it is not an open source license.

* `CHANGELOG.md` A changelog is a log or record of all notable changes made to a your project. This info includes
  
  ```
  ├── Release Tag
  ├── Release Date
  ├── Release Notes
  ├── Features
  ├── Enhancements
  ├── Bugs
  
  ```
  
* `CONTRIBUTING.md` Used for the purpose of describing how others can contribute to your project.
  
