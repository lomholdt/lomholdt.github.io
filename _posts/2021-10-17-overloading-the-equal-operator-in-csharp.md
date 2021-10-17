---
title: Overloading the == operator in C#
---



I just wanted to take 2 minutes of your time to show you a funny little quirk that resides in C# >= 7.0 that you might not know about, or at least I didn't.

It's all about overloading the `==` operator. If you have ever done a null check in your code, it might have looked something like

```csharp
if (person == null)
{
    throw new ArgumentNullException(nameof(person));
}
```

And in fact this is what VS2017 auto suggests in many cases. To a long extend this is just fine. The issue is that you are trusting the object to have not overloaded the ==operator. Ouch!
Consider the following example:

```csharp
class Person
{
    public string FirstName { get; private set; }

    public string LastName { get; private set; }

    public Person(string firstName, string lastName) => (FirstName, LastName) = (firstName, lastName);

    public static bool operator ==(Person a, Person b)
    {
        // This creates funky results
        if (object.ReferenceEquals(b, null))
        {
            return true;
        }
        return a.FirstName == b.FirstName && a.LastName == b.LastName;
    }

    public static bool operator !=(Person a, Person b)
    {
        return !(a == b);
    }

    public override bool Equals(object other)
    {
        return other is Person && this == (Person)other;
    }

    public override int GetHashCode()
    {
        return base.GetHashCode();
    }

    public override string ToString()
    {
        if (object.ReferenceEquals(this, null))
        {
            return "null";
        }
        return $"{this.FirstName} {this.LastName}";
    }
}
```

Person.csThe overloaded operator can be doing some silly checks that makes it useless. In fact, the correct way of doing a null check is by using the is operator. And don't take my word for it, I heard it directly from the mouth of Mads Torgersen and Jon Skeet. And they should know if any should know.
Lets prove my point using the example above. We create some new `Person` instances so we can try to compare.

```csharp
class Program
{
    static void Main(string[] args)
    {
        var person1 = new Person("Jonas", "Lomholdt");
        var personN = new Person("Jonas", "Lomholdt");
        var person2 = new Person("Mike", "Tyson");
        Person person3 = null;

        // Show that comparing instances is working

        var expected = true;
        var actual = Person1 == PersonN; // true

        var expected = false;
        var actual = Person1 == Person2; // False

        var expected = true;
        var actual = Person1 != Person2; // true

        var expected = true;
        var actual = Person3 is null; // true

        var expected = true;
        var actual = Person3 == null; // true

        // Here comes the fun part

        var expected = false;
        var actual = Person1 == null; // true
        // What?! This should be false. Person1 is instantiated

        // Using the is operator however works just fine every time.
        var expected = false;
        var actual = Person1 is null; // false
    }
}
```

Notice how the [is operator](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/is) works fine. But the `==` is messed up. So in the future, you might as well do the following to prevent mistakes. The is operator is always safe to use like this since [*it cannot be overloaded*](https://docs.microsoft.com/en-us/dotnet/csharp/language-reference/operators/operator-overloading#overloadable-operators).

```csharp
if (person is null)
{
    throw new ArgumentNullException(nameof(person));
}
```

To negate it ~~there is no nice way like the above, but~~ you can do one of the following. Either is a matter of taste.

```csharp
if (person is not null)
{
    throw new ArgumentNullException(nameof(person));
}

ArgumentNullException.ThrowIfNull(person);

if (!(person is null))
{
    throw new ArgumentNullException(nameof(person));
}

if (person is null == false)
{
    throw new ArgumentNullException(nameof(person));
}
```

> Side note: I initially called this article Overriding the == operator in C#, but in fact only methods are overridden. When we are dealing with operators they are overloaded. I have updated the article to reflect that fact.

> Update (2021-10-17): There's now a nice way to negate the `is` operator with the `not` operator.
