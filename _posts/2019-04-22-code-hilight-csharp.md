---
layout: post
title: "Code syntax highlight"
date: 2019-04-22
tags: [test, örnek]
---

Lorem ipsum bılı bılı tık tık

{% highlight python %}
x = ('a', 1, False)

def foo():
    c = x[1]
    print("deneme bir iki")
{% endhighlight %}

Syntax highlight için [lexer için dil listesi](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers "Diller") inline link.

Ayrıca [markdown için](https://mycyberuniverse.com/syntax-highlighting-jekyll.html "link") tıklayın.

## Örnek javascript mesela

```javascript
var rows = prompt("How many rows for your multiplication table?");
var cols = prompt("How many columns for your multiplication table?");
if(rows == "" || rows == null)
    rows = 10;
if(cols== "" || cols== null)
    cols = 10;
createTable(rows, cols);

function createTable(rows, cols) {
    var j=1;
    var output = "<table border='1' width='500' cellspacing='0'cellpadding='5'>";
    
    for(i=1;i<=rows;i++) {
    	output = output + "<tr>";
        while(j<=cols) {
  		  output = output + "<td>" + i*j + "</td>";
   		  j = j+1;
   		}
   		output = output + "</tr>";
   		j = 1;
    }
    output = output + "</table>";
    document.write(output);
}
```

## Örnek Csharp

```csharp
var names = new List<string>() { "John", "Tom", "Peter" };
foreach (string name in names)
{
    if (name == "Tom")
    {
        continue;
    }
    Console.WriteLine(name);
}
```

## örnek C
```c
#include <stdio.h>

int main()
{
    int a;
    long b;
    long long c;

    double e;
    long double f;


    printf("Size of int = %ld bytes \n", sizeof(a));
    printf("Size of long = %ld bytes\n", sizeof(b));
    printf("Size of long long = %ld bytes\n", sizeof(c));

    printf("Size of double = %ld bytes\n", sizeof(e));
    printf("Size of long double = %ld bytes\n", sizeof(f));

    return 0;
}
```
