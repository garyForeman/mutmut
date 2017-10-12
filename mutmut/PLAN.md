# Ideas and plans

1. Create a "mutant path" of every baron node visited. This path gets sent to the mutation function so we can make more complex matching rules. This should make `call_argument_mutation` radically simpler to implement. This is very close to `context.stack` already.
2. Use mutant path instead of a mutation index when reporting found mutants and when applying mutations. This will enable the user to work with mutants in any order while altering the code as the mutant path won't change as much.
3. Store a list of all killed mutants with their mutant paths.
4. Now we can make mutation testing radically faster by not rerunning tests for mutants that we already know are killed.