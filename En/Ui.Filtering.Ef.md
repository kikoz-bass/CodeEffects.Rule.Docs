
# T-SQL Auto-Generation with EF Core

This topic assumes that you already understand how to [manage](/decision-automation/business-rule-management) business rules in Code Effects. In [Rule-Based Data Filtering](/decision-automation/business-rule-data-filtering), [evaluation type](/decision-automation/business-rule-evaluation-type) rules are used as <em>filters</em>; [execution type](/decision-automation/business-rule-execution-type) rules are not used in this scenario. Any time this article refers to a <em>filter</em>, it means an evaluation type rule that has been created in the [Rule Editor](/decision-automation/business-rule-editor) using its `Filter` [mode](/decision-automation/business-rule-editor-modes) and saved as [Rule XML](/decision-automation/business-rules-storage).

The core principles of using Code Effects data filtering with EF Core are implemented in the <em>Data Filtering</em> [demo project](https://github.com/orgs/codeeffects-software/repositories).

Using Code Effects, you can attach rule-based data filters (the equivalent of `WHERE` clauses) of arbitrary complexity to your LINQ queries. The examples below show how to apply these filters to Entity Framework Core queries. The same pattern is used by the `Filter(..)` [extension method](/decision-automation/rule-engine-ruleextensions) for rule-based data filtering.

> In all examples, assume that `filterXml` var holds the XML of a previously saved evaluation type rule and that you have added a reference to the `Code.Effects.Engine.dll` assembly and `using CodeEffects.Rule.Engine;` directive.

## Filter(..) and Deferred Execution

Given a rule stored as XML, the `Filter(..)` method converts that rule into an expression tree and applies it to an `IQueryable<T>` value.

```csharp
public class TestClass
{
    public int Id { get; set; }
}

// "Get items where Id is less than [3]"
string filterXml = GetFilterXmlFromYourStorage();

TestClass[] array =
{
    new TestClass { Id = 1 },
    new TestClass { Id = 2 },
    new TestClass { Id = 3 },
    new TestClass { Id = 6 },
    new TestClass { Id = 8 }
};

var result = array.AsQueryable().Filter(filterXml);

foreach (var item in result)
{
    Console.WriteLine(item.Id);
}
```

The output of this example is:

```
1
2
```

Because `Filter(..)` returns an `IQueryable<T>`, no code executes until the sequence is enumerated. This deferred execution is what allows EF Core to translate the expression tree into T-SQL.

## Applying Filters to EF Core Queries

The main benefit of Code Effects [data filtering](/decision-automation/business-rule-data-filtering) appears when filters are applied to EF Core queries. EF Core translates the combined query (your LINQ expression together with the rule-based predicate) into T-SQL and executes that SQL on the database server.

Assume a standard EF Core context:

```csharp
public class AdventureWorksContext : DbContext
{
    public AdventureWorksContext(DbContextOptions<AdventureWorksContext> options) : base(options) { }

    public DbSet<Product> Products => Set<Product>();
}
```

You can enable SQL logging in `OnConfiguring`:

```csharp
protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
{
    if (!optionsBuilder.IsConfigured)
    {
        optionsBuilder
            .UseSqlServer("<connection-string>")
            .LogTo(Console.WriteLine)
            .EnableSensitiveDataLogging();
    }
}
```

Applying a filter to a simple query is straightforward:

```csharp
using var db = new AdventureWorksContext(options);

// "Get records where Product.Name starts with 'A'"
string filterXml = GetProductNameFilterXml();

var products = db.Products.Filter(filterXml);

foreach (var product in products)
{
    Console.WriteLine($"{product.ProductId,3}: {product.Name}");
}
```

To inspect the generated SQL:

```csharp
var query = db.Products.Filter(filterXml);
Console.WriteLine(query.ToQueryString());
```

## Filters That Reference Navigation Properties

Filters may reference properties of related entities. For example, consider a filter that uses `Product.ProductModel.Name` instead of `Product.Name`:

```csharp
// "Get records where ProductModel.Name starts with 'A'"
string filterXml = GetProductModelFilterXml();

var query = db.Products.Filter(filterXml);

foreach (var product in query)
{
    Console.WriteLine($"{product.ProductId,3}: {product.Name}");
}
```

EF Core automatically generates the required join between `Product` and `ProductModel`. The rule-based predicate becomes part of the database expression tree and is included in the final SQL.

## Including Related Entities

If your output requires values from the related entity (for example, `ProductModel.Name`), include the navigation property:

```csharp
string filterXml = GetProductModelFilterXml();

var query = db.Products
    .Include(p => p.ProductModel)
    .Filter(filterXml);

foreach (var product in query)
{
    Console.WriteLine($"{product.ProductId,3}: {product.Name} ({product.ProductModel!.Name})");
}
```

EF Core generates a single `SELECT` with a join to `ProductModel` and applies the rule-based predicate in the `WHERE` clause.

## Projecting After Filtering

If you want a projection that does not match the entity shape, apply `Filter(..)` to the entity set first, then project from the filtered query:

```csharp
string filterXml = GetProductModelFilterXml();

var filtered = db.Products.Filter(filterXml);

var productsWithModelName =
    from product in filtered
    select new
    {
        product.ProductId,
        product.Name,
        ModelName = product.ProductModel!.Name
    };

foreach (var product in productsWithModelName)
{
    Console.WriteLine($"{product.ProductId,3}: {product.Name} ({product.ModelName})");
}
```

This approach ensures that the filter remains independent of your projection while still allowing EF Core to translate the complete query into a single T-SQL statement.

## Null Handling

By default, the `Filter(..)` [extension method](/decision-automation/rule-engine-ruleextensions) performs strict `null` checking during rule evaluation. If a rule tries to access a member of a `null` object, the default behavior is to throw an exception to indicate that the data surface path is invalid for the given record.

To instead use standard C# `null` semantics, where accessing a nested property on a `null` reference simply returns `null` and the rule evaluates to `false`, you must call the overload of `Filter(..)` that accepts an [`EvaluationParameters`](/decision-automation/rule-common-models-evaluationparameters) object and set its `PerformNullChecks` property to `false`.

Example:

```csharp
var parameters = new EvaluationParameters
{
    PerformNullCheck = false   // Allow natural C# null propagation
};

var filtered = db.Products.Filter(ruleXml, parameters);
```

When `PerformNullChecks` is set to `false`, EF Core will translate the full expression tree using normal C# null-propagation rules:

- If any object in the property chain is `null`,
- the result of the access is `null`,
- and the filter condition evaluates to `false` for that row,
- without throwing an exception.

This behavior is often useful when filtering data that may contain optional or partially populated navigation properties, such as left-joined relationships or soft-deleted entities.

## Notes and Limitations

- <b>Translation limits</b>. Code Effects produces a valid LINQ expression tree, but EF Core may not translate <b>every</b> .NET operation in that tree. Some constructs may require modification of the rule or [in-memory evaluation](/decision-automation/business-rule-linq-to-object-support).
- <b>Case sensitivity</b>. String comparison behavior depends on your database collation.
- <b>Query debugging</b>. Use `ToQueryString()` to view the SQL for a particular query. Use `LogTo(..)` for broader logging during development.
- <b>Deferred execution</b>. `Filter(..)` does not execute the query; it only appends a predicate to the expression tree. Execution occurs when the query is enumerated.

## Summary

In EF Core, Code Effects `Filter(..)` integrates seamlessly with the provider:

- [Evaluation type](/decision-automation/business-rule-evaluation-type) rules are created and stored as [XML](/decision-automation/business-rules-storage) using the [Rule Editor](/decision-automation/business-rule-editor).
- `Filter(..)` converts rule XML into a LINQ expression tree and attaches it to an `IQueryable<T>`.
- EF Core translates the combined query into T-SQL, including joins for navigation properties.
- The database server executes the filtered query, and only matching records are returned.
- Projections, includes, and diagnostics remain fully under your control, while rule authors control the filtering logic.