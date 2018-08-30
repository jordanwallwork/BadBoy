# BadBoy - Interpreter for POC programming language based on HTML

Inspired by [/u/BadBoy6767](https://www.reddit.com/r/ProgrammerHumor/comments/9b8ylg/these_americans/e51vbum/?context=8&depth=9), so cheers for that...

I'm hoping to prove his point by using only valid HTML tags, though I doubt anything written this way would have much chance of getting through a linter unscathed!

## Language Reference

### Data types

 - Null: `<br/>`
 - Boolean (bool): `<b>true</b>` or `<b>false</b>`
 - String (string): `<span>This is a string</span>`
 - Number (number): `<i>123</i>`
 - Datetime (datettime): `<time datetime="2018-08-30 09:00"></time>` or `<time>2018-08-30 09:00</time>`

### Comments

HTML comments: `<!-- this is a comment -->

### Variables

Use `<var>` to declare / use variables. `id` attribute is optional when declaring variables (though skipping this is only really useful when returning from a function)

#### Declaring Variables

Use `<var>` with content to declare a variable.

    <var id="myVariable"><span>This is a string</span></var>

_is equivalent to `string myVariable = "This is a string"`_

#### Using variables

An empty tag will be used as a reference to a variable, named according to its `id` attribute. If a variable with that name doesn't exist, value will be null (`<br/>`). Within the scope of this piece of shit POC, all variables will be global (not a requirement of the language, it's just easier)

    <var id="myVariable"></var>
    
You can assign one variable to another using

    <var id="var1">1</var>
    <var id="var2><var id="var1"></var></var>
    
_equivalent to `int var1 = 1; int var2 = var1;`_

### Functions

#### Defining functions

Define a function using `<aside>`. `id` attribute required for function name. Optionally specify return type with `class` attribute; if this is missing then the function will be void.

There's no return statement; a function will return the last expression that was evaluated.

Function arguments are specified using `<input>` tags. These must be the first things in the function. Order does not matter, neither when defining functions or calling them. The following table maps which input types correlate to which variable datatype:

| Input | Datatype |
| --- | --- |
| `<input type="checkbox">` | bool |
| `<input type="text">` | string |
| `<input type="number">` | number |
| `<input type="datetime-local">` | datetime |

    <aside id="sayHello" class="string">
        <input type="text" name="name" />
        <span>Hello <var id="name"></var></span>
    </aside>
    
_equivalent to:_

    string sayHello(string name) {
        return "Hello " + name;
    }

#### Invoking functions

Use `<a>` to invoke a function. The `href` attrubute specifies the function name

    <a href="myFunction"></a>
    
_equvalent to `myFunction()`_

#### Passing parameters

There are two possible ways to invoke a function with parameters. One is more terse, but allows only passing in predefined variables, and this is by passing in the variable names as a querystring in the `href` attribute.

    <var id="myName"><span>Jordan</span></var>
    <var id="helloString"><a href="sayHello?name=myName"></a></var>
    
_equivalent to `string helloString = sayHello("Jordan")` (or more literally: `string myName = "Jordan"; string helloString = sayHello(myName)`)_

**Alternatively** you can pass the parameters using `<param>` tags. This is obviously more verbose, but allows you to pass in non-parameters.

    <a href="sayHello">
        <param name="name"><span>Jordan</span></param>
    </a>
    
    _equivalent to `sayHello("Jordan")`_
    
### Control Flow

Everything in the language will be a switch statement, rather than if/else (although of course if/else can be achieved using simply a switch with a single condition and a default case).

This is marked up using the `<select>` tag, with `<option>` tags for branches.

    <var id="myCondition"><b>true</b></var>
    <select value="myCondition">
        <option value="true">
            <!-- do something -->
        </option>
        <option>
            <!-- has no value, so is the default case -->
            <!-- if no default case then just acts like standard `if` -->
        </option>
    </select>
    
_equivalent to:_

    bool myCondition = true;
    if (myCondition) {
        // do something
    } else {
        // has no value, so is the default case
        // if no default case then just acts like standard `if`
    }
    
Adding more `<option>` tags makes it function like switch [though there is no fallthrough]:

    <var id="myName"><span>Jordan</span></var>
    <select value="myName">
        <option value="Jordan"><a href="func1"></a></option>
        <option value="Nadroj"><a href="func2"></a></option>
        <option><a href="func3"></a></option>
    </select>
    
_equivalent to:_

    string myName = "Jordan";
    switch (myName) {
        case "Jordan": func1(); break;
        case "Nadroj" func2(); break;
        default: func3(); break;
    }
