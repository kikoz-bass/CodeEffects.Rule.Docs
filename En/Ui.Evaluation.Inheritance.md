
# Rule Evaluation and Object Inheritance

The Code Effects business rules engine supports evaluating descendant [source object](/decision-automation/business-rule-source-object). To illustrate this concept, create one base class and two classes that inherit from it:

```csharp
using System;

public class MyBase
{
	public int ID { get; set; } = 0;
	public string Name { get; set; }

	public MyBase() { }
}

public class MySourceOne : MyBase
{
	public DateTime Date { get; set; } = DateTime.MinValue;

	public MySourceOne() { }
}
public class MySourceTwo : MyBase
{
	public TimeSpan Time { get; set; } = TimeSpan.MinValue;

	public MySourceTwo() { }
}
```

Assume we also have an [evaluation type](/decision-automation/business-rule-evaluation-type) rule that uses only the base class’ fields, like this:

> Check if ID is greater than 0 and Name starts with A

If we define instances of all three classes, the `Evaluator` can evaluate each instance against this rule because it relies solely on fields declared in the base class, and those fields are available to all of its descendants:

```csharp
using CodeEffects.Rule.Engine;

var zero = new MyBase { ID = 0, Name = "abc" };
var one = new MySourceOne { ID = 1, Name = "xyz" };
var two = new MySourceTwo { ID = 2, Name = "abc" };

// Retrieve Rule XML from your storage
string ruleXml = GetRuleXmlFromSomewhere();

var evaluator = new Evaluator(typeof(MyBase), ruleXml);

bool success = evaluator.Evaluate(zero); // Returns False
success = evaluator.Evaluate(one);       // Returns False
success = evaluator.Evaluate(two);       // Returns True
```

Notice that we created an instance of the `Evaluator` using the base type, yet we were able to pass instances of all three classes for evaluation without any exceptions.