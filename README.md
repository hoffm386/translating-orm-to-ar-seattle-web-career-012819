# Whitespace and ActiveRecord `where`

I used this fork of the learn-co lesson to experiment with all of the different ways to invoke ActiveRecord's `where` method.  My final output was:

```+----+---------------------------------+----------------+---------------+----------+
|                                  Exact Matches                                   |
+----+---------------------------------+----------------+---------------+----------+
|    | Code                            | Selected Names | Syntax Error? | Crashes? |
+----+---------------------------------+----------------+---------------+----------+
| 1  | Dog.where(name: "Teddy")        | [Teddy]        |               |          |
| 2  | Dog.where (name: "Teddy")       |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
| 3  | Dog.where(:name => "Teddy")     | [Teddy]        |               |          |
| 4  | Dog.where (:name => "Teddy")    |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
| 5  | Dog.where("name":"Teddy")       | [Teddy]        |               |          |
| 6  | Dog.where("name": "Teddy")      | [Teddy]        |               |          |
| 7  | Dog.where ("name":"Teddy")      |                | Yes           |          |
| 8  | Dog.where("name" :"Teddy")      |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
| 9  | Dog.where("name" => "Teddy")    | [Teddy]        |               |          |
| 10 | Dog.where ("name" => "Teddy")   |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
| 11 | Dog.where("name", "Teddy")      | []             |               |          |
| 12 | Dog.where ("name", "Teddy")     |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
| 13 | Dog.where({name: "Teddy"})      | [Teddy]        |               |          |
| 14 | Dog.where ({name: "Teddy"})     |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
| 15 | Dog.where({:name => "Teddy"})   | [Teddy]        |               |          |
| 16 | Dog.where ({:name => "Teddy"})  | [Teddy]        |               |          |
+----+---------------------------------+----------------+---------------+----------+
| 17 | Dog.where({"name" => "Teddy"})  | [Teddy]        |               |          |
| 18 | Dog.where ({"name" => "Teddy"}) | [Teddy]        |               |          |
+----+---------------------------------+----------------+---------------+----------+
| 19 | Dog.where{name: "Teddy"}        |                | Yes           |          |
| 20 | Dog.where{:name => "Teddy"}     |                | Yes           |          |
| 21 | Dog.where{"name" => "Teddy"}    |                | Yes           |          |
| 22 | Dog.where {name: "Teddy"}       |                | Yes           |          |
| 23 | Dog.where {:name => "Teddy"}    |                | Yes           |          |
| 24 | Dog.where {"name" => "Teddy"}   |                | Yes           |          |
+----+---------------------------------+----------------+---------------+----------+
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
|                                                 LIKE (`query = "%d%"`)                                                  |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
|    | Code                                       | Selected Names | Syntax Error? | Crashes?                             |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
|               Pure String Condition (vulnerable to SQL injections)               |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 25 | Dog.where("name LIKE '#{query}'")          | [Teddy,Fido]   |               |                                      |
| 26 | Dog.where ("name LIKE '#{query}'")         | [Teddy,Fido]   |               |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
|                                 Array Condition                                  |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 27 | Dog.where("name LIKE ?", "#{query}")       | [Teddy,Fido]   |               |                                      |
| 28 | Dog.where ("name LIKE ?", "#{query}")      |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
|                              Placeholder Condition                               |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 29 | Dog.where("name LIKE :x", x: query)        | [Teddy,Fido]   |               |                                      |
| 30 | Dog.where ("name LIKE :x", x: query)       |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 31 | Dog.where("name LIKE :x", :x => query)     | [Teddy,Fido]   |               |                                      |
| 32 | Dog.where ("name LIKE :x", :x => query)    |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 33 | Dog.where("name LIKE :x", "x" => query)    |                |               | missing value for :x in name LIKE :x |
| 34 | Dog.where ("name LIKE :x", "x" => query)   |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 35 | Dog.where("name LIKE :x", {x: query})      | [Teddy,Fido]   |               |                                      |
| 36 | Dog.where ("name LIKE :x", {x: query})     |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 37 | Dog.where("name LIKE :x", {:x => query})   | [Teddy,Fido]   |               |                                      |
| 38 | Dog.where ("name LIKE :x", {:x => query})  |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 39 | Dog.where("name LIKE :x", {"x" => query})  |                |               | missing value for :x in name LIKE :x |
| 40 | Dog.where ("name LIKE :x", {"x" => query}) |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
|                         Weird things that don't work...                          |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
| 41 | Dog.where({"name LIKE '#{query}'"})        |                | Yes           |                                      |
| 42 | Dog.where({"name LIKE": query})            |                |               | no such column: dogs.name            |
| 43 | Dog.where({"name LIKE ?", "#{query}"})     |                | Yes           |                                      |
| 44 | Dog.where({"name LIKE :x", x: query})      |                | Yes           |                                      |
| 45 | Dog.where({"name LIKE :x", :x => query})   |                | Yes           |                                      |
+----+--------------------------------------------+----------------+---------------+--------------------------------------+
```

To print out this table in the terminal:
1. Clone the repository: `git clone https://github.com/hoffm386/translating-orm-to-ar-seattle-web-career-012819.git`
2. Navigate to the cloned directory
3. Run `bundle install`
4. Run `ruby bin/run.rb`

You can also go into bin/run.rb and un-comment the code to generate the errors described.
