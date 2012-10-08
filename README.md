# Bash lambda

Real lambda support for bash (a functionally complete hack):

```
# Load the library into your shell (this can go into .bashrc):
$ . bash-lambda

# Start allocating closures and stuff:
$ add=$(fn x y 'echo $((x + y))')
$ map $(partial $add 5) $(list 1 2 3 4)
6
7
8
9
$ iterate $(partial $add 2) 3 | take 4
3
5
7
9
$ sum_list=$(partial reduce $add 0)
$ $sum_list $(list 1 2 3)
6
$ rand_int=$(fn 'echo $RANDOM')
$ repeatedly rand_int 100 | $sum_list
1566864

$ our_numbers=$(list $(repeatedly rand_int 100))

# Two ways to take an average. One is to close over the anonymous function
# $sum_list:
$ average=$(fn xs "echo \$((\$($sum_list \$xs) / \$(wc -l < \$xs)))")
$ $average $our_numbers
14927

# The other is to make the 'average' function visible within closures by giving
# it a name. While we're at it, let's do the same for sum_list; that way we
# won't need to close over $sum_list and escape a bunch of variables.
$ def sum-list $sum_list
$ defn average xs 'echo $(($(sum-list $xs) / $(wc -l < $xs)))'

# Real functions don't need to be dereferenced, since they aren't variables:
$ average $our_numbers
14927

# Save a function or value for later:
$ cp $our_numbers foo
$ cp $average bar       # Doesn't always work; depends on other fns
$ ./bar foo
```
