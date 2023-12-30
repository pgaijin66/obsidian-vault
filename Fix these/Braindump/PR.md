
Good work. I have left few comments on the PR ;). Some code were repeated so i just skipped it assuming you would infer from comments prior.  All the comments given there could be generalized into following:

1. Return error with context. If its not a main function then return error.
3. Do not treat methods as functions. They are different. I know coming from language which uses OOP extensively , it might be a bit difficult to wrap but go is not OOP. Sure it uses similar naming conventions but its not same.
4. API responses code should be used as intended ( 202 vs 200). From what you mentioned you would need to use `202` instead of `200`
5. API should return error is there was an error. 4XX, 5XX etc
7. Some naming convention were a bit misleading. This leads to issues for people who are reading the code. `GetSlackApiClient` over `NewSlackClient`
8. Better follow Go style guide. Go extensively makes use of short hand form. Its good to stick with convention of language at hand. 
9. Re-use error variables. You are creating variables for each error, unless chaining, its good to just reuse them. This way you will fill in the call stack easily.
10. Use singleton pattern for config vars. At any moment there should only be one and only one instance of the variable.
11. Do not re-name package, this will make your code misleading. Better use different directory structure ( shown below ). I could see you added packages like `slack` which made you override the one you already had to `isslack` which is not want you wanted package to be.  `IsXXX` functions should be reserved for validation and verification functions. 
12.  Make application configurable by passing static values from config file. This makes it easier when you need to change any parameters and makes application readable.
13. Do not capitalize variables or methods unless you absolutely need them. Go uses `mixedCaps` . Unlike other language, it holds special value `thisCase` values are not exported where are `ThisCase` are exported through out the entire program. This mean any buggy code can call any exported function and create havoc. Follow principle of least privilege. Only export vars / functions when required.
14. Return error instead of `fmt.Print`. There are lot of `fmt.print` in the code. Replace them with `return` and pass error back to calling function.
15. Another issue i could see was packaging. I could see you faced this issue and had to rename the package. This happens a lot. I took the liberty to refactor the directory structure. See if you can incorporate it into following structure.
    
Other than this nice work. 