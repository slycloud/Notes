# Agile Test

## Concept
* CI (Continous Integration)
* CD (Continuous Delivery)

## How to Test
1 Auto Test
  * Unit Test
  * E2E Test (End-to-end Test)
    需要长期维护，代价高，设计式样变化后也需要修改。根据需要判断是否要导入。
  * Feature Test before Release
    自动化测试环节，而不是每次编译，启动，然后手动测试。
  * TDD (Test Driven development) / BDD (Behavior Driven development)

2 Auto Build / Auto Deploy
  * Jenkins
  * CircleCI / Shippable / Wecker
    Cloud管理型的build，deploy工具。
  * AWS CodeDeploy

3 Unify Dev Environment

  统一开发环境，DB，OS等版本。

  * Chef / Ansible
  * Docker
    虚拟环境下，开发测试。

