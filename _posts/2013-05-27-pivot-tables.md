---
layout: post
title: "Pivot Tables in SQL"
tags: ["sql", "mssql", "pivot", "tables", "ssrs"]
---

<div class="message">
I was working on SSRS to generate a report for a client. But instead of the conventional way, they'd like what was data in the rows to be displayed as column name.
</div>

Assuming the problem is same as the following. The inventory system holds a list of products for each client, and tagged by different categories.

{% highlight sql %}
CREATE TABLE Inventory(ClientId INT, Category NVARCHAR(10), Product NVARCHAR(10))

INSERT INTO Inventory VALUES (1, 'Fruit', 'Banana')
INSERT INTO Inventory VALUES (1, 'Fruit', 'Apple')
INSERT INTO Inventory VALUES (1, 'Fruit', 'Orange')
INSERT INTO Inventory VALUES (1, 'Fruit', 'Kiwi')
INSERT INTO Inventory VALUES (1, 'Fruit', 'Pinapple')
INSERT INTO Inventory VALUES (1, 'Fruit', 'Mango')
INSERT INTO Inventory VALUES (1, 'Fruit', 'Grape')
INSERT INTO Inventory VALUES (2, 'Fruit', 'Acerola')
INSERT INTO Inventory VALUES (2, 'Fruit', 'Coconut')
INSERT INTO Inventory VALUES (2, 'Fruit', 'Watermelon')
INSERT INTO Inventory VALUES (2, 'Vegetable', 'Cabbage')
INSERT INTO Inventory VALUES (2, 'Vegetable', 'Carrot')
INSERT INTO Inventory VALUES (2, 'Vegetable', 'Lettuce')
INSERT INTO Inventory VALUES (2, 'Vegetable', 'Onion')
INSERT INTO Inventory VALUES (2, 'Meat', 'Pork')
INSERT INTO Inventory VALUES (2, 'Meat', 'Lamb')
INSERT INTO Inventory VALUES (3, 'Fruit', 'Lemon')
INSERT INTO Inventory VALUES (3, 'Fruit', 'Straberry')
INSERT INTO Inventory VALUES (3, 'Vegetable', 'Pea')
INSERT INTO Inventory VALUES (3, 'Vegetable', 'Corn')
INSERT INTO Inventory VALUES (3, 'Vegetable', 'Broccoli')
INSERT INTO Inventory VALUES (3, 'Vegetable', 'Potato')
INSERT INTO Inventory VALUES (4, 'Fruit', 'Blueberry')
INSERT INTO Inventory VALUES (4, 'Fruit', 'Raspberry')
INSERT INTO Inventory VALUES (4, 'Vegetable', 'Tomato')
INSERT INTO Inventory VALUES (4, 'Vegetable', 'Cucumber')
INSERT INTO Inventory VALUES (4, 'Meat', 'Beef')
INSERT INTO Inventory VALUES (4, 'Poultry', 'Chicken')
{% endhighlight %}

In order to retrieve an exhausted list of products, we normally use:

{% highlight sql %}
SELECT * FROM Inventory
{% endhighlight %}

And the result is pretty straight forward.

{% highlight yaml %}
ClientId Category Product
1 Fruit Banana
1 Fruit Apple
1 Fruit Orange
1 Fruit Kiwi
1 Fruit Pinapple
1 Fruit Mango
1 Fruit Grape
2 Fruit Acerola
2 Fruit Coconut
2 Fruit Watermelon
2 Vegetable Cabbage
2 Vegetable Carrot
2 Vegetable Lettuce
2 Vegetable Onion
2 Meat Pork
2 Meat Lamb
3 Fruit Lemon
3 Fruit Straberry
3 Vegetable Pea
3 Vegetable Corn
3 Vegetable Broccoli
3 Vegetable Potato
4 Fruit Blueberry
4 Fruit Raspberry
4 Vegetable Tomato
4 Vegetable Cucumber
4 Meat Beef
4 Poultry Chicken
{% endhighlight %}

But it gets harder and harder to read and understand because of the duplicated category information is not properly categorised. In which case, we can use keywords like `where` and `order by` to filter the result. Before you do anything, wouldn't it be better if data rows to be displayed as data column names in the result, or a [crosstab](http://en.wikipedia.org/wiki/Crosstab) report?

That's how the `Pivot` function becomes useful:

{% highlight sql %}
SELECT * FROM Inventory PIVOT (COUNT(Product) FOR Category IN ([Fruit], [Vegetable], [Meat], [Poultry])) AS pvt
{% endhighlight %}

Inside the [PIVOT](http://technet.microsoft.com/en-us/library/ms177410.aspx) function, it performs action on 'Product' column, and categorises the result according to 'Category' data columns. And here's what you get:

{% highlight yaml %}
ClientId Fruit Vegetable Meat Poultry
1 7 0 0 0
2 3 4 2 0
3 2 4 0 0
4 2 2 1 1
{% endhighlight %}

You might already notice the hard coded part of query, and that's really annoying to use when automate the whole query process, and actually make it work in SSRS.

In order to tackle this problem, here is how you can do pivot dynamically:

{% highlight sql %}
DECLARE @cols VARCHAR(1000)
SET @cols = STUFF((SELECT DISTINCT '],['+ Category FROM Inventory FOR XML PATH('')),1,2,'') + ']'
DECLARE @query VARCHAR(1000)
SET @query = 'SELECT * FROM Inventory PIVOT (COUNT(Product) FOR Category IN ('+ @cols +')) AS pvt'
EXECUTE (@query)
{% endhighlight %}

For more information about the pivot syntax, here is what options you can use:

{% highlight sql %}
SELECT <non-pivoted column>,
    [first pivoted column] AS <column name>,
    [second pivoted column] AS <column name>,
    ...
    [last pivoted column] AS <column name>
FROM
    (<SELECT query that produces the data>)
    AS <alias for the source query>
PIVOT
(
    <aggregation function>(<column being aggregated>)
FOR
[<column that contains the values that will become column headers>]
    IN ( [first pivoted column], [second pivoted column],
    ... [last pivoted column])
) AS <alias for the pivot table>
<optional ORDER BY clause>;
{% endhighlight %}
