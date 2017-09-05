# dotnet Kata
## Based on [Roy Osherove](http://osherove.com/tdd-kata-1/)

---

## The Basics

- Try not to read ahead.
- Do one task at a time. The trick is to learn to work incrementally.
- Make sure you only test for correct inputs. there is no need to test for invalid inputs for this kata

---

Create a new Library called Calculator with a corresponding nUnit test project

![Press Down Key](assets/down-arrow.png)

+++

## Create xunit test project
```bash
 dotnet new xunit -n Calculator.Test
```

## Create library project
```bash
dotnet new classlib -n Calculator
```

+++
## Convert it to nUnit
```bash
cd Calculator.Test
dotnet add package NUnit --version 3.7.1
dotnet add package NUnit3TestAdapter --version 3.8.0
dotnet remove package xunit
dotnet remove package xunit.runner.visualstudio
```

### Add a reference to your library in Calculator.Test.csproj
```xml
<ItemGroup>
    <projectreference Include="..\Calculator\Calculator.csproj" />
</ItemGroup>
```
+++
Final Calculator.Test.csproj
```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netcoreapp1.1</TargetFramework>
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="15.0.0" />
    <PackageReference Include="NUnit" Version="3.7.1" />
    <PackageReference Include="NUnit3TestAdapter" Version="3.8.0" />
  </ItemGroup>
  <ItemGroup>
    <projectreference Include="..\Calculator\Calculator.csproj" />
  </ItemGroup>
</Project>
```

+++

## Test it
```bash
dotnet restore
dotnet test
```

### Should error with 
```bash
λ dotnet test
Build started, please wait...
UnitTest1.cs(2,7): error CS0246: The type or namespace name 'Xunit' could not be found (are you missing a using directive or an assembly reference?) [C:\Users\aheld\Documents\GitHub\code-presenting\project\Calculator.Test\Calculator.Test.csproj]
<<<snip>>>
```

+++

### Test class is still xUnit.  Move to the next and write an nUnit test

---
### Create a Test for a method in the Calculator class such as  ```int Add(string numbers)```

- Passing in an Empty String should return 0
- Remember to start with a failing test

![Press Down Key](assets/down-arrow.png)

+++


Calculator.Test\CalculatorTest.cs
```csharp
using NUnit.Framework;
using NUnit.Framework.Constraints;

namespace Calculator.Test
{
    [TestFixture]
    public class UnitTest1
    {
        [Test]
        public void Test1()
        {
            var c = new Calculator();
            Assert.That( c.Add(""), Is.EqualTo(0));
        }
    }
}
```

+++

Calculator\Calculator.cs
```csharp 
using System;

namespace Calculator
{
    public class Calculator
    {
        public int Add(String input){
            return -1;
        }
    }
}
```
---

## Passing in a single number as a string returns that number.
### Add(“1”) = 1
### Add(“2”) => 2 
### etc...

---

## Passing in a pair of numbers yields the sum
### Add(“1,2”) = 3
### Add(“2,3”) => 5 
### etc...

### Remember to refactor often

---
## Sum an unknown amount of numbers

---
## With Stable Production Code -  __Refactor tests__

---
## Allow the Add method to handle new lines between numbers (instead of commas).
- the following input is ok:  “1\n2,3”  (will equal 6)
- the following input is NOT ok:  “1,\n” (not need to prove it -  just clarifying)
---

## With Stable Production Code -  __Refactor tests__

+++

### Static Fixtures
```csharp
   [TestFixtureSource("_fixtureArgs")]
   public class AddTest{

    public AddTest(string input, int expected){
        Assert.AreEqual(calculator.Add(input), expected);
    }

     static object [] _fixtureArgs = {
             new object[] { "1,2", 3}
     }
   }
```
---
## Support different delimiters to change a delimiter, 
- the beginning of the string will contain a separate line that looks like this:   
- “//[delimiter]\n[numbers…]” 
  - for example “//;\n1;2” should return three where the default delimiter is ‘;’ .
  - the first line is optional. all existing scenarios should still be supported

---

## With Stable Production Code -  __Refactor tests__

+++

### Generated Fixtures, try to use this:
```csharp
// pesudocode
 [TestFixtureSource(typeof(CrTestCaseData))]
   class TestCaseData: IEnumerable
    {
        private static readonly TestInputs[] FixtureArgs = {
            new TestInputs("1,2", 3) };

        public IEnumerator GetEnumerator()
            { return GetEnumeratorInternal(); }
        private IEnumerator GetEnumeratorInternal(){
            foreach (var crTestInput in FixtureArgs)
            {
                yield return new object[]
                    {TestInputs.Input, TestInputs.Expected};
            }
        }
    }

   public struct TestInputs
    {
        public TestInputs(string input, int expected)
        {
            Input = input;
            Expected = expected;
        }
        
        public string Input;
        public int Expected;
    }
```
---

## Calling Add with a negative number will throw an exception “negatives not allowed”
- Exception message should include the negative that was passed.
- Exception message should include multiple negative values, if multiples are passed 

--- 

## Bonus Refactor

Generate tests based on all permutations when given an expected Int, delimeters and number of inputs.

TestPermutations(3, {',','\n','$"}, 2)
should test:
["1,2", "2,1", "1\n2", "2\n1", "3,0", "3\n0",
"\\$\n1$2", "\\$\n$n1", "\\$\n1$2", "\\$\n2$1", "\\$\n3$0", "\\$\n3$0"]

---
## Bonus Round
- bigger than 1000 should be ignored, so adding 2 + 1001  = 2
- Delimiters can be of any length with the following format:  “//[delimiter]\n” for example: “//[***]\n1***2***3” should return 6
- Allow multiple delimiters like this:  “//[delim1][delim2]\n” for example “//[*][%]\n1*2%3” should return 6.
- make sure you can also handle multiple delimiters with length longer than one char

--

## Double Bonus Refactor

Generate tests based on all permutations when given an expected Int, delimeters.  No need to specify number of inputs

TestPermutations(3, {',','\n'})
should test:
["1,2", "2,1", "1\n2", "2\n1", "3,0", "3\n0", "3"]



