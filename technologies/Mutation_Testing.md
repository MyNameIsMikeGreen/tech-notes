Mutation Testing
================

# Overview
Mutation testing involves introducing certain changes to code to determine whether these changes are detected by existing tests. In effect, it is testing tests.

If a change, known as a "mutant" doesn't cause any test case to fail, the mutation test has failed. However, if some test does fail, the mutant is said to have been "killed" and the mutation test has passed.

Mutation testing complements TDD well. With TDD, tests act as a safety net; the developer has confidence that a large refactor is safe if the tests continue to pass. However, beyond developer experience and common sense, it is often difficult to know how safe the safety net is. Mutation testing attempts to reveal holes in the net.

# Mutation Test Lifecycle
Following writing code and unit tests as normal, the mutation testing engine will execute the following operations:

1. Run all unit tests
  * Ensure they pass under normal conditions
2. Apply a mutation operator and recompile the code
  * Change a method call
  * Replace one comparison operator with another
  * Change a boolean
  * etc...
3. Re-run all unit tests against the mutated code
4. Pass if one or more unit tests fail, fail if unit tests continue to pass.

# Test Suitability
Mutation testing is only applicable to tests which execute the same way every time. They must not be tests which change any sort of global state. Therefore, it is likely not suitable to run mutation testing on end-to-end or integration test cases. In general, unit tests are the most suitable tests to mutate.

# Test Results
In the simplest terms, if a mutation is identified ("killed") during testing, this is good; if a mutation is not identified ("survives") during testing, this is bad. However, the significance of the survival of a mutant should be assessed on a case-by-case basis.

It is perfectly reasonable for some mutants to survive in some circumstances, for instance:
* The mutant was applied on useless/unused code
* The mutant was applied on code which only impacts performance, not results
  * e.g. `if (runFaster()) { speedUp() }`

To fully understand the results of a mutation test, the developer should look at the code that was under test and assess whether an issue has truly been identified.

# Usage

For Java-based projects, Pitest is the most commonly used tool for mutation testing. It is easily used with existing projects in a variety of ways:

* InteliJ Plugin (`PIT mutation testing Idea plugin`)
  * Runs Pitest within the IDE itself.
  * Facilitates the creation of a `PIT Runner` run configuration.
* Maven Plugin
  * To add Pitest to a maven project, simply add the plugin details to the pom.xml:
    ```xml
    <plugin>
      <groupId>org.pitest</groupId>
      <artifactId>pitest-maven</artifactId>
      <version>LATEST</version>
    </plugin>
    ```
  * Run Pitest with `mvn org.pitest:pitest-maven:mutationCoverage`.
  * The parameters of the tests can be altered using a variety of tags in this plugin block. See the official documentation for details.

# Resources
* ["Mutation Testing: Case Studies on Spring Boot APIs" Presentation by Pivotal](https://www.youtube.com/watch?v=88fDcPurp-Y)
* https://pitest.org/
