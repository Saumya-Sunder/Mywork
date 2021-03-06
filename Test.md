- [Introduction](#introduction)
- [Problem definition](#problem-definition)
- [Properties of uniform partition](#properties-of-uniform-partition)
- [Partition for symmetric FDT](#partition-for-symmetric-fdt)
- [Partition for asymmetric FDT](#partition-for-asymmetric-fdt)
  - [Mapping asymmetric tree to virtual symmetric tree](#mapping-asymmetric-tree-to-virtual-symmetric-tree)
  - [Parity-group to target mapping](#parity-group-to-target-mapping)
  - [Target-frame to parity group mapping](#target-frame-to-parity-group-mapping)
- [Characterizing the region of tolerable failures](#characterizing-the-region-of-tolerable-failures)
- [Algorithms](#algorithms)
- [References](#references)

Introduction
------------

Motr uses a parity declustered layout which stripes a file over
devices/targets in such a way that failure of any *`K`* devices, among
available *`P`* devices are tolerable. We aim to extend this fault
tolerance to failure domains other than a device (ie. controller,
enclosure, rack).

Problem definition
------------------

We represent a pool as a tree, with each level representing one failure
domain. Starting from the virtual root node "Pool", we get racks,
enclosures, controllers, and devices at subsequent levels of this tree.
[[1]](#1) This hierarchy of failure domains can be, if desirable, refined to
include drawers, midplanes, *etc*. Inclusion of new failure domain types
won't affect the design materially.

Consider Fig 1.0 representing a tree of failure domains. Assume that
each parity group has *`N`* data units, *`K`* parity units, and *`S`* spare
units. Our aim is to distribute the total *`G`*  = *`N`* + *`K`* + *`S`*
units across a pool, such that user supplied tolerance for each level of
tree is satisfied. Following definitions are useful for exploring the
solution.

**Def:** *Tolerance vector* associated with a failure domains tree (FDT)
of height $h$ is a vector of size $h$,$i^{th}$ member of which
represents the required tolerance associated with $i^{th}$level of the
FDT.

For the given values of $N$, $K$ and $S$ all tolerance vectors
associated with an FDT need not be feasible.

**Def:** For the given values of $N$, $K$ and $S$ we represent the set
of feasible tolerance vectors by $F_{v}(N\  + \ S,\ K)$.

**Def**: An FDT is symmetric if all nodes at the same level have same
number of children.

**Def:** An FDT that is not symmetric is asymmetric.

**Def:** A partition of a positive integer $n$, also called an integer
partition, is a way of writing $n$ as a sum of positive integers \[3\].

**Def:** Two partitions that differ only in order of their summands are
considered equivalent.

**Def:** For a given number $n$ a set of all partitions of $l$ parts is
denoted by $\text{Part}(n,\ l)$.

**Def:** A partition $d\  \in \text{Part}(n,\ l)$ is called *uniform* if
every summand
$s \in \{\text{floor}(\frac{n}{l}),\ \text{ceil}(\frac{n}{l})\}$.

**Note:** A uniform distribution can be generated by dividing $n$
equally (to integer limit) into $l$ parts, and distribute the remainder
$r\  = \ n\ \%\ l$ , into any of the $r$ from $l\ $parts.

**Def:** A set of uniform partitions is denoted by
$\text{UPart}(n,\ l)$.

**Def:** A partition that is not uniform is called a *skewed* partition.

**Def:** A two-tuple $(N,\ L)$ defines a *constraint over a partition*
$d$ from $d\  \in \text{Part}(n,\ l)$ if $N\  < \ n$ and $L\  < \ l$.

**Def:** A partition $d_{}$ is said to satisfy a constraint $(N,\ L)$ if
it does not have any $L$ sized subset, sum of whose elements exceeds
$N$.

Properties of uniform partition
-------------------------------

**Claim 1:** If a partition ${d\  \in \text{Part}(n,\ l)}_{}$ does not
satisfy a constraint $(N,\ L)$, then none of its equivalent partitions
would satisfy the constraint.

**Proof:** Obvious.

**Claim 2:** Let $d_{s}$ represent an equivalent partition of $d_{}^{}$,
summands of which are arranged in descending order. Then if
$u\  \in \text{UPart}(n,\ l)$ and
$v\  \in \text{Part}(n,\ l)\ \backslash\ \text{Up}\text{art}(n,\ l)$,
then-

$\sum_{j\  = \ 0}^{i}{}u_{s}\lbrack\ j\rbrack\  \leq \ \sum_{j\  = \ 0}^{i}{}v_{s}\lbrack\ j\ \rbrack$
, for any $0\  \leq i < l$.

ie. for any arbitrary $0\  \leq i < l$, sum of first $i$ elements of
$u_{s}$ is always less than or equal to that of $v_{s}$.

**Proof:**

Let $q\ \  = \ \text{floor}(n/l)$ and $r\  = \ n\ \%\ l$. For a uniform
partition exactly $r$ elements will have value $q\  + \ 1$ and remaining
elements will hold value $q$.

1.  Suppose assertion of the claim is false and hence there exists some
    $i'$ such that:
    $\sum_{j\  = \ 0}^{i'}{}u_{s}\lbrack\ j\ \rbrack\  > \ \sum_{j\  = \ 0}^{i'}{}v_{s}\lbrack\ j\ \rbrack$.

2.  if $u_{s}\lbrack i'\rbrack\ \  > \ v_{s}\lbrack i'\rbrack$

    a.  If $u_{s}\lbrack i'\rbrack\ \  = \ q$, then
        $u_{s}\lbrack i\rbrack\ \  > v_{s}\lbrack i\rbrack,\ \text{for}\ i'\  < i\  < \ l\ $as
        all further elements from $u_{s}\ $have value $q$.

    b.  If $u_{s}\lbrack i'\rbrack\ \  = \ q\  + \ 1$, then
        $u_{s}\lbrack i\rbrack\ \  \geq v_{s}\lbrack i\rbrack,\ \text{for}\ i'\  < i\  < \ l\ $,
        as $u_{s}\lbrack i\rbrack\  \in \{ q,\ q\  + \ 1\}$

    c.  In both the cases above sum of elements in skewed distribution
        will be less than that of uniform distribution, which is a
        contradiction.

3.  if $u_{s}\lbrack i'\rbrack\  \leq v_{s}\lbrack i'\rbrack$

    a.  This implies
        $v_{s}\lbrack j\rbrack\  \geq u_{s}\lbrack j\rbrack,\ \text{for}\ 0\  \leq j\  \leq i'\ $,
        as $u_{s}\lbrack j\rbrack\  \in \{ q,\ q\  + \ 1\}$ and
        $v_{s}\ $is a sorted list. This contradicts (1) above.

**Property 1:** If a uniform partition $u\  \in \text{UPart}(n,\ l)$
does not satisfy a constraint $(N,\ L)$, then no skewed partition $v$
can satisfy the constraint.

**Proof:**

Claim 1 allows us to consider $u_{s}$ and $v_{s}$ instead of $u$ and
$v$. By Claim 2 we have :

$\sum_{i = 0}^{L_{}}{}u_{s}\lbrack i\rbrack\  \leq \ \sum_{i = 0}^{L}{}v_{s}\lbrack i\rbrack$

Since it is given that
$N\  < \ \sum_{i = 0}^{L}{}u_{s}\lbrack i\rbrack\ $, a skewed partition
can not satisfy the given constraint.

**Property 2:** If $u\  \in \text{UPart}(n,\ l)$ and
$v\  \in \ \text{Part}(n,\ l)\ \backslash\text{less}\ \text{UPart}(n,\ l)$,
then a maximum value from $u$ is less than or equal to maximum value in
$v$. Formally,

> $\max_{n_{i}\  \in \ v}\ n_{i}\  \geq \ \max_{n_{j}\  \in u}\ n_{j}\ $.

**Proof:**

Follows immediately from Property 1, when $L$ is set to $1$.

Partition for symmetric FDT
---------------------------

Consider a symmetric FDT. If each node of tree distributes units
received from its parent uniformly among its children, then we achieve
the best possible tolerance with given parameters. This is so because if
a node distributes incoming units in skewed manner, then by Property 2
of uniform partition, the maximum value within its children (and hence
across the entire level of children) will increase. This might affect
the tolerance of entire level of children. Uniform partition helps
finding bounds on tolerance for each level. eg. If in a symmetric FDT we
have $R$ racks, $E$ enclosure within each rack, $C$ controllers within
each enclosure, and input parity group has
$G\  = \ N\  + \ S\  + \ K$units, then:

${\text{\ \ }K}_{R}\  \leq \ \frac{K}{\text{ceil}\left( \frac{G}{R} \right)\text{\ \ }}\text{\ \ \ \ \ \ \ \ }\text{\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ }(B_{1})$

$\text{\ \ }K_{E}\  \leq \ \frac{K}{\text{ceil}\left( \frac{1}{E}\text{ceil}\left( \frac{G}{R} \right)\text{\ \ } \right)\text{\ \ }}\text{\ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ }(B_{2})$

In general if we have a symmetric tree with number of children a node at
level $l$ has are $c_{l}$, then for a given level $L$ we have:

$K_{L}\  \leq \frac{K}{\text{ceil}\left( \frac{1}{c_{L}}\ \text{ceil}\ \left( \frac{1}{c_{L - 1}}\text{ceil}\left( \text{...}\frac{1}{c_{2}\ }\text{ceil}\left( \frac{G}{c_{1}} \right) \right) \right) \right)\ }\text{\ \ \ \ \ \ \ \ \ \ \ }(B)$

Partition for asymmetric FDT
----------------------------

Unlike symmetric FDT, uniform partition need not be a feasible in the
case of asymmetric FDT. For example in Fig 1.0, suppose each enclosure
in rack $r_{0}$ has two controllers except the enclosure $e_{0}$, which
has one controller. If each enclosure receives $q$ number of units (as a
result of uniform distribution at the level of racks), then all
controllers except that of $e_{0}$ will receive $\frac{q}{2}$ units from
a parity group. Such an imbalance might not guarantee the tolerance of
$K_{C}$ associated with controllers. This will require redistributing
some of the units from $e_{0}$ to other enclosures (from same or other
racks) leading to a skewed partition at the level of enclosures (and
possibly at the level of racks). The another problem this issue causes
is inability to pick an equivalent partition at a given level. Since all
equivalent partitions need not be supported at the level of a skewed
element (element causing asymmetry in FDT), a load imbalance gets
introduced.

In order to address this issue, we factor out a virtual symmetric tree
off a given asymmetric tree. The degree of each node at any level in
virtual tree is same as the degree of a least degree node at the same
level in the input asymmetric tree. Following example demonstrates the
case. In the example from Fig. 3 we assume that from enclosure level
onwards, the tree is symmetric. The virtual tree that we construct out
of this tree will have two racks each having only single enclosure.

The fault-tolerant permutation is generated on this virtual tree. All
input tolerance parameters (ie. $K_{R},\ K_{E},\ K_{C},$ and $K_{T}$)
are evaluated against the virtual tree. It can be noted that a virtual
tree formed in this manner will cause enclosure e~0~ to get filled
before other enclosures. This is so because it will be part of both
possible virtual trees, whereas other enclosures are part of only a
single virtual tree. We address this issue in a pragmatic way. We
exclude those levels from FDT for which tolerance cannot be satisfied.
This helps in reducing asymmetries to some extend. Eg, suppose
$N\  + \ S\  = 8$, $K\  = \ 2$, and suppose each enclosure has two
controllers in Fig. 3, then in a virtual tree from example in Fig, 3 the
tolerance can never be satisfied at the level of racks and enclosures.
If we eliminate rack, and enclosures (i.e., let
$K_{R}\  = \ 0,\ K_{E}\  = \ 0$), then we can distribute units uniformly
at the level of controllers. Since all controllers will get populated
uniformly the pool won't be left with any holes in the end.

###  Mapping asymmetric tree to virtual symmetric tree

The root will be identical in both trees. Let node $n_{}$be a member of
both symmetric as well as asymmetric tree. Let degree of $n$ in
asymmetric tree be $d_{A}(n)$ and that in symmetric tree be$d_{S}(n)$.
For creating a mapping between children of $n$ in both the trees, we
create a random permutation of its $d_{A}(n)\  - \ 1$ children and pick
the first $d_{S}(n)\  - \ 1$ of them. The randomized permutation will be
generated using the *tile-id*[^1], and *gfid* associated with the file.
Fig. 4 depicts the mapping scheme in detail.

### Parity-group to target mapping

The procedure here runs in two steps:

-   Map an input (parity_group, unit) using fault-tolerant permutation
    present in pool-version, to appropriate location in the virtual
    tile.

-   Map the virtual symmetric tree to the physical tree using the scheme
    discussed in the previous sub-section.

### 

### Target-frame to parity group mapping

If index of a target, and index of a frame on it is given then following
steps lead to the relevant parity-group and unit.

-   Calculate index of tile using the formula: tile_id = frame_index /
    tile-\>rows_nr.

-   Apply inverse of mappings between real tree and virtual tree, at
    each level of tree, starting from the eldest ancestor of given
    target, to get column index within a tile. This locates the target
    and frame in the fault-tolerant permutation.

-   Apply inverse of the fault-tolerant permutation and get the parity
    group and source associated with the frame.

![](media/image1.png){width="6.166666666666667in"
height="8.489583333333334in"}

**Fig. 4 Mapping for an arbitrary tile tile~k~ from skeleton/virtual
tree to real tree. The skeleton tile contains P targets whereas the real
tree contains R \>= P targets. For each target in skeleton tile, a
random permutation maps the ancestors of the target from skeleton tree
to real tree. The figure depicts how the target t~1~ form the skeleton
tree gets mapped to the target t~d~ from the real tree, after applying a
sequence of random permutations at each level of ancestors of t~1~.
Please note that a target is a logical representation of device private
to a file, and hence all files share mutually exclusive target space
\[2\].**

Characterizing the region of tolerable failures
-----------------------------------------------

Throughout the document, when we say that a level $l$ has a tolerance of
$K_{l}$, it implicitly indicates that $K_{l}\  + \ 1$ failures at level
$l$ would cause more than $K$ failures in some parity group. Thus if
level $1$ has $K_{1}$ failures and level $2$ has $K_{2}$ failures such
that affected failure domains from the second level are not children of
affected failure domains from the first level, then it implies that we
have more than $K$ failures in at least one parity group. Now suppose we
have $\ i_{1}\  < \ K_{1}$ failures at the first level and
$\ i_{2}\  < \ K_{2}\ $ failures at the second level. How would we know
if these failures are tolerable ? In this section we characterize the
region of all tolerable failure vectors (a term that will be soon
defined). It turns out that this region is a convex polytope.

**Notations:**

Let the height of failure domains tree be $D$. Consider a vector-space
$\mathfrak{R}^{D}$ over the field of real numbers $\mathfrak{R}$, each
dimension of which we map to one failure domain level. Let $K_{l}$
represent the maximum tolerable failures at level $l$.

**Def:** A *failure vector* $\  \in$ $\mathfrak{R}^{D}$ is a vector,
$l^{th}$ component of which represents the failures encountered at level
$l$.

**Def:** A failure vector is said to be *tolerable* if maximum number of
failures caused by it in any parity group is not more than $K$.

**Assumption A~1~**:

Though it is not possible to support fractional failures we assume its
feasibility in following section, as it helps in visualizing the locus
of tolerable failure vectors.

**Claim 3**: Under the assumption **A~1~**, the set of all tolerable
failure vectors is convex.

**Proof**:

Let $\Lambda^{*}$denote the set of all tolerable failure vectors. If
$\  \in \ \Lambda^{*}$, then for any $0\  < \ l\  \leq \ D$, let maximum
failures caused by $v_{l}$ in any parity group be $k_{\text{vl}}$.
Consider a map,

$s:\ \mathfrak{R}^{D}\mathfrak{\  \rightarrow \ R}$

$\  \rightarrow \ \sum_{l\  = \ 1}^{D}{}k_{\text{vl}}$

It is clear that $s$ is linear (and hence convex). Then by definition,

$\Lambda^{*}\  = \ s^{- 1}(\ \lbrack 0,\ K\rbrack)$

Since the region $\lbrack 0.\ K\rbrack$ is convex, and $\Lambda^{*}$ is
a linear pre-image of it, $\Lambda^{*}$ is convex.

It is clear that not all members of $\Lambda^{*}$are practically
tolerable as we can not deal with fractional failures. But all tolerable
failure vectors, that do not represent fractional failures will always
be members of $\Lambda^{*}$. A convex region is characterized by its
extreme points. The next claim helps in establishing at least $D$
extreme points of $\Lambda^{*}$.

**Def:** A vector $\  \in$ $\mathfrak{R}^{D}$ is called an *extreme
vector* if there exists $1\  \leq l \leq D$ such that:

$e_{i}\ \  = \ K_{l}$, if $i\  = \ l$,

> $e_{i}\  = \ 0$, otherwise.

We represent an extreme vector having non-zero component in $i^{th}$
direction as $$.

**Claim 4:** Let $\  \in$ $\mathfrak{R}^{D}$ be an input failure vector.
Let $\Lambda$ represent the convex polytope formed using extreme vectors
$$, $$, ..., $$. If $\ \  \in \Lambda$, then it is tolerable.

**Proof:**

Let $\lambda_{i\ }$'s be the non-negative scalars such that,

> $\  = \ \sum_{i\  = \ 1}^{D}{}\lambda_{i}$.

The maximum possible failures at the level of a parity group that this
vector could cause are: $\sum_{i\  = \ 1}^{D}{}\lambda_{i}K$, because
maximum possible failures any $$ would cause are $K$.

Since $$ is contained in the convex polytope of $$'s,
$\sum_{i\  = \ 1}^{D}{}\lambda_{i}\  \leq 1$. Hence total failures in
any parity group can not be more than $K$ units.

It is worth noting that the condition above though sufficient, is not
necessary for a failure vector to be tolerable. Eg. suppose $N\  = \ 8$,
$K\  = \ 5$, and $S\  = \ 5$. Assume that a pool has $9$ racks. Thus
using the uniform partitioning, each rack would receive $2$ units from a
parity group, and so maximum tolerable failures at the level of racks is
$K_{1}\  = \ 2$. Thus $K_{1}$ failures at the level of racks still
leaves some room for more failures at other levels, that could be
tolerated. Thus one can see that $\Lambda\  \subseteq \ \Lambda^{*}$.
Fig. 5 helps in visualising this fact. If for each level $l$, failure of
$K_{l}$ failure domains leads to *exactly* $K$ failures in at least one
parity group (and of course $K$ or lesser failures in other parity
groups), then $\Lambda = \Lambda^{*}$, and the condition from Claim 4
becomes sufficient as well as necessary.

A convex polytope is an intersection of closed half-spaces. A closed
half-space can be represented by an inequality of the form \[5\]:

$a_{11}\text{.\ }v_{1}\  + \ a_{12}.{\ v}_{2}\  + \ ...\  + \ a_{1n}\text{.\ }v_{n}\  \leq \ b_{1}$

An intersection of $m$ such closed half-spaces can be represented by a
matrix inequality:

$A\text{.\ }\  \leq \ $

where $A$ is an $m\  \times \ n$ matrix and $$ is an $m$-vector.

Once all $K_{l}$'s are known, we can compute and store $A$, and $$ for
$\Lambda$. Thus, determining the feasibility of tolerating input
failures reduces to a matrix-vector multiplication.

Algorithms 
----------

/\* Constructs a symmetric tree of failure domains using an asymmetric
tree. \*/

fdt_vsymm_tree_generate

Input:

1.  ${h(X)}_{}$ - depth of tree $X$.

2.  $T_{A}$ - asymmetric tree.

3.  Tolerance vector tol_vec\[1:depth($T_{A}$)\]. tol_vec\[$i$\]
    indicates the desired tolerance for depth $i$ in tree $T_{A}$.

Notations:

1.  $T_{S}$ - output, virtual symmetric tree.

2.  $l$ - current level at which algorithm is operating in both $T_{A}$
    and $T_{S}$.

3.  $c_{l}$ - number of children a least degree node from level $l$ has
    in $T_{A}$.

Procedure:

1.  Initialize $l$ to $0$, $T_{S}\  = \ \text{root}\ (T_{A})$.

2.  Calculate $c_{l}$ for $T_{A}$, and for each node in $T_{S}$ at level
    $l$, create $c_{l}$ number of children.

3.  $l\  + = \ 1$. If $l\  < \ h(T_{A})$ goto (2), else goto (4).

4.  Stop.

/\* Generates a fault-tolerant permutation to be applied to all tiles

from all files.

\*/

fdt_ft_perm_generate

Input:

1.  symmetric tree $T_{S}$.

2.  $N,\ K,\ S$ - Parity group parameters.

3.  tolerance vector tol_vec\[1:depth($T_{V}$)\]. tol_vec\[$i$\]
    indicates the desired tolerance for depth $i$ in tree $T_{V}$.

Notations:

1.  $G\  = \ N\  + \ K\  + S$.

2.  ${v(i)}_{}$ - vacant frames under failure domain $i$.

3.  $l$ - level at which algorithm is currently iterating.

4.  $P$- total targets present in $T_{V}$.

Procedure:

1.  Calculate $P$, and based on $N,\ K,\ S$, and $P$, calculate the tile
    dimensions in $T_{V}$.

2.  Initialize $l$ to $0$, and for each failure domain $i$, present in
    $T_{V}$, initialize $v(i)$ to number of rows in a tile.

3.  For each parity group in a tile, starting from the root of $T_{V}$,
    distribute units uniformly at each level. When a failure domain $i$,
    receives a unit, decrement $v(i)$ by one.

4.  Stop.

fd_pg2tgt_map

Input:

1.  $T_{A}$ - asymmetric FDT.

2.  $T_{V}$ - (virtual)symmetric FDT.

3.  gfid: gob index of file.

4.  tile_id: index of tile within a file.

5.  $N$, $K,\ S$, and $P$, the usual parity group parameters.

6.  $< \text{pg},\ u >$ - A two-tuple indicating parity group index, and
    unit index.

Procedure:

1.  Locate $< \text{pg},\ u >$ in the fault-tolerant permutation stored
    in pool-version. We denote this location by
    $\text{fp}\ ( < \text{pg},\ u > )$.

2.  Starting from the top of $T_{V}$, for each level, map failure
    domains associated with $\text{fp}\ ( < \text{pg},\ u > )$ to
    appropriate failure domains from $T_{A}$.

3.  Return \<target_id, frame_id\> from $T_{A}$.

fd_tgt2pg_map

Input:

1.  gfid: global index associated with a file to which the tile belongs.

2.  $T_{A}$- asymmetric tree of failure domains.

3.  \<target, frame\> in $T_{A}$.

Notation:

1.  $< \text{pg},\ u >$ - a two tuple indicating parity group and unit.

2.  tid - tile index

Procedure:

1.  Obtain tid using the frame index and number of rows per tile.

2.  Starting from the target, apply inverse of mappings between virtual
    symmetric tree and asymmetric tree, at each ancestor of the target.

3.  Apply the inverse of fault-tolerant permutation.

4.  Return $< pg,\ u >$.

References
----------

<a id="1">[1]</a> 
Please refer to [HLD of a parity de-clustering algorithm](https://docs.google.com/a/seagate.com/document/d/1THpmQZig__zkfh6CdiMgAfbH5BUv7NfhW0ZpxRhvYEU/edit) for the definition of a tile.

<a id="2">[2]</a> 
[Failure Domains description doc](https://docs.google.com/a/seagate.com/document/d/19IMkodSeWu-w-NooUh7EbDD5C416FYM7bhfVXSb_U-c/edit#heading=h.7vglgtla0sm1)

<a id="3">[3]</a> 
[Failure domains - Skeleton tree to real tree mapping](https://docs.google.com/a/seagate.com/file/d/0B6co5mpIf4sZUEc3NDFpbS1MY2s/edit)

<a id="4">[4]</a> 
[Pools in configuration schema description doc](https://docs.google.com/a/seagate.com/document/d/19IdRJBQLglVi0D8FxZ4cTF9G7QwRmm1Wa9YhbetO5qA/edit)

<a id="5">[5]</a> 
[Wiki link for "Partition number theory"](http://en.wikipedia.org/wiki/Partition_(number_theory))

<a id="6">[6]</a> 
[Wiki link for "Convex Polytopes"](http://en.wikipedia.org/wiki/Convex_polytope)


