When defining an own strategy

  * should use own strategy instead of default strategy to answer the question

Hierarchical evaluation engines specifications

When calling answer on a child evaluation engine

  * should override parent aggregator with child aggregator
  * should use expressions from child and parent
  * should evaluate expressions from parent first

When calling answer on a parent evaluation engine

  * should use parent aggregator
  * should use expressions only from parent

Logging specifications

When an answer is calculated

  * should log how answer was derived

Modules specifications

When loading a module into an evaluation engine

  * should use definitions from module to answer questions too

Question answering specifications

When calling answer on evaluation engine and no aggregator is specified

  * should throw InvalidOperationException

When calling answer on evaluation engine with a parameter

  * should pass parameter to the evaluation expressions

When calling answer on evaluation engine with several defined expressions and an expression aggregator

  * should aggregate the result of all expressions into a single result

When calling answer with expressions with constraints

  * should evaluate expressions without constraints
  * should evaluate expressions with fulfilled constraints
  * should ignore expressions with constraints that are not fulfilled

Solution definition specifications

When defining inline expressions in the call to ByEvaluating

  * should evaluate all expressions for the question

When defining several expressions with a single call to ByEvaluating

  * should evaluate all expressions for the question

When defining several expressions with a single call to Solve

  * should evaluate all expressions for the question

When defining several expressions with individual calls to Solve

  * should evaluate all expressions for the question

Validation specifications

When validating invalid data

  * should return invalid validation result
  * should return validation result with violations
  * should return violations with reason set by failing rule

When validating valid data

  * should return valid validation result
  * should return validation result without violations

Validation Extensibility specifications

When validating invalid data with extended validation result

  * should return invalid validation result
  * should return validation result with violations
  * should return violations with reason set by failing rule
  * should return violations with extended data

When validating valid data with extended validation result

  * should return valid validation result
  * should return validation result without violations