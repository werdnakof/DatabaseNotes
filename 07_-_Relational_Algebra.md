# Relational Algebra

* **relation** can be viewed as a table with columns and rows
* **attribute** is a named column of a **relation**
* **domain** is the set of allowable values for an **attribute**
* **tuple** is a row of a **relation** (ordered collection of values)
* **degree** of a **relation** is the number of **attributes** it contains
* **cardinality** of a **relation** is the number of **tuples** it contains

### Properties of relations

* Each **relation** in a relational database schema has a distinct name
* Each **attribute** of each **tuple** in a **relation** contains a single atomic value
* Each **attribute** has a distinct name
* The values of an **attribute** are all from the same domain
* Each **tuple** is distinct; there are no duplicate **tuples**
* The order of the **attributes** in a **tuple** is insignificant
* The order of the **tuples** in a **relation** is insignificant

### Relational Operations

**Selection (σ$\sigma$)**
* Consists of atomic predicates, which take the forms: **a $ b** or **a %$ v**
* **a** and **b** are **attributes**
* **$** is binary operation: <=, <, =, >, >=
* **v** is a constant value
* Predicates can be combined via logical symbols
    * ¬ (negation) i.e. ¬A is true if and only if A is false.
    * ∧ (and) 
    * ∨ (or) (e.g. _age > 18 ∨ gender == female_)

**Projection (π)**
* Consists of **attribute** extraction
* SQL: _SELECT name, age, gender FROM ..._

**Renaming (ρ)**
* Rename an attribute, doesn't change shape of a table

**Set Operations**
* Union of two operation must share the same attributes
* Difference of two operation must have same same number of attributes, with the same domains
* Intersection

**Cartesian Product**
* The cartesian product of two relations R and S contains the concatenation of every tuple in R with every tuple in S
* E.g.
    * R has tuples a, b
    * S has tuples 1, 2, 3
    * R x S has tuples a1, a2, a3, b1, b2, b3

**Theta Join ⋈**

E.g.
Table R

A|B
:---:|:---:
a|1
b|2

Table S


B|C
:---:|:---:
1|x
1|y
3|z

Table R ⋈ S by R.B = S.B

A|B|C
:---:|:---:|:---:
a|1|x
a|1|y

**Join Predicates**
* same as theta join excepts joining the predicates of the tables

**Equi Joins** 
* An equijoin is a theta join in which 
  the only predicate operator used is equality (=)
* SELECT * FROM table1 JOIN table2 ON (table1.id = table2.id)
* Using above as example,equijoin R and S

A|B|C
:---:|:---:|:---:
a|1|x
a|1|y

**Natural Joins**
* A natural join is an equijoin over all the common attributes 
  in a column; occurrence of each common attribute is then removed from the resulting relation
* SELECT * FROM table1 NATURAL JOIN table2
* Using above as example, natural join R and S

A|C
:---:|:---:
a|x
a|y

**Outer Join / Semi Join (left ⋊) (right ⋉)** 

The (left) outer join of two relations R and S is a join which 
also includes tuples from R which do not have
corresponding tuples in S; missing values are set to null

R ⋊ S

A|B|C
:---:|:---:|:---:
a|1|x
a|1|y
b|2|

**Full outer join (⟗)**

R ⟗ S

A|B|C
:---:|:---:|:---:
a|1|x
a|1|y
b|2|_
_ |3|z

**Antijoin (▷)**

A antijoin of two relations R and S contains all the tuples of R 
which are in the join of R and S with predicate F

R ▷ S

A|B
:---:|:---:
a|1

S ▷ R

B|C
:---:|:---:
1|x
1|y



**Mapping SQL to the Relational Algebra Examples**

SQL|Relational
:---:|:---:
SELECT * FROM {table} WHERE {pred} | σ_pred(table)
SELECT {cols} FROM {table} | π_cols(table)
SELECT {cols} FROM {table} WHERE {pred} | π_cols(σ_pred(table))
SELECT {cols} as <cogs> FROM {table} WHERE {pred} | ρ_cogs(π_cols(σ_pred(table)))

_With Context_

> SELECT FNAME, LNAME, ADDRESS FROM EMPLOYEE, DEPARTMENT WHERE DNAME=‘Research’ AND DNUMBER=DNO

will be

> ResDept &larr; &pi;__dname=research_(Department)

> DeptEmps &larr; ResDept ⋈__dumber=dno_(Employee)

> Result &larr; &pi;__fname,lname,address_(DeptEmps)

or in one line

> &pi;__fname,lname,address_( &pi;__dname=research_(Department) ⋈__dumber=dno_(Employee) )

## Relational Transformations

* Selection is commutative

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\sigma_p(\sigma_q(R))&space;=&space;\sigma_q(\sigma_p(R))" title="\small \sigma_p(\sigma_q(R)) = \sigma_q(\sigma_p(R))" />

* Only the last projection is required

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\prod_L\prod_M...\prod_N&space;(R)=&space;\prod_L(R)" title="\small \prod_L\prod_M...\prod_N (R)= \prod_L(R)" />

* Selection and projection are commutative if predicate only involves attributes in the projection list
    
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\pi_{A1,...Am}(\sigma_{p}(R))&space;=&space;\sigma_p(\pi_{A1,...Am}(R))" title="\small \pi_{A1,...Am}(\sigma_{p}(R)) = \sigma_p(\pi_{A1,...Am}(R))" />
    
* Cartesian product and theta join are commutative

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;R&space;\bowtie_p&space;S&space;=&space;S&space;\bowtie_p&space;R" title="\small R \bowtie_p S = S \bowtie_p R" />
    <br>
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;R&space;\times&space;S&space;=&space;S&space;\times&space;R" title="\small R \times S = S \times R" />
    
* Projection distributes over theta join,
  if projection lists can be divided into attributes of the relations being joined,
  and join condition only uses attributes from the projection list

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\prod_{L_1&space;\cup&space;L_2}(R&space;\bowtie_r&space;S)&space;=&space;\prod_{L_1}(R)&space;\bowtie_r&space;\prod_{L_2}(S)" title="\small \prod_{L_1 \cup L_2}(R \bowtie_r S) = \prod_{L_1}(R) \bowtie_r \prod_{L_2}(S)" />

* Set union and intersection are commutative

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;R&space;\cup&space;S&space;=&space;S&space;\cup&space;R" title="\small R \cup S = S \cup R" />
    <br>
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;R&space;\cap&space;S&space;=&space;S&space;\cap&space;R" title="\small R \cap S = S \cap R" />

* Selection distributes over set operations

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\sigma_p(R&space;\cup&space;S)&space;=&space;\sigma_p(R)&space;\cup&space;\sigma_p(S)" title="\small \sigma_p(R \cup S) = \sigma_p(R) \cup \sigma_p(S)" />
    <br>
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\sigma_p(R&space;\cap&space;S)&space;=&space;\sigma_p(R)&space;\cap&space;\sigma_p(S)" title="\small \sigma_p(R \cap S) = \sigma_p(R) \cap \sigma_p(S)" />
    <br>
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\sigma_p(R&space;-&space;S)&space;=&space;\sigma_p(R)&space;-&space;\sigma_p(S)" title="\small \sigma_p(R - S) = \sigma_p(R) - \sigma_p(S)" />

* Projection distributes over set union

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;\prod_L(R&space;\cup&space;S)&space;=&space;\prod_L(R)&space;\cup&space;\prod_L(S)" title="\small \prod_L(R \cup S) = \prod_L(R) \cup \prod_L(S)" />

* Associativity of theta join and cartesian product

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;(R&space;\bowtie&space;S)&space;\bowtie&space;T&space;=&space;R&space;\bowtie&space;(S&space;\bowtie&space;T)" title="\small (R \bowtie S) \bowtie T = R \bowtie (S \bowtie T)" />
    <br>
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;(R&space;\times&space;S)&space;\times&space;T&space;=&space;R&space;\times&space;(S&space;\times&space;T)" title="\small (R \times S) \times T = R \times (S \times T)" />

* Associativity of set union and intersection

    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;(R&space;\cup&space;S)&space;\cup&space;T&space;=&space;R&space;\cup&space;(S&space;\cup&space;T)" title="\small (R \cup S) \cup T = R \cup (S \cup T)" />
    <br>
    <img src="http://latex.codecogs.com/png.latex?\inline&space;\dpi{200}&space;\small&space;(R&space;\cap&space;S)&space;\cap&space;T&space;=&space;R&space;\cap&space;(S&space;\cap&space;T)" title="\small (R \cap S) \cap T = R \cap (S \cap T)" />
    


<!--stackedit_data:
eyJoaXN0b3J5IjpbODI0Mzg0NDE3XX0=
-->