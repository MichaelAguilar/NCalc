# NCalc  
NCalc is a mathematical expression parser for .NET. NCalc can parse any expression and evaluate the result, including static or dynamic parameters and custom functions.

  - [What Is It?](#)
  - [What Does It Do?](#)
      - [Simple Expressions](#)
      - [Evaluates .NET data types](#)
      - [Handles mathematical functional from System.Math](#)
      - [Evaluates custom functions](#)
      - [Handles unicode characters](#)
      - [Define parameters, even dynamic or expressions](#)
  - [How Do I Build It?](#)
  
### What Is It?
Nalc was created by [Sébastien Ros](https://www.codeplex.com/site/users/view/sebastienros) and offered under the MIT license via [CodePlex](https://ncalc.codeplex.com/) and [NuGet](https://www.nuget.org/packages/ncalc/). 

Sadly, the package is no longer being maintained and there are a handful of issues that are still present in this otherwise terrific software. Rather than finding another expression parser, I've sought to correct what issues I could with it and redistribute the source for community use (pursuant to the license under which it was originally released). Issues and Pull Requests are more than welcome in support of this endeavor. When time permits, I'll attempt to transcribe any open issues on CodePlex to this repository so that they can be properly tracked and addressed.

Below is an at-a-glance look at the functionality that NCalc offers and examples of how to implement it.

### What Does It Do?
##### Simple Expressions
```c#
Expression e = new Expression("2 + 3 * 5");
Debug.Assert(17 == e.Evaluate());
```

##### Evaluates .NET data types
```c#
  Debug.Assert(123456 == new Expression("123456").Evaluate()); // integers
  Debug.Assert(new DateTime(2001, 01, 01) == new Expression("#01/01/2001#").Evaluate()); // date and times
  Debug.Assert(123.456 == new Expression("123.456").Evaluate()); // floating point numbers
  Debug.Assert(true == new Expression("true").Evaluate()); // booleans
  Debug.Assert("azerty" == new Expression("'azerty'").Evaluate()); // strings
```

##### Handles mathematical functional from System.Math
```c#
  Debug.Assert(0 == new Expression("Sin(0)").Evaluate());
  Debug.Assert(2 == new Expression("Sqrt(4)").Evaluate());
  Debug.Assert(0 == new Expression("Tan(0)").Evaluate());
```

##### Evaluates custom functions
```c#
  Expression e = new Expression("SecretOperation(3, 6)");
  e.EvaluateFunction += delegate(string name, FunctionArgs args)
      {
          if (name == "SecretOperation")
              args.Result = (int)args.Parameters[0].Evaluate() + (int)args.Parameters[1].Evaluate();
      };
  
  Debug.Assert(9 == e.Evaluate());
```

##### Handles unicode characters
```c#
  Debug.Assert("経済協力開発機構" == new Expression("'経済協力開発機構'").Evaluate());
  Debug.Assert("Hello" == new Expression(@"'\u0048\u0065\u006C\u006C\u006F'").Evaluate());
  Debug.Assert("だ" == new Expression(@"'\u3060'").Evaluate());
  Debug.Assert("\u0100" == new Expression(@"'\u0100'").Evaluate());
```

##### Define parameters, even dynamic or expressions
```c#
  Expression e = new Expression("Round(Pow([Pi], 2) + Pow([Pi2], 2) + [X], 2)");

  e.Parameters["Pi2"] = new Expression("Pi * [Pi]");
  e.Parameters["X"] = 10;

  e.EvaluateParameter += delegate(string name, ParameterArgs args)
    {
      if (name == "Pi")
      args.Result = 3.14;
    };

  Debug.Assert(117.07 == e.Evaluate());
```

### How Do I Build It?
Added ILMerge to build process to remove unnecessary Antlr3 reference
