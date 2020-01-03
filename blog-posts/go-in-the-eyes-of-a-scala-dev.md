## Pro

- only one kind of loop
- compilation speed
- CSP is much better than actors
- doesn't compile when any var unused

## Con

- really missing expression-based syntax (Go doesn't even have the ternary operator `<cond> ? <a> : <b>`)
- simplicity of the language leads to loss of power in creating abstract interfaces
- unsophisticated type system leads to "dynamic typing" in practice (`interface{}`)
- <https://stackoverflow.com/questions/19979178/what-is-the-idiomatic-go-equivalent-of-cs-ternary-operator>
- <https://stackoverflow.com/questions/10485743/contains-method-for-a-slice>

## imperative vs functional

- it's possible to work around shared state and mutability. you just need to learn the good patterns how to do it.
- functional programming has a lot of concepts to learn as well. and to learn those you also learn in which situation mutable state is badly used. at this point you're probably able to avoid bad use of mutable state anyway.
- nevertheless, fp "prevents" bad use of mutable state altogether
