# NCalc  
NCalc is a mathematical expression parser for .NET. NCalc can parse any expression and evaluate the result, including static or dynamic parameters and custom functions.

  - [What Is It?](#what-is-it)
  - [What Does It Do?](#what-does-it-do)
      - [Simple Expressions](#simple-expressions)
      - [Evaluates .NET data types](#evaluates-net-data-types)
      - [Handles Mathematical Functions From System.Math](#handles-mathematical-functions-from-systemmath)
      - [Evaluates Custom Functions](#evaluates-custom-functions)
      - [Handles Unicode Characters](#handles-unicode-characters)
      - [Define Parameters, Even Dynamic or Expressions](#define-parameters-even-dynamic-or-expressions)
  - [How Do I Build It?](#how-do-i-build-it)

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

##### Handles Mathematical Functions From System.Math
```c#
  Debug.Assert(0 == new Expression("Sin(0)").Evaluate());
  Debug.Assert(2 == new Expression("Sqrt(4)").Evaluate());
  Debug.Assert(0 == new Expression("Tan(0)").Evaluate());
```

##### Evaluates Custom Functions
```c#
  Expression e = new Expression("SecretOperation(3, 6)");
  e.EvaluateFunction += delegate(string name, FunctionArgs args)
      {
          if (name == "SecretOperation")
              args.Result = (int)args.Parameters[0].Evaluate() + (int)args.Parameters[1].Evaluate();
      };
  
  Debug.Assert(9 == e.Evaluate());
```

##### Handles Unicode Characters
```c#
  Debug.Assert("経済協力開発機構" == new Expression("'経済協力開発機構'").Evaluate());
  Debug.Assert("Hello" == new Expression(@"'\u0048\u0065\u006C\u006C\u006F'").Evaluate());
  Debug.Assert("だ" == new Expression(@"'\u3060'").Evaluate());
  Debug.Assert("\u0100" == new Expression(@"'\u0100'").Evaluate());
```

##### Define Parameters, Even Dynamic or Expressions
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
In order to cut down on the dll size and make it easier to reference in other projects, I've used ILMerge to incorporate the Antlr3 functionality into the NCalc .dll that is built. Doing this requires the following steps:  

1. Install [ILMerge](http://www.microsoft.com/en-us/download/details.aspx?id=17630).
2. Create the file `Ilmerge.CSharp.targets` in your MSBuild directory.
  1. x86 Platforms: C:\Program Files\MSBuild
  2. x64 Platforms: C:\Program Files (x86)\MSBuild
  3. See the [ILMerge Sample](https://github.com/MichaelAguilar/NCalc/wiki/ILMerge-Sample-File) on the Wiki for details on the file structure.
3. Make the following changes to your csproj files via your text editor of choice:
  1. Add the `<Ilmerge>True</Ilmerge>` tag to the Antlr reference (if it doesn't exist already).
    * Note that if you add/remove references from Visual Studio you will have to manually add this tag back.
  3. Replace `Import Project="$(MSBuildBinPath)\Microsoft.CSharp.targets" />` with `<Import Project="$(MSBuildExtensionsPath)\Ilmerge.CSharp.targets" />`.
4. Build as you would normally.
