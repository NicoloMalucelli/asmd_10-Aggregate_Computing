# Aggregate Computing

## Variations to perform in Program

### case 9: branch vs mux

Branch acts in the same way as an if, at least from the point of view of the single agent (even thought there are complication from the point of view of the system), that means that 
as first thing the condition is evaluated and then, depending on the result, one branch is executed rather than the other. For this reason, when we set sense1 to true in a certain area,
we can see the counter increasing from zero up to 1000, and if we set sense1 back to 0 and we repeat the process, the counter restarts from the beginning.

```
branch(sense1)(rep(0){x => Math.min(x+1, 1_000)})(0)
```

When we use mux, each node evaluates at each step both the branches, therefore the counter is increased at each step no matter the value of the condition. Differently from before, if we set
sense1 to false and to true again, we won't see the counter restarting from zero because the instruction `rep(0){x => Math.min(x+1, 1_000)` is always executed even though sense1 is false.

```
mux(sense1)(rep(0){x => Math.min(x+1, 1_000)})(0)
```

### case 12:  Scala interoperability

In order to collect all the neighbours ids in a set, we use the foldhoodplus operator. The default value is en empty Set and we ask to each one of our neighbours their id. Then we add together
all the ids using the shown accumulator function.

```
foldhoodPlus(Set())((acc, v) => acc ++ v)(Set(nbr(mid())))
```

### case 8:  experiment with pairs

We use minHoodPlus to find the closest neighbour, then we take the second element of the tuple to retrieve the id of that neighbour. 

```
minHoodPlus((nbrRange, nbr(mid())))._2
```

### case 14: understand gossiping

The initial value of rep is setted to the node's id, then at each step, the max x between the node's and the agent's ones is taken as new x.

```
rep(mid()){x => maxHood(nbr(x))}
```

### case 16

Is it very similar to a normal gradient function, but if sense2 is true in a node, the range is multiplied by a factor of 5.

```
rep[Double](Double.MaxValue){x => mux[Double](sense1){0.0}{minHoodPlus(nbr{x}+mux(sense2){nbrRange*5}{nbrRange})}}
```

### partitions

This function partionates nodes depending on the closest source. Sources are those nodes whose ids are contained in the partitions' set.

```
private def partition(partitions: Set[Int]) = 
    rep[(Double, Int)]((Double.MaxValue, Int.MaxValue)){x =>
      mux[(Double, Int)](partitions contains mid())
        {(0.0, mid())}
        {minHoodPlus(nbr{x._1} + nbrRange, nbr{x._2})}
    }
```

