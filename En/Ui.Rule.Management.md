
# Creating and Managing Business Rules

Managing systems built on conventional business rule engines is rarely easy.  
Evaluating existing, approved rules against organizational data is usually straightforward - most engines provide intuitive ways to do that.  
The real challenge lies in <b>rule management</b>: creating new rules and modifying existing ones. That’s where many business teams experience friction.

Traditionally, business rules are created in one of two ways:

- **Decision tables.**  
  Rules are defined as text, CSV, XML, or Excel files authored by business users following specific formats - either public or proprietary. Each table typically defines one or more conditions and optional actions that form a ruleset. These files are then validated, compiled, tested, and deployed by IT.

- **Graphical user interfaces (GUIs).**  
  Rules are created or modified through a graphical UI—either provided by the engine or built in-house. Such UIs often depend on scripting knowledge or rely on cascaded drop-downs that change dynamically based on user input. IT usually handles configuration and maintenance, while the business manages the rules themselves.

While other methods exist, these two dominate.

Decision tables have existed since the 1960s and have changed little since. Their core limitation is that business analysts can’t manage corporate rules without heavy IT involvement. Non-technical users typically must learn a rule definition syntax or work with rigid, technical formats.  
In such systems, Microsoft Excel © often becomes the interface of choice—and it’s not an ideal one for managing complex business logic.

The GUI approach is more appealing because it promises to separate IT from day-to-day business logic. However, building a system that handles real-world business requirements is far more difficult than assembling a few drop-downs, labels, and check boxes—no matter how sophisticated the backend logic is.

---

## The Code Effects Approach

Code Effects takes a fundamentally different path.  
Our business rule engine delivers an almost limitless graphical interface while maintaining record-setting evaluation performance.  
There are no decision tables, no predefined UI controls—everything is XML-based, web-native, and compiled for speed.  
It’s straightforward to set up and genuinely pleasant to use.

As explained in other sections, Code Effects is deliberately designed to focus on its core purpose rather than attempt to cover every possible feature of a rule-management system.  
It doesn’t include rule authorization, versioning, or debugging out of the box—you can implement those parts as needed.  
We concentrate on the features essential for successful <b>creation</b>, <b>validation</b>, and <b>evaluation</b> of business rules.

---

## Integrating Code Effects

Here’s what’s required to use Code Effects in your own product or as a stand-alone rule engine:

1. **Define your source object(s).**  
   Create the <b>class</b> or <b>Source XML</b> document that represents the data type your rules will evaluate.  
   Many organizations already have such models.  
   You can even generate them dynamically at runtime with our [FlexSource technology](/decision-automation/business-rule-flex-source) if your data structure is stored in a database. The possibilities are nearly limitless.

2. **Decide where rules are evaluated.**  
   Choose which of your systems will host rule evaluation.  
   This can be a web application, a Windows service, or an Azure service that executes your business logic.  
   With Code Effects, integration takes just a couple of assembly references and a few lines of code to evaluate a single object—or millions—against one or more rules.

3. **Implement rule handling logic.**  
   The <b>Rule Editor</b> provides everything needed to author and evaluate rules in the most effective, economical, and high-performance way available today.  
   What happens to those rules—who can access them, which ones are deployed to production, how they’re versioned, secured, or approved—is entirely up to you.  
   Code Effects gives you the authoring and evaluation capabilities; you build or update your system(s) that manage rule storage, security, and lifecycle.  
   Typical implementations store and retrieve rules in a database, apply versioning or access control, and deploy selected versions to production.  
   Our [demo projects](/decision-automation/demo-projects) include complete working examples.

---

## Design Philosophy

Code Effects is built around a simple principle:  
<b>We provide the most powerful and efficient way to author and evaluate rules. You control everything else.</b>

Our technology delivers unmatched performance, simplicity, and flexibility for building, validating, and executing rules—all through a single, unified platform.  
Your organization defines the environment around it: rule access, versioning, deployment, auditing, and governance.  

This separation of responsibility is deliberate. It ensures that Code Effects stays fast, compact, and focused on what it does best—helping your users create and execute business logic with clarity, precision, and speed.  
This approach also allows Code Effects to integrate seamlessly into systems of any scale, from small web applications to enterprise-level platforms.
